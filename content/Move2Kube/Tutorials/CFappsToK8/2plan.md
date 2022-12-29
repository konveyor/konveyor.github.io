---
title: "2. Plan"
date: 2022-08-12T12:08:16-06:00
draft: false
permalink: /tutorials/migration-workflow/collect
parent: "Migrating Enterprise Scale Cloud Foundry Apps to Kubernetes"
grand_parent: Tutorials
weight: 2
---

---
We start by planning the migration. During the plan phase, Move2Kube will analyze the files in the source directory, detect what services exist, create a plan on how to containerize them using Dockerfiles, and transform them into Kubernetes deployments, services, ingress, etc.

In order to do the planning, Move2Kube has a large number of built-in transformers for different languages and platforms. Each transformer walks through the source directory from top to bottom and tries to find files that it recognizes. For example, a Golang transformer will try to find a `go.mod` file to detect a Golang project. Once it detects a directory containing a service, it will try to extract as much information from it as possible. Some of the information it tries to find are the service name, ports, environment variables, etc.

The plan file can be created using the CLI or through the UI. When complete, the plan file contains all the transformers that Move2Kube detected and ran. These transformers will be run again during the transformation phase.  

The plan file also contains all the services that Move2Kube was able to detect. The service name comes from the transformer that detected that service. We can edit this plan before moving to the transformation phase. For now, leave it as is.

This information is stored in YAML format in a plan file called `m2k.plan` which is used later during the transformation phase. We can edit this file to enable/disable transformers, add/remove detected services, etc.

## Prerequisites

Download the [enterprise-app](https://github.com/konveyor/move2kube-demos/tree/main/samples/enterprise-app) using the command below.

```console
$ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/enterprise-app/src -r move2kube-demos
$ ls src
README.md		config-utils		customers	docs			frontend		gateway			orders
  ```

## Planning using the CLI

1. Run `move2kube plan -s src` to generate a plan for migrating the multiple components of the app to Kubernetes.


```console
$ move2kube plan -s src
INFO[0000] Configuration loading done   
INFO[0000] Planning Transformation - Base Directory 
INFO[0000] [CloudFoundry] Planning transformation   
INFO[0000] Identified 3 named services and 0 to-be-named services
INFO[0000] [CloudFoundry] Done  
INFO[0000] [ComposeAnalyser] Planning transformation
INFO[0000] [ComposeAnalyser] Done   
INFO[0000] [DockerfileDetector] Planning transformation
INFO[0000] [DockerfileDetector] Done
INFO[0000] [Base Directory] Identified 3 named services and 0 to-be-named services
INFO[0000] Transformation planning - Base Directory done
INFO[0000] Planning Transformation - Directory Walk 
INFO[0000] Identified 1 named services and 0 to-be-named services in config-utils
INFO[0000] Identified 1 named services and 0 to-be-named services in customers
INFO[0000] Identified 1 named services and 0 to-be-named services in frontend
INFO[0000] Identified 1 named services and 0 to-be-named services in gateway
INFO[0000] Identified 1 named services and 0 to-be-named services in inventory
INFO[0000] Identified 1 named services and 0 to-be-named services in orders
INFO[0000] Transformation planning - Directory Walk done
INFO[0000] [Directory Walk] Identified 6 named services and 0 to-be-named services
INFO[0000] [Named Services] Identified 6 named services
INFO[0000] No of services identified : 6
INFO[0000] Plan can be found at [/Users/user/Desktop/tutorial/m2k.plan].
```

2. Look at the plan file that was generated.
```console
$ cat m2k.plan
```
```yaml
apiVersion: move2kube.konveyor.io/v1alpha1
kind: Plan
......
apiVersion: move2kube.konveyor.io/v1alpha1
kind: Plan
metadata:
  name: myproject
spec:
  sourceDir: src
  services:
    config-utils:
```
{{%expand "Click to see the rest of the YAML."%}}
```yaml
      - transformerName: Maven
      paths:
        MavenPom:
          - config-utils/pom.xml
        ServiceDirPath:
          - config-utils
      configs:
        Maven:
          mavenAppName: config-utils
          artifactType: jar
          mvnwPresent: false
      customers:
        - transformerName: Maven
        paths:
          MavenPom:
            - customers/pom.xml
          ServiceDirPath:
            - customers
        configs:
          Maven:
            mavenAppName: customers
            artifactType: war
            mavenProfiles:
              - dev-inmemorydb
              - prod-externaldb
            mvnwPresent: true
          SpringBoot:
            springBootVersion: 2.5.0
            springBootProfiles:
              - dev-inmemorydb
              - prod-externaldb
        frontend:
          - transformerName: CloudFoundry
          paths:
            CfManifest:
              - frontend/manifest.yml
            ServiceDirPath:
              - frontend
          configs:
            CloudFoundryService:
              serviceName: frontend
            ContainerizationOptions:
              - Nodejs-Dockerfile
          - transformerName: Nodejs-Dockerfile
            paths:
              ServiceDirPath:
                - frontend
          gateway:
            - transformerName: CloudFoundry
              paths:
                BuildArtifact:
                  - gateway/target/ROOT.jar
                CfManifest:
                  - gateway/manifest.yml
                ServiceDirPath:
                  - gateway
              configs:
                CloudFoundryService:
                  serviceName: gateway
                ContainerizationOptions:
                  - Maven
            - transformerName: Maven
              paths:
                MavenPom:
                  - gateway/pom.xml
                ServiceDirPath:
                  - gateway
              configs:
                Maven:
                  mavenAppName: gateway
                  artifactType: jar
                  mavenProfiles:
                    - dev
                    - prod
                  mvnwPresent: true
                SpringBoot:
                  springBootAppName: gateway
                  springBootProfiles:
                    - dev
                    - prod
          inventory:
            - transformerName: Maven
              paths:
                MavenPom:
                  - inventory/pom.xml
                ServiceDirPath:
                  - inventory
              configs:
                Maven:
                  mavenAppName: inventory
                  artifactType: jar
                  mavenProfiles:
                    - dev-inmemorydb
                    - prod-externaldb
                  mvnwPresent: true
                SpringBoot:
                  springBootProfiles:
                    - dev-inmemorydb
                    - prod-externaldb
            orders:
              - transformerName: CloudFoundry
                paths:
                  BuildArtifact:
                    - orders/target/ROOT.jar
                  CfManifest:
                    - orders/manifest.yml
                  ServiceDirPath:
                    - orders
                configs:
                  CloudFoundryService:
                    serviceName: orders
                  ContainerizationOptions:
                    - Maven
              - transformerName: Maven
                paths:
                  MavenPom:
                    - orders/pom.xml
                  ServiceDirPath:
                    - orders
                configs:
                  Maven:
                    mavenAppName: orders
                    artifactType: jar
                    mavenProfiles:
                      - dev-inmemorydb
                      - prod-externaldb
                    mvnwPresent: true
                  SpringBoot:
                    springBootAppName: orders
                    springBootProfiles:
                      - dev-inmemorydb
                      - prod-externaldb
transformers:
  Buildconfig: m2kassets/built-in/transformers/kubernetes/buildconfig/transformer.yaml
  CloudFoundry: m2kassets/built-in/transformers/cloudfoundry/transformer.yaml
  ClusterSelector: m2kassets/built-in/transformers/kubernetes/clusterselector/transformer.yaml
  ComposeAnalyser: m2kassets/built-in/transformers/compose/composeanalyser/transformer.yaml
  ComposeGenerator: m2kassets/built-in/transformers/compose/composegenerator/transformer.yaml
  ContainerImagesPushScriptGenerator: m2kassets/built-in/transformers/containerimagespushscript/transformer.yaml
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
{{% /expand%}}

Next step: Transform to generate the output needed to deploy the app to Kubernetes.

## Planning using the UI

1. Open the UI.
```console
$ docker run --rm -it -p 8080:8080 quay.io/konveyor/move2kube-ui:v0.3.1
INFO[0000] Starting Move2Kube API server at port: 8080
```
2. Create a new workspace.
3. Create a new project.
4. Scroll down to the **project inputs** section and then upload the source directory and the collected information zip files.

Optional: If you have collected Cloud Foundry runtime metadata using the `move2kube collect` command you can create a zip file and upload that as well. Make sure to upload it as `sources`.

5. Scroll down to the **planning** section and click **Start Planning**.

> **Note** Planning takes a few minutes.

Next step: Transform to generate the output needed to deploy app to Kubernetes.
