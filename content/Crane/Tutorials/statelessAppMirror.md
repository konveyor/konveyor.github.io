---
title: "Stateless application mirror"
date: 2022-04-14T15:18:35-06:00
draft: false
---
This tutorial is an example of how to mirror a simple, stateless [PHP Guestbook application](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/) using Crane.

**Roadmap**
- **1. Deploy** the Guestbook application in the source cluster.
- **2. Extract** resources from the source cluster using Crane Export.
- **3. Transform** resources to prepare manifests for the destination cluster using Crane Transform.
- **4. Apply** the transformations using Crane Apply and apply application manifests to the destination cluster.

**Prerequisites**

1. Create a source and destination Kubernetes cluster environment in minikube or Kind:

- **minikube**
```
wget "https://raw.githubusercontent.com/konveyor/crane/main/hack/minikube-clusters-start.sh"
chmod +x minikube-clusters-start.sh./minikube-clusters-start.sh
```
- **Kind**
```
wget "https://raw.githubusercontent.com/konveyor/crane-runner/main/hack/kind-up.sh"
chmod +x kind-up.sh./kind-up.sh
```
2. Install Crane using the [Installation Guide](https://crane-docs.konveyor.io/content/getting-started/installation/).

> **Important:** Read through [Kubernetes' documentation on accessing multiple clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). This document references **src** and **dest** contexts that refer to the clusters created using the minikube startup scripts above.
When working in the home environment, or using Kind (`kind-src` and `kind-dest`), modify the commands below to reference the correct cluster context.

## Step 1. Deploy the Guestbook application in the source cluster

Deploy the [Kubernetes' stateless guestbook application](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/) and modify it to be consumable with [Kustomize](https://kustomize.io/) (Kubernetes native and template-free tool to manage application configuration). The guestbook application consists of:

- redis leader deployment and service
- redis follower deployment and service
- guestbook front-end deployment and service
```
kubectl --context src create namespace guestbook
kubectl --context src --namespace guestbook apply -k github.com/konveyor/crane-runner/examples/resources/guestbook
kubectl --context src --namespace guestbook wait --for=condition=ready pod --selector=app=guestbook --timeout=180s
```

**Optional**

Forward localhost traffic to the frontend of the Guestbook application to access Guestbook from a browser using `localhost:8080`:
```
kubectl --context src --namespace guestbook port-forward svc/frontend 8080:80
```

## Step 2. Extract from the source cluster

Crane’s `export` command extracts all of the specified resources from the “source” cluster.
```
crane export --context src --namespace guestbook
```
Check the `export` directory to verify it is working correctly. The directory should look similar to the example below:
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

Crane Export is using a discovery client to see all of the API resources in the specified namespace of the designated cluster and outputing them to the disk in YAML form. This allows workloads to migrate in a non-destructive way.

Going forward these manifests will be working on the disk without impacting the active resources in the “source” cluster.

## Step 3. Generate transformations
Crane’s `transform` command generates tranformations in the form of JSON patches and stores them on the disk in the transform directory (unless overridden using `--transform-dir`).

```
crane transform
```
Check the transform directory to verify the command worked correctly:
```
$ tree -a transform
```
The directory should look similar to the example below:
```
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
Crane Transform is iterating through the configured plugins and running them against the exported resources from the previous step. View which plugins are configured with Crane Transform list-plugins and optional arguments to those plugins with crane transform optionals.

Explore what plugins can be configured with the Crane plugin-manager list, install one, and customize the exported resources:
```
crane transform --optional-flags="add-annotations=custom-crane-annotation=foo"
```
If the flags get difficult to manage via the command-line, specify a `--flags-file` similar to the example below:
```
debug: false
export-dir: myExport
transform-dir: myTransform
output-dir: myOutput
optional-flags:
  add-annotations:
    custom-crane-annotation: "foo"
```

## 4. Apply transformations

The Crane `apply` command takes the exported resources and  transformations and renders the results as YAML files that can be applied to another cluster.
```
crane apply
```
Look at one of the transformations created in the last step to better understand the Apply command.
```
$ cat transform/resources/guestbook/transform-Deployment_guestbook_frontend.yaml
[{"op":"remove","path":"/metadata/uid"},{"op":"remove","path":"/metadata/resourceVersion"},{"op":"remove","path":"/metadata/creationTimestamp"},{"op":"remove","path":"/metadata/generation"},{"op":"remove","path":"/status"}]%
```
When crane applies the transformations for the Guestbook frontend it executes a handful of JSON patches that:

- Remove the UID
- Remove the resourceVersion
- Remove the creationTimestamp
- Remove the generation field
- Remove the status

The leftover data from the source cluster is removed from the final manifests to make them applicable to the destination cluster.

The resources are effectively cluster agnostic and ready to be kubectl applied to the chosen cluster or placed under version control to be later managed by GitOps and CI/CD pipelines.

> **Note:** Additional patches to add/remove/replace additional fields on the resources previously exported are available if optional flags are specified.

### Apply the manifests to the destination cluster

Apply the manifests prepared for the destination cluster using `kubectl` directly:
```
kubectl --context dest create namespace guestbook
kubectl --context dest --namespace guestbook --recursive=true apply -f ./output
```
> **Note:** To change the namespace, use [Kustomize](https://kustomize.io/).
```
cd output/resources/guestbook
kustomize init --namespace custom-guestbook --autodetect
```
The result is a kustomization.yaml similar to the example below:
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
After creating the custom-guestbook namespace, apply the kustomization.yaml with `kubectl apply -k`.

## Cleanup
```
kubectl --context dest delete namespace guestbook
```

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Crane/Tutorials/StatelessAppMirror.md)
