---
title: "Example: Stateless application mirror"
date: 2022-04-14T15:18:35-06:00
draft: true
---
## Stateless application mirror
This tutorial demonstrates how to mirror a simple stateless PHP Guestbook application using Crane.

### Roadmap
- Deploy Guestbook application in “source” cluster.
- Extract resources from “source” cluster using Crane Export.
- Generate transformations to prepare manifests for “destination” cluster using Crane Transform.
- Apply transformations using Crane Apply.
- Apply application manifests into “destination” cluster.

### Before You Begin
You will need a “source” and “destination” Kubernetes cluster. You can create an environment in minikube with:
```
wget "https://raw.githubusercontent.com/konveyor/crane/main/hack/minikube-clusters-start.sh"
chmod +x minikube-clusters-start.sh
./minikube-clusters-start.sh
```
or kind with:
```
wget "https://raw.githubusercontent.com/konveyor/crane-runner/main/hack/kind-up.sh"
chmod +x kind-up.sh
./kind-up.sh
```
You will also need to install crane. See the [Installation Guide](https://crane-docs.konveyor.io/content/getting-started/installation/).

**NOTE:** Before proceeding, it may be helpful to read through [Kubernetes' documentation on accessing multiple clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). The remainder of this document references a src and dest context that refer to clusters you would have instantiated using our minikube startup scripts. If you bring your own environment, or use kind (kind-src and kind-dest), you will need modify the commands below to reference the correct cluster context.

### Deploy Guestbook Application in “source” Cluster
You will be deploying [Kubernetes' stateless guestbook application](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/) modified here to be consumable via [Kustomize](https://kustomize.io/). The guestbook application consisists of:

- redis leader deployment and service
- redis follower deployment and service
- guestbook front-end deployment and service
```
kubectl --context src create namespace guestbook
kubectl --context src --namespace guestbook apply -k github.com/konveyor/crane-runner/examples/resources/guestbook
kubectl --context src --namespace guestbook wait --for=condition=ready pod --selector=app=guestbook --timeout=180s
```
#### Optional

Forward localhost traffic to the frontend of the guestbook application:
```
kubectl --context src --namespace guestbook port-forward svc/frontend 8080:80
Now you should be able to access the guestbook from the browser of your choice via localhost:8080.
```
### Extract Resources From “source” Cluster
Crane’s `export`command is how you extract all of the resources you want from the “source” cluster.
```
crane export --context src --namespace guestbook
```
Make sure everything is working as expected by checking the export directory, it should look something like:
```
$ tree -a export
export
├── failures
│   └── guestbook
└── resources
    └── guestbook
        ├── ConfigMap_guestbook_kube-root-ca.crt.yaml
        ├── Deployment_guestbook_frontend.yaml
        ├── Deployment_guestbook_redis-master.yaml
        ├── Deployment_guestbook_redis-slave.yaml
        ├── Endpoints_guestbook_frontend.yaml
        ├── Endpoints_guestbook_redis-master.yaml
        ├── Endpoints_guestbook_redis-slave.yaml
        ├── EndpointSlice_guestbook_frontend-bkqbs.yaml
        ├── EndpointSlice_guestbook_redis-master-hxr5k.yaml
        ├── EndpointSlice_guestbook_redis-slave-8wt7z.yaml
        ├── Pod_guestbook_frontend-5fd859dcf6-5nvbm.yaml
        ├── Pod_guestbook_frontend-5fd859dcf6-j8w94.yaml
        ├── Pod_guestbook_frontend-5fd859dcf6-s9x8p.yaml
        ├── Pod_guestbook_redis-master-55d9747c6c-6f9bz.yaml
        ├── Pod_guestbook_redis-slave-5c6b4c5b47-jnrsr.yaml
        ├── Pod_guestbook_redis-slave-5c6b4c5b47-xz776.yaml
        ├── ReplicaSet_guestbook_frontend-5fd859dcf6.yaml
        ├── ReplicaSet_guestbook_redis-master-55d9747c6c.yaml
        ├── ReplicaSet_guestbook_redis-slave-5c6b4c5b47.yaml
        ├── Secret_guestbook_default-token-5vsrb.yaml
        ├── ServiceAccount_guestbook_default.yaml
        ├── Service_guestbook_frontend.yaml
        ├── Service_guestbook_redis-master.yaml
        └── Service_guestbook_redis-slave.yaml

4 directories, 24 files
```
**NOTE:** What’s happening here? crane export is using a discovery client to see all of the API resources in the specified namespace of the designated cluster and outputing them to disk in YAML form. This allows you to migrate your workloads in a non-destructive way. Going forward you’ll be working against these manifests on disk without impacting the active resources in the “source” cluster.

### Generate Transformations
Crane’s `transform` command generates tranformations in the form of JSON patches and stores those patches on disk in `transform` directory (unless overridden via `--transform-dir`).
```
crane transform
```
And verify that it behaved as we expect by checking out the transform directory:
```
$ tree -a transform
transform
└── resources
    └── guestbook
        ├── transform-ConfigMap_guestbook_kube-root-ca.crt.yaml
        ├── transform-Deployment_guestbook_frontend.yaml
        ├── transform-Deployment_guestbook_redis-master.yaml
        ├── transform-Deployment_guestbook_redis-slave.yaml
        ├── transform-Secret_guestbook_default-token-5vsrb.yaml
        ├── transform-ServiceAccount_guestbook_default.yaml
        ├── transform-Service_guestbook_frontend.yaml
        ├── transform-Service_guestbook_redis-master.yaml
        ├── transform-Service_guestbook_redis-slave.yaml
        ├── .wh.Endpoints_guestbook_frontend.yaml
        ├── .wh.Endpoints_guestbook_redis-master.yaml
        ├── .wh.Endpoints_guestbook_redis-slave.yaml
        ├── .wh.EndpointSlice_guestbook_frontend-bkqbs.yaml
        ├── .wh.EndpointSlice_guestbook_redis-master-hxr5k.yaml
        ├── .wh.EndpointSlice_guestbook_redis-slave-8wt7z.yaml
        ├── .wh.Pod_guestbook_frontend-5fd859dcf6-5nvbm.yaml
        ├── .wh.Pod_guestbook_frontend-5fd859dcf6-j8w94.yaml
        ├── .wh.Pod_guestbook_frontend-5fd859dcf6-s9x8p.yaml
        ├── .wh.Pod_guestbook_redis-master-55d9747c6c-6f9bz.yaml
        ├── .wh.Pod_guestbook_redis-slave-5c6b4c5b47-jnrsr.yaml
        ├── .wh.Pod_guestbook_redis-slave-5c6b4c5b47-xz776.yaml
        ├── .wh.ReplicaSet_guestbook_frontend-5fd859dcf6.yaml
        ├── .wh.ReplicaSet_guestbook_redis-master-55d9747c6c.yaml
        └── .wh.ReplicaSet_guestbook_redis-slave-5c6b4c5b47.yaml

2 directories, 24 files
```
**NOTE:** What `crane transform` is doing, is iterating through the configured plugins and running them against the exported resources from the prevoius step. You can see which plugins are configured with `crane transform list-plugins` and you can see optional arguments to those plugins with `crane transform optionals`.

Now is a great time to explore what plugins you can configure with `crane plugin-manager list`, install one, and maybe customize the exported resources with something like:
```
crane transform --optional-flags=<span style**=**"color: red;">"add-annotations=custom-crane-annotation=foo"</span>
```
If the flags start to get tough to manage via the command-line, you can also specify a `--flags-file` that might look something like:
```
debug: false
export-dir: myExport
transform-dir: myTransform
output-dir: myOutput
optional-flags:
  add-annotations:
    custom-crane-annotation: "foo"
```
### Apply Transformations
Crane’s `apply` command takes the exported resources + transformations saved as JSON patches and renders the results as YAML files that you should be able to apply to another cluster as is.
```
crane apply
```
**NOTE:** To better understand what crane is doing during this step, go back and look at one of the transformations created in the last step:
```
$ cat transform/resources/guestbook/transform-Deployment_guestbook_frontend.yaml
[{"op":"remove","path":"/metadata/uid"},{"op":"remove","path":"/metadata/resourceVersion"},{"op":"remove","path":"/metadata/creationTimestamp"},{"op":"remove","path":"/metadata/generation"},{"op":"remove","path":"/status"}]%
```
So when crane “apply"s the transformations for the guestbook frontend, it will be executing a handful of JSON patches that:

1. Remove the UID
2. Remove the resourceVersion
3. Remove the creationTimestamp
4. Remove the generation field
5. Remove the status

All of this data are leftovers from the “source” cluster and we are removing them from the final manifests to make them applicable to the “destination” cluster. At this stage, these resources are effectively cluster agnostic ready to be `kubectl apply`ed to the cluster of your choosing **OR** placed under version control to be later managed by GitOps + CI/CD Pipelines. If you were to specify optional flags, you would likely see additional patches that may add/remove/replace additional fields on the resources previously exported.

### Apply Manifests to “destination” Cluster
Now that you have manifests prepared for the “destination” cluster, you can apply them using `kubectl` directly:
```
kubectl --context dest create namespace guestbook
kubectl --context dest --namespace guestbook --recursive=true apply -f ./output
```
**NOTE:** If you wanted to change the namespace, you could use something like [Kustomize](https://kustomize.io/), the Kubernetes native + template-free way to manage application configuration. For example:
```
cd output/resources/guestbook
kustomize init --namespace custom-guestbook --autodetect
```
Would give you a `kustomization.yaml` that looks like:
```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- ConfigMap_guestbook_kube-root-ca.crt.yaml
- Deployment_guestbook_frontend.yaml
- Deployment_guestbook_redis-master.yaml
- Deployment_guestbook_redis-slave.yaml
- Secret_guestbook_default-token-5vsrb.yaml
- ServiceAccount_guestbook_default.yaml
- Service_guestbook_frontend.yaml
- Service_guestbook_redis-master.yaml
- Service_guestbook_redis-slave.yaml
namespace: custom-guestbook
```
And you could apply it (after creating the custom-guestbook namespace of course) with `kubectl apply -k`.

### What’s Next
Read more about [Crane](https://github.com/konveyor/crane).
Check out [Crane Runner](https://github.com/konveyor/crane) where you can perform application migrations inside Kubernetes.

### Cleanup
kubectl --context dest delete namespace guestbook
