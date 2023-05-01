---
title: "3. Transform"
date: 2022-08-12T12:08:16-06:00
draft: false
permalink: /tutorials/migration-workflow/collect
parent: "Migrating Enterprise Scale Cloud Foundry Apps to Kubernetes"
grand_parent: Tutorials
weight: 3
---

Now run the transformation according to the plan file generated in the previous step. The transformation phase runs all of the transformers again, but this time the transformers will use the plan to generate the output files.

During this process, the transformers might run into situations where it requires some more information to generate the output. In order to get this information, it will ask the user some questions. The questions range from yes/no, to multiple choice, to string input and most will have a default answer.

Example: Some of the questions Move2Kube will ask is about the type of container registry where you want to push the images to. It also needs to know the registry namespace and any authentication necessary for pulling images from that registry.

If you want to skip the QA, use the `--qa-skip` flag to accept the default answers. However, a config file that contains all of the answers using the `--config` flag can be used instead of skipping.

After the transformation is finished, all the answers are written to a config file called `m2kconfig.yaml` which can be used for later transformations.

The transformation phase produces all the necessary output files including the Dockerfiles, build scripts for containerizing various services and Kubernetes deployment, and the service and ingress YAMLs necessary for deploying the application to a Kubernetes cluster.

Move2Kube also generates the CI/CD pipeline and parameterized versions of all the Kubernetes YAMLs (Helm chart, Kustomize YAMLs, Openshift templates, etc.) for various environments (dev, staging, prod, etc.).

## Prerequisites

Perform the Plan step before this procedure.

## Transforming using the CLI

1. Run the transformation in the same directory as the plan file. This will detect the plan file and use it to find the source directory.
```
$ move2kube transform
Optional: Provide answers to questions using a config file...
```
If you want to avoid the question answers during transformation, you can use this [config file](https://github.com/konveyor/move2kube-demos/blob/main/samples/enterprise-app/config/m2kconfig.yaml)
```
$ move2kube transform --config m2kconfig.yaml
```
2. Answer all the questions as appropriate. For most questions accept the default answers. Some questions to watch out for are:

- A spurious service called `config-utils` was detected by one of the transformers can be deselected when asked to select the services or by editing the plan file.
- Move2Kube has detected the Maven profiles for each of the Java services. Select the `dev-inmemorydb` profile to deploy to MiniKube. There will be similar questions for the SpringBoot profiles.
- The container registry and namespace to use. A container registry is where all the images are stored (Example: Quay, Docker Hub, etc.).
- The ingress hostname and ingress TLS secret. If deploying to MiniKube, give `localhost` as the ingress host and leave the TLS secret blank.
- Select `ClusterIP` to only expose the `order` `customers` `inventory` and `gateway` services inside the cluster. Choose `Ingress` and `/` as the path to expose the `frontend` service. This way only the `frontend` will be exposed outside the cluster through the ingress.


```console
$ move2kube transform
INFO[0000] Detected a plan file at path /Users/user/Desktop/tutorial/m2k.plan. Will transform using this plan.
INFO[0000] Starting Plan Transformation 
? Select all transformer types that you are interested in:
ID: move2kube.transformers.types
Hints:
[Services that don't support any of the transformer types you are interested in will be ignored.]
 Buildconfig, CloudFoundry, ClusterSelector, ComposeAnalyser, ComposeGenerator, ContainerImagesPushScriptGenerator, DockerfileDetector, DockerfileImageBuildScript, DockerfileParser, DotNetCore-Dockerfile,EarAnalyser, EarRouter, Golang-Dockerfile, Gradle, Jar, Jboss, Knative, Kubernetes, KubernetesVersionChanger, Liberty, Maven, Nodejs-Dockerfile, PHP-Dockerfile, Parameterizer, Python-Dockerfile, ReadMeGenerator,Ruby-Dockerfile, Rust-Dockerfile, Tekton, Tomcat, WarAnalyser, WarRouter, WinConsoleApp-Dockerfile, WinSLWebApp-Dockerfile, WinWebApp-Dockerfile, ZuulAnalyser
? Select all services that are needed:
ID: move2kube.services.[].enable
Hints:
[The services unselected here will be ignored.]
 customers, frontend, gateway, inventory, orders
INFO[0005] Iteration 1  
INFO[0005] Iteration 2 - 5 artifacts to process 
INFO[0005] Transformer CloudFoundry processing 3 artifacts
INFO[0005] Transformer CloudFoundry Done
INFO[0005] Transformer Maven processing 2 artifacts
```
{{%expand "Click to see the remaining transform questions."%}}
```console 
? Choose the Maven profile to be used for the service customers
ID: move2kube.services.customers.activemavenprofiles
Hints:
[Selected Maven profiles will be used for setting configuration for the service customers]
 prod-externaldb
? Choose Springboot profiles to be used for the service customers
ID: move2kube.services.customers.activespringbootprofiles
Hints:
[Selected Springboot profiles will be used for setting configuration for the service customers]
 prod-externaldb
? Choose the Maven profile to be used for the service inventory
ID: move2kube.services.inventory.activemavenprofiles
Hints:
[Selected Maven profiles will be used for setting configuration for the service inventory]
 prod-externaldb
? Choose Springboot profiles to be used for the service inventory
ID: move2kube.services.inventory.activespringbootprofiles
Hints:
[Selected Springboot profiles will be used for setting configuration for the service inventory]
 prod-externaldb
? Select port to be exposed for the service inventory :
ID: move2kube.services.inventory.port
Hints:
[Select Other if you want to expose the service inventory to some other port]
 8080
INFO[0010] Transformer WarRouter processing 2 artifacts
? Select the transformer to use for service customers
ID: move2kube.services.customers.wartransformer
 Tomcat
INFO[0012] Transformer WarRouter Done   
INFO[0012] Transformer Maven Done   
INFO[0012] Created 2 pathMappings and 6 artifacts. Total Path Mappings : 2. Total Artifacts : 5.
INFO[0012] Iteration 3 - 6 artifacts to process 
INFO[0012] Transformer Jar processing 1 artifacts   
INFO[0012] Transformer Jar Done 
INFO[0012] Transformer Maven processing 2 artifacts 
? Choose the Maven profile to be used for the service gateway
ID: move2kube.services.gateway.activemavenprofiles
Hints:
[Selected Maven profiles will be used for setting configuration for the service gateway]
 prod
? Choose Springboot profiles to be used for the service gateway
ID: move2kube.services.gateway.activespringbootprofiles
Hints:
[Selected Springboot profiles will be used for setting configuration for the service gateway]
 prod
? Select port to be exposed for the service gateway :
ID: move2kube.services.gateway.port
Hints:
[Select Other if you want to expose the service gateway to some other port]
 8080
? Choose the Maven profile to be used for the service orders
ID: move2kube.services.orders.activemavenprofiles
Hints:
[Selected Maven profiles will be used for setting configuration for the service orders]
 prod-externaldb
? Choose Springboot profiles to be used for the service orders
ID: move2kube.services.orders.activespringbootprofiles
Hints:
[Selected Springboot profiles will be used for setting configuration for the service orders]
 prod-externaldb
? Select port to be exposed for the service orders :
ID: move2kube.services.orders.port
Hints:
[Select Other if you want to expose the service orders to some other port]
 8080
INFO[0018] Transformer Maven Done   
INFO[0018] Transformer Nodejs-Dockerfile processing 1 artifacts
? Enter the port to be exposed for the service frontend:
ID: move2kube.services.frontend.port
Hints:
[The service frontend will be exposed to the specified port]
 8080
INFO[0021] Transformer Nodejs-Dockerfile Done   
INFO[0021] Transformer Tomcat processing 2 artifacts
INFO[0021] Transformer Tomcat Done  
INFO[0021] Created 10 pathMappings and 10 artifacts. Total Path Mappings : 12. Total Artifacts : 11.
INFO[0021] Iteration 4 - 10 artifacts to process
INFO[0021] Transformer DockerfileImageBuildScript processing 4 artifacts
? Select the container runtime to use :
ID: move2kube.containerruntime
Hints:
[The container runtime selected will be used in the scripts]
 docker
INFO[0022] Transformer DockerfileImageBuildScript Done  
INFO[0022] Transformer DockerfileParser processing 4 artifacts
INFO[0022] Transformer ZuulAnalyser processing 2 artifacts
INFO[0022] Transformer ZuulAnalyser Done
INFO[0022] Transformer DockerfileParser Done
INFO[0022] Transformer Jar processing 2 artifacts   
INFO[0022] Transformer Jar Done 
INFO[0022] Created 5 pathMappings and 10 artifacts. Total Path Mappings : 17. Total Artifacts : 21.
INFO[0022] Iteration 5 - 10 artifacts to process
INFO[0022] Transformer ClusterSelector processing 2 artifacts
? Choose the cluster type:
ID: move2kube.target.clustertype
Hints:
[Choose the cluster type you would like to target]
 Kubernetes
INFO[0024] Transformer ClusterSelector Done 
INFO[0024] Transformer Buildconfig processing 2 artifacts
? What kind of service/ingress to create for inventory's 8080 port?
ID: move2kube.services."inventory"."8080".servicetype
Hints:
[Choose Ingress if you want a ingress/route resource to be created]
 ClusterIP
? What kind of service/ingress to create for frontend's 8080 port?
ID: move2kube.services."frontend"."8080".servicetype
Hints:
[Choose Ingress if you want a ingress/route resource to be created]
 Ingress
? Specify the ingress path to expose frontend's 8080 port?
ID: move2kube.services."frontend"."8080".urlpath
Hints:
[Leave out leading / to use first part as subdomain]
 /
? What kind of service/ingress to create for customers's 8080 port?
ID: move2kube.services."customers"."8080".servicetype
Hints:
[Choose Ingress if you want a ingress/route resource to be created]
 ClusterIP
? Provide the minimum number of replicas each service should have
ID: move2kube.minreplicas
Hints:
[If the value is 0 pods won't be started by default]
 2
? Enter the URL of the image registry :
ID: move2kube.target.imageregistry.url
Hints:
[You can always change it later by changing the yamls.]
 quay.io
? Enter the namespace where the new images should be pushed :
ID: move2kube.target.imageregistry.namespace
Hints:
[Ex : myproject]
 move2kube
? [quay.io] What type of container registry login do you want to use?
ID: move2kube.target.imageregistry.logintype
Hints:
[Docker login from config mode, will use the default config from the local machine.]
 No authentication
INFO[0051] Transformer Buildconfig Done 
INFO[0051] Transformer ComposeGenerator processing 2 artifacts
INFO[0051] Transformer ComposeGenerator Done
INFO[0051] Transformer ContainerImagesPushScriptGenerator processing 2 artifacts
INFO[0051] Transformer ContainerImagesPushScriptGenerator Done
INFO[0051] Transformer DockerfileImageBuildScript processing 3 artifacts
INFO[0051] Transformer DockerfileImageBuildScript Done  
INFO[0051] Transformer DockerfileParser processing 2 artifacts
INFO[0051] Transformer ZuulAnalyser processing 2 artifacts
INFO[0051] Transformer ZuulAnalyser Done
INFO[0051] Transformer DockerfileParser Done
INFO[0051] Transformer ClusterSelector processing 2 artifacts
INFO[0051] Transformer ClusterSelector Done 
INFO[0051] Transformer Knative processing 2 artifacts   
INFO[0051] Transformer Knative Done 
INFO[0051] Transformer ClusterSelector processing 2 artifacts
INFO[0051] Transformer ClusterSelector Done 
INFO[0051] Transformer Kubernetes processing 2 artifacts
? Provide the ingress host domain
ID: move2kube.target.ingress.host
Hints:
[Ingress host domain is part of service URL]
 localhost
? Provide the TLS secret for ingress
ID: move2kube.target.ingress.tls
Hints:
[Leave empty to use http]

INFO[0058] Transformer Kubernetes Done  
INFO[0058] Transformer ClusterSelector processing 2 artifacts
INFO[0058] Transformer ClusterSelector Done 
INFO[0058] Transformer Tekton processing 2 artifacts
INFO[0059] Transformer Tekton Done  
INFO[0059] Created 32 pathMappings and 15 artifacts. Total Path Mappings : 49. Total Artifacts : 31.
INFO[0059] Iteration 6 - 15 artifacts to process
INFO[0059] Transformer ClusterSelector processing 2 artifacts
INFO[0059] Transformer ClusterSelector Done 
INFO[0059] Transformer Buildconfig processing 2 artifacts
? What kind of service/ingress to create for orders's 8080 port?
ID: move2kube.services."orders"."8080".servicetype
Hints:
[Choose Ingress if you want a ingress/route resource to be created]
 ClusterIP
? What kind of service/ingress to create for gateway's 8080 port?
ID: move2kube.services."gateway"."8080".servicetype
Hints:
[Choose Ingress if you want a ingress/route resource to be created]
 ClusterIP
INFO[0066] Transformer Buildconfig Done 
INFO[0066] Transformer ComposeGenerator processing 2 artifacts
INFO[0066] Transformer ComposeGenerator Done
INFO[0066] Transformer ContainerImagesPushScriptGenerator processing 2 artifacts
INFO[0066] Transformer ContainerImagesPushScriptGenerator Done
INFO[0066] Transformer ClusterSelector processing 2 artifacts
INFO[0067] Transformer ClusterSelector Done 
INFO[0067] Transformer Knative processing 2 artifacts   
INFO[0067] Transformer Knative Done 
INFO[0067] Transformer ClusterSelector processing 2 artifacts
INFO[0067] Transformer ClusterSelector Done 
INFO[0067] Transformer Kubernetes processing 2 artifacts
INFO[0067] Transformer Kubernetes Done  
INFO[0067] Transformer Parameterizer processing 4 artifacts
INFO[0067] Transformer Parameterizer Done   
INFO[0067] Transformer ReadMeGenerator processing 5 artifacts
INFO[0067] Transformer ReadMeGenerator Done 
INFO[0067] Transformer ClusterSelector processing 2 artifacts
INFO[0067] Transformer ClusterSelector Done 
INFO[0067] Transformer Tekton processing 2 artifacts
INFO[0068] Transformer Tekton Done  
INFO[0068] Created 52 pathMappings and 7 artifacts. Total Path Mappings : 101. Total Artifacts : 46.
INFO[0068] Iteration 7 - 7 artifacts to process 
INFO[0068] Transformer Parameterizer processing 4 artifacts
INFO[0068] Transformer Parameterizer Done   
INFO[0068] Transformer ReadMeGenerator processing 5 artifacts
INFO[0068] Transformer ReadMeGenerator Done 
INFO[0069] Plan Transformation done 
INFO[0069] Transformed target artifacts can be found at [/Users/user/Desktop/tutorial/myproject].
```
{{% /expand%}}

## Transforming using the UI
Continue from the previous step in the UI.

1. Scroll down from the **Plan** section to the **Outputs** section.

```consle
spec:
    sourceDir:sources
    services:
        config-utils:
            -transformerName: Maven
             paths:
                MavenPom:
                 -src/src/config-utils/pom.xml
                ServiceDirPath:
                 -src/src/config-utils
             configs:
                Maven:
                 mavenAppName: config-utils
                 artifactType: jar
            customers-tomcat:
             - transformerName: Maven
                 
```
2. Click the **Start transformation** button.

A form to ask the user questions to guide the transformation opens.

3. Answer all the questions as appropriate. For most questions accept the default answers. Some questions to watch out for are:

* A spurious service called `config-utils` was detected by one of the transformers can be deselected when asked to select the services or by editing the plan file.
* Move2Kube has detected the Maven profiles for each of the Java services. Select the `dev-inmemorydb` profile to deploy to MiniKube. There will be similar questions for the SpringBoot profiles.
* The container registry and namespace to use. A container registry is where all the images are stored (Example: Quay, Docker Hub, etc.).
* The ingress hostname and ingress TLS secret. If deploying to MiniKube, give `localhost` as the ingress host and leave the TLS secret blank.
* Select `ClusterIP` to only expose the `order` `customers` `inventory` and `gateway` services inside the cluster. Choose `Ingress` and `/` as the path to expose the `frontend` service. This way only the `frontend` will be exposed outside the cluster through the ingress.

4. Click the **Next** button to continue going through the questions and then run the tranformation.

Move2Kube processes the transformation and the output appears.

5. Click the output ID link to download.

## Using the output generated by Move2Kube transform

For a sample output of what Move2Kube generates for this enterprise app, click [here](https://github.com/konveyor/move2kube-demos/tree/main/samples/enterprise-app/output).

After the output has generated, run the scripts inside the `scripts` directory.

1. Run the `builddockerimages.sh` script to build all the container images for each service using the Dockerfiles that were generated.
```
$ cd myproject/scripts/
$ ./builddockerimages.sh
```
2. Run the `pushimages.sh` script to push them to the specified container registry.
```
$ ./pushimages.sh
```
3. Because the `prod-externaldb` profile was selected, deploy the database using the YAMLs located [here](https://github.com/konveyor/move2kube-demos/tree/main/samples/enterprise-app/database).
```
$ cd ..
$ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/enterprise-app/database -r move2kube-demos
$ minikube start --memory 8192 --cpus 2 # do this only if you are deploying to Minikube
$ kubectl apply -f database/
```
4. Deploy the Kubernetes YAMLs that Move2Kube generated to the cluster
```
$ kubectl apply -f deploy/yamls
```
The application is now running on the cluster.

5. Get the URL where the app has been deployed to, using `kubectl get ingress myproject -o yaml`  

> **Note:** If deployed to Minikube, enable the ingress addon and start `minikube tunnel` to access the ingress on `localhost`.

```console
$ minikube addons enable ingress
💡  After the addon is enabled, please run "minikube tunnel" and the ingress resources would be available at "127.0.0.1"
▪ Using image k8s.gcr.io/ingress-nginx/controller:v1.0.4
▪ Using image k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1
▪ Using image k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1
🔎  Verifying ingress addon...
🌟  The 'ingress' addon is enabled
$ minikube addons enable ingress-dns
💡  After the addon is enabled, please run "minikube tunnel" and the ingress resources would be available at "127.0.0.1"
▪ Using image gcr.io/k8s-minikube/minikube-ingress-dns:0.0.2
🌟  The 'ingress-dns' addon is enabled
$ minikube tunnel
❗  The service/ingress myproject requires privileged ports to be exposed: [80 443]
🔑  sudo permission will be asked for it.
🏃  Starting tunnel for service myproject.
Password:
```
The app is now available on [http://localhost](http://localhost).

> **Optional:** As part of the transformation, if Cloud Foundry runtime information is required, use the collect output in planning and transformation: Collect information from running apps.

### Customizing the output

After inspecting the output that Move2Kube produced some changes might be necessary. For example:
* Changing the base image used in the Dockerfiles.
* Adding some annotations to the Ingress YAML.
* Changing the output directory structure.
* Changing which values are parameterized in the Helm chart.
* Generating some new files, etc. 

For all these user specific requirements and more, use customizations.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Move2Kube/Tutorials/CFappsToK8/3transform.md)