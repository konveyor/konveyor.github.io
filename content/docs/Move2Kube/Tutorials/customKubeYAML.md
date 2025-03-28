---
title: "Customize Kubernetes YAMLs to target specific clusters"
date: 2022-08-04T19:13:38-06:00
draft: false
---
Move2Kube already supports targeting across multiple clusters including: Kubernetes, Openshift, IBM-IKS, IBM-Openshift, Azure-EKS, Azure-AKS and GCP-GKS. There might be situations that require generating Kubernetes YAMLs to target a particular cluster. This tutorial shows how to use Konveyor Move2Kube to change the versions of existing Kubernetes resources to target a particular cluster. Move2Kube can also be customized to generate Kubernetes YAMLS deployable on a particular cluster.

**Prerequisites**

1. Install the Move2Kube CLI tool.

1. Install [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl).

1. Use the [kubernetes-to-kubernetes](https://github.com/konveyor/move2kube-demos/tree/main/samples/kubernetes-to-kubernetes) sample. This directory has some Kubernetes YAMLs that deploy a web app with multiple services. There are three services: a frontend website in PHP, a backend API in NodeJS, and a cache service using Redis.

**Procedure**

1. Collect data about the Kubernetes cluster using `move2kube collect`. Limit the collection to only cluster information using the `-a k8s` annotation flag.

> **Note:** Before running the below command, log in to the target cluster. To verify, run `kubectl get pods`.

```console
$ move2kube collect -a k8s
INFO[0000] Begin collection
INFO[0000] [*collector.ClusterCollector] Begin collection
INFO[0006] [*collector.ClusterCollector] Done   
INFO[0006] [*collector.ImagesCollector] Begin collection
INFO[0006] [*collector.ImagesCollector] Done
INFO[0006] Collection done  
INFO[0006] Collect Output in [/Users/user/m2k_collect]. Copy this directory into the source directory to be used for planning.
```

The data collected will be stored in a new directory called `./m2k_collect`.

```console
$ ls m2k_collect
cf
```

The `./m2k_collect/clusters` directory contains the YAML file which has the cluster application information including the buildpacks that are supported, the memory, the number of instances and the ports that are supported. If there are environment variables, it collects that information too. The name of the cluster can be found in the `metadata.name` file, and can be renamed in the YAML file.

* For this tutorial, the `move2kube collect` output YAML file which contains the cluster related information is stored [here](https://github.com/konveyor/move2kube-transformers/tree/main/custom-cluster-selector/clusters/my-kubernetes-cluster.yaml). The cluster `metadata.name` was renamed as `my-kubernetes-cluster` in the YAML file.

* Use a [custom configured version](https://github.com/konveyor/move2kube-transformers/tree/main/custom-cluster-selector) of the [clusterselector transformer](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers/kubernetes/clusterselector).

2. Download the [custom-cluster-selector](https://github.com/konveyor/move2kube-transformers/tree/main/custom-cluster-selector) transformer into the `customizations` sub-directory.

```console
$ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d custom-cluster-selector -r move2kube-transformers -o customizations
```

The `customizations/custom-cluster-selector/transformer.yaml` is the transformer configuration. There are two changes in the [custom-cluster-selector/transformer.yaml](https://github.com/konveyor/move2kube-transformers/tree/main/custom-cluster-selector/transformer.yaml) compared to the built-in [clusterselector/transformer.yaml](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers/kubernetes/clusterselector/transformer.yaml) :
* The name of the custom transformer is `CustomClusterSelector` (see `name` field in the `metadata` section).
* Specify an `override` section which is asking Move2Kube to disable the transformer named `ClusterSelector` if `CustomClusterSelector` transformer is present.

```console
  $ cat customizations/custom-cluster-selector/transformer.yaml
  apiVersion: move2kube.konveyor.io/v1alpha1
  kind: Transformer
  metadata:
    name: CustomClusterSelector
    labels:
      move2kube.konveyor.io/built-in: true
      move2kube.konveyor.io/kubernetesclusterselector: true
  spec:
    class: "ClusterSelectorTransformer"
    directoryDetect:
      levels: 0
    consumes:
      IR:
        merge: true
        mode: OnDemandPassThrough
      KubernetesOrgYamlsInSource:
        merge: false
        mode: OnDemandPassThrough
    produces:
      IR:
        disabled: false
      KubernetesOrgYamlsInSource:
        disabled: false
    override:
      matchLabels:
        move2kube.konveyor.io/name: ClusterSelector
```

To check what information `move2kube collect -a k8s` collected, view the content inside the [my-kubernetes-cluster.yaml](https://github.com/konveyor/move2kube-transformers/tree/main/custom-cluster-selector/clusters/my-kubernetes-cluster.yaml).

```console
$ cat customizations/custom-cluster-selector/clusters/my-kubernetes-cluster.yaml
```

```yaml
apiVersion: move2kube.konveyor.io/v1alpha1
kind: ClusterMetadata
metadata:
  name: my-kubernetes-cluster
spec:
  storageClasses:
    - default
    - ibmc-file-bronze
    - ibmc-file-bronze-gid
    - ibmc-file-custom
    - ibmc-file-gold
- ibmc-file-gold-gid
     - ibmc-file-retain-bronze
     - ibmc-file-retain-custom
     - ibmc-file-retain-gold
     - ibmc-file-retain-silver
     - ibmc-file-silver
     - ibmc-file-silver-gid
   apiKindVersionMap:
     APIService:
       - apiregistration.k8s.io/v1
     BGPConfiguration:
       - crd.projectcalico.org/v1
     BGPPeer:
       - crd.projectcalico.org/v1
     Binding:
       - v1
     BlockAffinity:
       - crd.projectcalico.org/v1
     CSIDriver:
       - storage.k8s.io/v1
     CSINode:
       - storage.k8s.io/v1
     CSIStorageCapacity:
       - storage.k8s.io/v1beta1
     CatalogSource:
       - operators.coreos.com/v1alpha1
     CertificateSigningRequest:
       - certificates.k8s.io/v1
     ClusterInformation:
       - crd.projectcalico.org/v1
     ClusterRole:
       - rbac.authorization.k8s.io/v1
     ClusterRoleBinding:
       - rbac.authorization.k8s.io/v1
     ClusterServiceVersion:
       - operators.coreos.com/v1alpha1
     ComponentStatus:
       - v1
     ConfigMap:
       - v1
     ControllerRevision:
       - apps/v1
     CronJob:
       - batch/v1
       - batch/v1beta1
     CustomResourceDefinition:
       - apiextensions.k8s.io/v1
     DaemonSet:
       - apps/v1
     Deployment:
       - apps/v1
     EndpointSlice:
       - discovery.k8s.io/v1
       - discovery.k8s.io/v1beta1
     Endpoints:
       - v1
     Event:
       - events.k8s.io/v1
       - events.k8s.io/v1beta1
       - v1
     Eviction:
       - v1
     FelixConfiguration:
       - crd.projectcalico.org/v1
     FlowSchema:
       - flowcontrol.apiserver.k8s.io/v1beta1
     GlobalNetworkPolicy:
       - crd.projectcalico.org/v1
     GlobalNetworkSet:
       - crd.projectcalico.org/v1
     GuestBook:
       - webapp.metamagical.dev/v1
     HorizontalPodAutoscaler:
       - autoscaling/v1
       - autoscaling/v2beta1
       - autoscaling/v2beta2
     HostEndpoint:
       - crd.projectcalico.org/v1
     IPAMBlock:
       - crd.projectcalico.org/v1
     IPAMConfig:
       - crd.projectcalico.org/v1
     IPAMHandle:
       - crd.projectcalico.org/v1
     IPPool:
       - crd.projectcalico.org/v1
     Ingress:
       - networking.k8s.io/v1
     IngressClass:
       - networking.k8s.io/v1
     InstallPlan:
       - operators.coreos.com/v1alpha1
     Job:
       - batch/v1
     KubeControllersConfiguration:
       - crd.projectcalico.org/v1
     Lease:
       - coordination.k8s.io/v1
     LimitRange:
       - v1
     LocalSubjectAccessReview:
       - authorization.k8s.io/v1
     MutatingWebhookConfiguration:
       - admissionregistration.k8s.io/v1
     Namespace:
       - v1
     NetworkPolicy:
       - networking.k8s.io/v1
       - crd.projectcalico.org/v1
     NetworkSet:
       - crd.projectcalico.org/v1
     Node:
       - v1
     NodeMetrics:
       - metrics.k8s.io/v1beta1
     NodeProxyOptions:
       - v1
     Operator:
       - operators.coreos.com/v1
     OperatorGroup:
       - operators.coreos.com/v1
       - operators.coreos.com/v1alpha2
     PersistentVolume:
       - v1
     PersistentVolumeClaim:
       - v1
     Pod:
       - v1
     PodAttachOptions:
       - v1
     PodDisruptionBudget:
       - policy/v1
       - policy/v1beta1
     PodExecOptions:
       - v1
     PodMetrics:
       - metrics.k8s.io/v1beta1
     PodPortForwardOptions:
       - v1
     PodProxyOptions:
       - v1
     PodSecurityPolicy:
       - policy/v1beta1
     PodTemplate:
       - v1
     PriorityClass:
       - scheduling.k8s.io/v1
     PriorityLevelConfiguration:
       - flowcontrol.apiserver.k8s.io/v1beta1
     RBACSync:
       - ibm.com/v1alpha1
     Redis:
       - webapp.metamagical.dev/v1
     ReplicaSet:
       - apps/v1
     ReplicationController:
       - v1
     ResourceQuota:
       - v1
     Role:
       - rbac.authorization.k8s.io/v1
     RoleBinding:
       - rbac.authorization.k8s.io/v1
     RuntimeClass:
       - node.k8s.io/v1
       - node.k8s.io/v1beta1
     Scale:
       - apps/v1
       - v1
     Secret:
       - v1
     SelfSubjectAccessReview:
       - authorization.k8s.io/v1
     SelfSubjectRulesReview:
       - authorization.k8s.io/v1
     Service:
       - v1
     ServiceAccount:
       - v1
     ServiceProxyOptions:
       - v1
     StatefulSet:
       - apps/v1
     StorageClass:
       - storage.k8s.io/v1
     SubjectAccessReview:
       - authorization.k8s.io/v1
     Subscription:
       - operators.coreos.com/v1alpha1
     TokenRequest:
       - v1
     TokenReview:
       - authentication.k8s.io/v1
     ValidatingWebhookConfiguration:
       - admissionregistration.k8s.io/v1
     VolumeAttachment:
       - storage.k8s.io/v1
     VolumeSnapshot:
       - snapshot.storage.k8s.io/v1
       - snapshot.storage.k8s.io/v1beta1
     VolumeSnapshotClass:
       - snapshot.storage.k8s.io/v1
       - snapshot.storage.k8s.io/v1beta1
     VolumeSnapshotContent:
       - snapshot.storage.k8s.io/v1
       - snapshot.storage.k8s.io/v1beta1
```

The [my-kubernetes-cluster.yaml](https://github.com/konveyor/move2kube-transformers/tree/main/custom-cluster-selector/clusters/my-kubernetes-cluster.yaml) has information about the target cluster under the `spec` field, including the `storageClasses`, `Deployment`, `Service`, `Ingress` and `NetworkPolicy` versions supported by the target cluster. Move2Kube will use this information and generate the Kubernetes YAMLs which are tailored for the given target cluster.

1. Download the [kubernetes-to-kubernetes](https://github.com/konveyor/move2kube-demos/tree/main/samples/kubernetes-to-kubernetes) sample.

  ```
  $ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/kubernetes-to-kubernetes -r move2kube-demos
  $ ls kubernetes-to-kubernetes/
  api-deployment.yaml  api-service.yaml  redis-deployment.yaml  redis-service.yaml  web-deployment.yaml  web-ingress.yaml  web-service.yaml
  ```

4. Generate a plan file by providing the `custom-cluster-selector` transformer inside the downloaded `customizations` folder to Move2Kube using the `-c flag`.

```console
$ move2kube plan -s kubernetes-to-kubernetes -c customizations
INFO[0000] Configuration loading done
INFO[0000] Planning Transformation - Base Directory
INFO[0000] [CloudFoundry] Planning transformation
INFO[0000] [CloudFoundry] Done
INFO[0000] [DockerfileDetector] Planning transformation
INFO[0000] [DockerfileDetector] Done
INFO[0000] [ComposeAnalyser] Planning transformation
INFO[0000] [ComposeAnalyser] Done
INFO[0000] [Base Directory] Identified 0 named services and 0 to-be-named services
INFO[0000] Transformation planning - Base Directory done
INFO[0000] Planning Transformation - Directory Walk
INFO[0000] Identified 1 named services and 0 to-be-named services in .
INFO[0000] Identified 1 named services and 0 to-be-named services in .
INFO[0000] Transformation planning - Directory Walk done
INFO[0000] [Directory Walk] Identified 1 named services and 1 to-be-named services
INFO[0000] [Named Services] Identified 1 named services
INFO[0000] No of services identified : 1
INFO[0000] Plan can be found at [/Users/user/m2k.plan].
```

5. View the generated plan file in YAML format. Notice Move2Kube has detected the `custom-cluster-selector` as `customizationsDir` which will be used during the *Transform* phase.

```console
$ cat m2k.plan
```

```yaml
apiVersion: move2kube.konveyor.io/v1alpha1
kind: Plan
metadata:
  name: myproject
spec:
  sourceDir: kubernetes-to-kubernetes
  customizationsDir: custom-cluster-selector
apiVersion: move2kube.konveyor.io/v1alpha1
kind: Plan
metadata:
   name: myproject
 spec:
   sourceDir: kubernetes-to-kubernetes
   customizationsDir: custom-cluster-selector
   services:
     move2kube-transformers:
       - transformerName: Parameterizer
         paths:
           KubernetesYamls:
             - .
           ServiceDirPath:
             - .
       - transformerName: KubernetesVersionChanger
         type: KubernetesOrgYamlsInSource
         paths:
           KubernetesYamls:
             - .
           ServiceDirPath:
             - .
   transformers:
     Buildconfig: m2kassets/built-in/transformers/kubernetes/buildconfig/transformer.yaml
     CloudFoundry: m2kassets/built-in/transformers/cloudfoundry/transformer.yaml
     ComposeAnalyser: m2kassets/built-in/transformers/compose/composeanalyser/transformer.yaml
     ComposeGenerator: m2kassets/built-in/transformers/compose/composegenerator/transformer.yaml
     ContainerImagesPushScriptGenerator: m2kassets/built-in/transformers/containerimagespushscript/transformer.yaml
     CustomClusterSelector: m2kassets/custom/transformer.yaml
     DockerfileDetector: m2kassets/built-in/transformers/dockerfile/dockerfiledetector/transformer.yaml
     DockerfileImageBuildScript: m2kassets/built-in/transformers/dockerfile/dockerimagebuildscript/transformer.yaml
     DockerfileParser: m2kassets/built-in/transformers/dockerfile/dockerfileparser/transformer.yaml
     DotNetCore-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/dotnetcore/transformer.yaml
     EarAnalyser: m2kassets/built-in/transformers/dockerfilegenerator/java/earanalyser/transformer.yaml
     EarRouter: m2kassets/built-in/transformers/dockerfilegenerator/java/earrouter/transformer.yaml
     Golang-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/golang/transformer.yaml
     Gradle: m2kassets/built-in/transformers/dockerfilegenerator/java/gradle/transformer.yaml
     Jar: m2kassets/built-in/transformers/dockerfilegenerator/java/jar/transformer.yaml
     Jboss: m2kassets/built-in/transformers/dockerfilegenerator/java/jboss/transformer.yaml
     Knative: m2kassets/built-in/transformers/kubernetes/knative/transformer.yaml
     Kubernetes: m2kassets/built-in/transformers/kubernetes/kubernetes/transformer.yaml
     KubernetesVersionChanger: m2kassets/built-in/transformers/kubernetes/kubernetesversionchanger/transformer.yaml
     Liberty: m2kassets/built-in/transformers/dockerfilegenerator/java/liberty/transformer.yaml
     Maven: m2kassets/built-in/transformers/dockerfilegenerator/java/maven/transformer.yaml
     Nodejs-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/nodejs/transformer.yaml
     PHP-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/php/transformer.yaml
     Parameterizer: m2kassets/built-in/transformers/kubernetes/parameterizer/transformer.yaml
     Python-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/python/transformer.yaml
     ReadMeGenerator: m2kassets/built-in/transformers/readmegenerator/transformer.yaml
     Ruby-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/ruby/transformer.yaml
     Rust-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/rust/transformer.yaml
     Tekton: m2kassets/built-in/transformers/kubernetes/tekton/transformer.yaml
     Tomcat: m2kassets/built-in/transformers/dockerfilegenerator/java/tomcat/transformer.yaml
     WarAnalyser: m2kassets/built-in/transformers/dockerfilegenerator/java/waranalyser/transformer.yaml
     WarRouter: m2kassets/built-in/transformers/dockerfilegenerator/java/warrouter/transformer.yaml
     WinConsoleApp-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/windows/winconsole/transformer.yaml
     WinSLWebApp-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/windows/winsilverlightweb/transformer.yaml
     WinWebApp-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/windows/winweb/transformer.yaml
     ZuulAnalyser: m2kassets/built-in/transformers/dockerfilegenerator/java/zuul/transformer.yaml
```

1. Run the transformation using `move2kube transform`.

```console
$ move2kube transform
INFO[0000] Detected a plan file at path /home/user/m2k.plan. Will transform using this plan.
? Select all transformer types that you are interested in:
ID: move2kube.transformers.types
Hints:
[Services that don't support any of the transformer types you are interested in will be ignored.]
  [Use arrows to move, space to select, <right> to all, <left> to none, type to filter]
> [✓]  Maven
  [✓]  Rust-Dockerfile
  [✓]  Tomcat
  [✓]  ZuulAnalyser
  [✓]  CustomClusterSelector
  [✓]  Jboss
  [✓]  Parameterizer
  [✓]  ReadMeGenerator
  [✓]  WarRouter
  [✓]  DockerfileDetector
  [✓]  Gradle
  [✓]  Python-Dockerfile
  [✓]  WinConsoleApp-Dockerfile
  [✓]  Buildconfig
  [✓]  EarAnalyser
  [✓]  Knative
  [✓]  Nodejs-Dockerfile
  [✓]  WinSLWebApp-Dockerfile
  [✓]  ContainerImagesPushScriptGenerator
  [✓]  DockerfileImageBuildScript
  [✓]  Tekton
  [✓]  DotNetCore-Dockerfile
  [✓]  Liberty
  [✓]  DockerfileParser
  [✓]  Golang-Dockerfile
  [✓]  WinWebApp-Dockerfile
  [✓]  CloudFoundry
  [✓]  ComposeGenerator
  [✓]  Kubernetes
  [✓]  Ruby-Dockerfile
  [✓]  WarAnalyser
  [✓]  ComposeAnalyser
  [✓]  Jar
  [✓]  PHP-Dockerfile
  [✓]  EarRouter
  [✓]  KubernetesVersionChanger
```

7. Accept the default by pressing the **Enter** key.

```console
? Select all services that are needed:
ID: move2kube.services.[].enable
Hints:
[The services unselected here will be ignored.]
  [Use arrows to move, space to select, <right> to all, <left> to none, type to filter]
> [✓]  myproject
```

8. Accept the detected service.

```console
INFO[0017] Starting Plan Transformation
INFO[0017] Iteration 1  
INFO[0017] Iteration 2 - 1 artifacts to process
INFO[0017] Transformer CustomClusterSelector processing 1 artifacts
? Choose the cluster type:
ID: move2kube.target.clustertype
Hints:
[Choose the cluster type you would like to target]
  [Use arrows to move, type to filter]
  GCP-GKE
  IBM-IKS
  IBM-Openshift
  Kubernetes
> my-kubernetes-cluster
  Openshift
  AWS-EKS
  Openshift
```

9. Select `my-kubernetes-cluster` as the target cluster type to deploy to.

```console
INFO[0393] Transformer CustomClusterSelector Done   
INFO[0393] Transformer KubernetesVersionChanger processing 1 artifacts
INFO[0393] Transformer KubernetesVersionChanger Done
INFO[0393] Created 1 pathMappings and 1 artifacts. Total Path Mappings : 1. Total Artifacts : 1.
INFO[0393] Iteration 3 - 1 artifacts to process
INFO[0393] Transformer Parameterizer processing 1 artifacts
INFO[0393] Transformer Parameterizer Done   
INFO[0393] Plan Transformation done
INFO[0393] Transformed target artifacts can be found at [/Users/user/myproject].
```

10. The transformation has completed and generated a directory called `myproject`.

> **Note:** The name of the output directory is the same as the project name (by default `myproject`). The project name can be changed using the `-n` flag.

  ```console
  $ ls
  customizations   kubernetes-to-kubernetes m2k.plan m2kconfig.yaml   m2kqacache.yaml  myproject

  $ ls myproject/source
  kubernetes-to-kubernetes-versionchanged   kubernetes-to-kubernetes-versionchanged-parameterized
  ```

  The applications can now be deployed to Kubernetes using these generated artifacts.

## Exploring the output

The full structure of the output directory can be seen by executing the `tree` command.

```console
  $ tree myproject/
   myproject
  └── source
      ├── kubernetes-to-kubernetes-versionchanged
      │   ├── api-deployment.yaml
      │   ├── api-service.yaml
      │   ├── redis-deployment.yaml
      │   ├── redis-service.yaml
      │   ├── web-deployment.yaml
      │   ├── web-ingress.yaml
      │   └── web-service.yaml
      └── kubernetes-to-kubernetes-versionchanged-parameterized
          ├── helm-chart
          │   └── move2kube-transformers
          │       ├── Chart.yaml
          │       ├── templates
          │       │   ├── api-deployment.yaml
          │       │   ├── api-service.yaml
          │       │   ├── redis-deployment.yaml
          │       │   ├── redis-service.yaml
          │       │   ├── web-deployment.yaml
          │       │   ├── web-ingress.yaml
          │       │   └── web-service.yaml
          │       ├── values-dev.yaml
          │       ├── values-prod.yaml
          │       └── values-staging.yaml
          ├── kustomize
          │   ├── base
          │   │   ├── api-deployment.yaml
          │   │   ├── api-service.yaml
          │   │   ├── kustomization.yaml
          │   │   ├── redis-deployment.yaml
          │   │   ├── redis-service.yaml
          │   │   ├── web-deployment.yaml
          │   │   ├── web-ingress.yaml
          │   │   └── web-service.yaml
          │   └── overlays
          │       ├── dev
          │       │   ├── apps-v1-deployment-api.yaml
          │       │   ├── apps-v1-deployment-redis.yaml
          │       │   ├── apps-v1-deployment-web.yaml
          │       │   └── kustomization.yaml
          │       ├── prod
          │       │   ├── apps-v1-deployment-api.yaml
          │       │   ├── apps-v1-deployment-redis.yaml
          │       │   ├── apps-v1-deployment-web.yaml
          │       │   └── kustomization.yaml
          │       └── staging
          │           ├── apps-v1-deployment-api.yaml
          │           ├── apps-v1-deployment-redis.yaml
          │           ├── apps-v1-deployment-web.yaml
          │           └── kustomization.yaml
          └── openshift-template
              ├── parameters-dev.yaml
              ├── parameters-prod.yaml
              ├── parameters-staging.yaml
              └── template.yaml

  13 directories, 42 files
```

The `myproject/source/kubernetes-to-kubernetes-versionchanged` directory has the new  Kubernetes YAMLs (deployment/service/ingress/etc.) which are tailored to meet the target cluster requirements.

Some other things to observe:

- The Helm chart in the `source/kubernetes-to-kubernetes-versionchanged-parameterized/helm-chart` directory.
- The Kustomize YAMLs in the `source/kubernetes-to-kubernetes-versionchanged-parameterized/kustomize` directory.
- The Openshift Template in the `source/kubernetes-to-kubernetes-versionchanged-parameterized/openshift-template` directory.

For more details on how to customize the parameterization look at the documentation.

## Conclusion
Follow these steps to complete the tutorial.

1. Run `move2kube collect -a k8s` after logging in to the cluster to collect the cluster-related information inside the `m2k_collect` folder. 
2. Copy-paste the generated YAML file inside `m2k_collect/cluster/` to [custom-cluster-selector/clusters/](https://github.com/konveyor/move2kube-transformers/tree/main/custom-cluster-selector/clusters/). 
3. While running `move2kube plan -s src -c custom-cluster-selector` (or transform `move2kube transform -s src -c custom-cluster-selector`), provide the customization folder to Move2Kube using the `-c flag`. 
4. Select the target cluster in the QA during *Transform* phase. Move2Kube will generate the Kubernetes YAMLs for the target cluster.

Given Kubernetes YAMLs, this tutorial showed how Move2Kube can create/transform the Kubernetes YAMLs to target a specific cluster.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Move2Kube/Tutorials/customKubeYAML.md)