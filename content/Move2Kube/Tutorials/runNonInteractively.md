---
title: "Run transforms non-interactively"
date: 2022-08-04T19:10:32-06:00
draft: false
---

Move2Kube interacts with users through a series of questions during the transformation phase. After looking at the output, re-running it and giving different answers to some of the questions may be necessary. In order to avoid answering all of the same questions over and over, Move2Kube provides a simple configuration file.

In the directory where the `move2kube transform` command was run there is a file called `m2kconfig.yaml` which contains the answers provided to all of the questions that were asked. There is also a `m2kqacache.yaml` file which contains both the questions and the answers in more detail and can be used when running the transform using the `--config` flag

**Summary command**

```console
$ move2kube transform --config path/to/m2kconfig.yaml
```

## Running a tranform non-interactively

1. Download the language platforms sample. Each directory contains a simple web application written in different languages.
```console
    $ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/language-platforms -r move2kube-demos

    $ ls language-platforms
    django		golang		java-gradle	java-gradle-war	java-maven	java-maven-war	nodejs		php		python		ruby		rust
```

> **Important:** If there already is a `m2kconfig.yaml` from a previous run, skip the next step.

2. Run the plan and transform on the `language-platforms` source, answer all the questions as appropriate.
```console
    $ ls
    language-platforms
    $ move2kube plan -s language-platforms
    INFO[0000] Configuration loading done                   
    INFO[0000] Planning Transformation - Base Directory     
    ...                
    INFO[0000] Plan can be found at [/Users/user/Desktop/tutorial/m2k.plan].
    $ move2kube transform
    INFO[0000] Detected a plan file at path /Users/user/Desktop/tutorial/m2k.plan. Will transform using this plan.
    ...
    INFO[0095] Plan Transformation done
    INFO[0095] Transformed target artifacts can be found at [/Users/user/Desktop/tutorial/myproject].
```

3. View the `m2kconfig.yaml` file in the output.

```console
    $ ls
    m2k.plan	m2kconfig.yaml	m2kqacache.yaml	myproject	language-platforms
    $ cat m2kconfig.yaml
```
    The `m2kconfig.yaml` might look different depending on what questions were asked and the answers given.
    
```yaml
    move2kube:
      containerruntime: docker
      minreplicas: "2"
      services:
        golang:
          "8080":
            urlpath: /golang
          enable: true
          ports:
            - "8080"
        java-gradle:
          "9080":
            urlpath: /java-gradle
          enable: true
          wartransformer: Liberty
        java-maven:
          "9080":
            urlpath: /java-maven
          enable: true
          wartransformer: Liberty
        myproject-django:
          "8080":
            urlpath: /myproject-django
          enable: true
          port: "8080"
        myproject-java-war:
          "9080":
            urlpath: /myproject-java-war
          enable: true
          wartransformer: Liberty
        myproject-php:
          "8082":
            urlpath: /myproject-php
          enable: true
        myproject-python:
          "8080":
            urlpath: /myproject-python
          enable: true
          port: "8080"
        nodejs:
          "8080":
            urlpath: /nodejs
          enable: true
          port: "8080"
        ruby:
          "8080":
            urlpath: /ruby
          enable: true
          port: "8080"
        rust:
          "8085":
            urlpath: /rust
          enable: true
          port: "8085"
      target:
        clustertype: Kubernetes
        imageregistry:
          logintype: No authentication
          namespace: myproject
          url: quay.io
        ingress:
          host: myproject.com
          tls: ""
      transformers:
        types:
          - EarRouter
          - WinConsoleApp-Dockerfile
          - Gradle
          - Jar
          - WinSLWebApp-Dockerfile
          - DockerfileImageBuildScript
          - DotNetCore-Dockerfile
          - EarAnalyser
          - Golang-Dockerfile
          - Ruby-Dockerfile
          - Tomcat
          - Buildconfig
          - ComposeGenerator
          - ContainerImagesPushScriptGenerator
          - DockerfileParser
          - WarAnalyser
          - WinWebApp-Dockerfile
          - WarRouter
          - ZuulAnalyser
          - KubernetesVersionChanger
          - Tekton
          - Maven
          - PHP-Dockerfile
          - Parameterizer
          - Python-Dockerfile
          - ClusterSelector
          - DockerfileDetector
          - Kubernetes
          - Liberty
          - Rust-Dockerfile
          - Nodejs-Dockerfile
          - ReadMeGenerator
          - CloudFoundry
          - ComposeAnalyser
          - Jboss
          - Knative
      transformerselector: ""
```

The config file only contains the answers provided to the questions. To better understand the config file, look at its companion file `m2kqacache.yaml`

```console
    $ cat m2kqacache.yaml
    ```

```yaml
    apiVersion: move2kube.konveyor.io/v1alpha1
    kind: QACache
    spec:
      solutions:
        - id: move2kube.transformers.types
          type: MultiSelect
          description: 'Select all transformer types that you are interested in:'
          hints:
            - Services that don't support any of the transformer types you are interested in will be ignored.
          options:
            - Golang-Dockerfile
            - Nodejs-Dockerfile
            - ContainerImagesPushScriptGenerator
            - ReadMeGenerator
            - ComposeAnalyser
            - EarAnalyser
            - Ruby-Dockerfile
            - WarAnalyser
            - DotNetCore-Dockerfile
            - Python-Dockerfile
            - Liberty
            - Knative
            - CloudFoundry
            - Tomcat
            - ZuulAnalyser
            - Tekton
            - Rust-Dockerfile
            - KubernetesVersionChanger
            - DockerfileParser
            - Parameterizer
            - PHP-Dockerfile
            - Kubernetes
            - WinWebApp-Dockerfile
            - Maven
            - Jboss
            - ComposeGenerator
            - Gradle
            - WinConsoleApp-Dockerfile
            - Buildconfig
            - DockerfileDetector
            - WarRouter
            - EarRouter
            - ClusterSelector
            - Jar
            - DockerfileImageBuildScript
            - WinSLWebApp-Dockerfile
          default:
            - Golang-Dockerfile
            - Nodejs-Dockerfile
            - ContainerImagesPushScriptGenerator
            - ReadMeGenerator
            - ComposeAnalyser
            - EarAnalyser
            - Ruby-Dockerfile
            - WarAnalyser
            - DotNetCore-Dockerfile
            - Python-Dockerfile
            - Liberty
            - Knative
            - CloudFoundry
            - Tomcat
            - ZuulAnalyser
            - Tekton
            - Rust-Dockerfile
            - KubernetesVersionChanger
            - DockerfileParser
            - Parameterizer
            - PHP-Dockerfile
            - Kubernetes
            - WinWebApp-Dockerfile
            - Maven
            - Jboss
            - ComposeGenerator
            - Gradle
            - WinConsoleApp-Dockerfile
            - Buildconfig
            - DockerfileDetector
            - WarRouter
            - EarRouter
            - ClusterSelector
            - Jar
            - DockerfileImageBuildScript
            - WinSLWebApp-Dockerfile
          answer:
            - EarRouter
            - WinConsoleApp-Dockerfile
            - Gradle
            - Jar
            - WinSLWebApp-Dockerfile
            - DockerfileImageBuildScript
            - DotNetCore-Dockerfile
            - EarAnalyser
            - Golang-Dockerfile
            - Ruby-Dockerfile
            - Tomcat
            - Buildconfig
            - ComposeGenerator
            - ContainerImagesPushScriptGenerator
            - DockerfileParser
            - WarAnalyser
            - WinWebApp-Dockerfile
            - WarRouter
            - ZuulAnalyser
            - KubernetesVersionChanger
            - Tekton
            - Maven
            - PHP-Dockerfile
            - Parameterizer
            - Python-Dockerfile
            - ClusterSelector
            - DockerfileDetector
            - Kubernetes
            - Liberty
            - Rust-Dockerfile
            - Nodejs-Dockerfile
            - ReadMeGenerator
            - CloudFoundry
            - ComposeAnalyser
            - Jboss
            - Knative
        - id: move2kube.transformerselector
          type: Input
          hints:
            - Set the transformer selector config.
          default: ""
          answer: ""
        - id: move2kube.services.[].enable
          type: MultiSelect
          description: 'Select all services that are needed:'
          hints:
            - The services unselected here will be ignored.
          options:
            - java-maven
            - myproject-django
            - myproject-python
            - rust
            - golang
            - java-gradle
            - myproject-java-war
            - myproject-php
            - nodejs
            - ruby
          default:
            - java-maven
            - myproject-django
            - myproject-python
            - rust
            - golang
            - java-gradle
            - myproject-java-war
            - myproject-php
            - nodejs
            - ruby
          answer:
            - java-maven
            - myproject-django
            - myproject-python
            - rust
            - golang
            - java-gradle
            - myproject-java-war
            - myproject-php
            - nodejs
            - ruby
        - id: move2kube.services.java-gradle.wartransformer
          type: Select
          description: Select the transformer to use for service java-gradle
          options:
            - Liberty
            - Tomcat
            - Jboss
          default: Liberty
          answer: Liberty
        - id: move2kube.services.ruby.port
          type: Select
          description: 'Select port to be exposed for the service ruby :'
          hints:
            - Select Other if you want to expose the service ruby to some other port
          options:
            - "8080"
            - Other (specify custom option)
          default: "8080"
          answer: "8080"
        - id: move2kube.services.nodejs.port
          type: Input
          description: 'Enter the port to be exposed for the service nodejs: '
          hints:
            - The service nodejs will be exposed to the specified port
          default: "8080"
          answer: "8080"
        - id: move2kube.services.myproject-python.port
          type: Select
          description: 'Select port to be exposed for the service myproject-python :'
          hints:
            - Select Other if you want to expose the service myproject-python to some other port
          options:
            - "8080"
            - Other (specify custom option)
          default: "8080"
          answer: "8080"
        - id: move2kube.services.myproject-django.port
          type: Select
          description: 'Select port to be exposed for the service myproject-django :'
          hints:
            - Select Other if you want to expose the service myproject-django to some other port
          options:
            - "8080"
            - Other (specify custom option)
          default: "8080"
          answer: "8080"
        - id: move2kube.services.java-maven.wartransformer
          type: Select
          description: Select the transformer to use for service java-maven
          options:
            - Liberty
            - Tomcat
            - Jboss
          default: Liberty
          answer: Liberty
        - id: move2kube.services.rust.port
          type: Select
          description: 'Select port to be exposed for the service rust :'
          hints:
            - Select Other if you want to expose the service rust to some other port
          options:
            - "8085"
            - Other (specify custom option)
          default: "8085"
          answer: "8085"
        - id: move2kube.services.myproject-java-war.wartransformer
          type: Select
          description: Select the transformer to use for service myproject-java-war
          options:
            - Liberty
            - Tomcat
            - Jboss
          default: Liberty
          answer: Liberty
        - id: move2kube.services.golang.ports
          type: MultiSelect
          description: 'Select ports to be exposed for the service golang :'
          hints:
            - Select Other if you want to add more ports
          options:
            - "8080"
            - Other (specify custom option)
          default:
            - "8080"
          answer:
            - "8080"
        - id: move2kube.containerruntime
          type: Select
          description: 'Select the container runtime to use :'
          hints:
            - The container runtime selected will be used in the scripts
          options:
            - docker
            - podman
          default: docker
          answer: docker
        - id: move2kube.services."rust"."8085".urlpath
          type: Input
          description: What URL/path should we expose the service rust's 8085 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /rust
          answer: /rust
        - id: move2kube.services."myproject-django"."8080".urlpath
          type: Input
          description: What URL/path should we expose the service myproject-django's 8080 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /myproject-django
          answer: /myproject-django
        - id: move2kube.services."golang"."8080".urlpath
          type: Input
          description: What URL/path should we expose the service golang's 8080 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /golang
          answer: /golang
        - id: move2kube.services."nodejs"."8080".urlpath
          type: Input
          description: What URL/path should we expose the service nodejs's 8080 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /nodejs
          answer: /nodejs
        - id: move2kube.services."ruby"."8080".urlpath
          type: Input
          description: What URL/path should we expose the service ruby's 8080 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /ruby
          answer: /ruby
        - id: move2kube.services."myproject-python"."8080".urlpath
          type: Input
          description: What URL/path should we expose the service myproject-python's 8080 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /myproject-python
          answer: /myproject-python
        - id: move2kube.services."myproject-php"."8082".urlpath
          type: Input
          description: What URL/path should we expose the service myproject-php's 8082 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /myproject-php
          answer: /myproject-php
        - id: move2kube.minreplicas
          type: Input
          description: Provide the minimum number of replicas each service should have
          hints:
            - If the value is 0 pods won't be started by default
          default: "2"
          answer: "2"
        - id: move2kube.target.imageregistry.url
          type: Select
          description: 'Enter the URL of the image registry : '
          hints:
            - You can always change it later by changing the yamls.
          options:
            - Other (specify custom option)
            - us.icr.io
            - quay.io
            - registry.ng.bluemix.net
          default: quay.io
          answer: quay.io
        - id: move2kube.target.imageregistry.namespace
          type: Input
          description: 'Enter the namespace where the new images should be pushed : '
          hints:
            - 'Ex : myproject'
          default: myproject
          answer: myproject
        - id: move2kube.target.imageregistry.logintype
          type: Select
          description: '[quay.io] What type of container registry login do you want to use?'
          hints:
            - Docker login from config mode, will use the default config from your local machine.
          options:
            - Use existing pull secret
            - No authentication
            - UserName/Password
          default: No authentication
          answer: No authentication
        - id: move2kube.target.clustertype
          type: Select
          description: 'Choose the cluster type:'
          hints:
            - Choose the cluster type you would like to target
          options:
            - AWS-EKS
            - Azure-AKS
            - GCP-GKE
            - IBM-IKS
            - IBM-Openshift
            - Kubernetes
            - Openshift
          default: Kubernetes
          answer: Kubernetes
        - id: move2kube.target.ingress.host
          type: Input
          description: Provide the ingress host domain
          hints:
            - Ingress host domain is part of service URL
          default: myproject.com
          answer: myproject.com
        - id: move2kube.target.ingress.tls
          type: Input
          description: Provide the TLS secret for ingress
          hints:
            - Leave empty to use http
          default: ""
          answer: ""
        - id: move2kube.services."myproject-java-war"."9080".urlpath
          type: Input
          description: What URL/path should we expose the service myproject-java-war's 9080 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /myproject-java-war
          answer: /myproject-java-war
        - id: move2kube.services."java-maven"."9080".urlpath
          type: Input
          description: What URL/path should we expose the service java-maven's 9080 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /java-maven
          answer: /java-maven
        - id: move2kube.services."java-gradle"."9080".urlpath
          type: Input
          description: What URL/path should we expose the service java-gradle's 9080 port on?
          hints:
            - Enter :- not expose the service
            - Leave out leading / to use first part as subdomain
            - Add :N as suffix for NodePort service type
            - Add :L for Load Balancer service type
          default: /java-gradle
          answer: /java-gradle
```

The cache file contains both the questions and the answers along with additional information about each question, such as the default answer, the type of the question, the question IDs, any hints that were provided, etc.

The config file stores the answer to a question under the key specified by the question's ID.  

For example, the question `What URL/path should we expose the service java-maven's 9080 port on?` has the id `move2kube.services."java-maven"."9080".urlpath`.
So we find the answer in the config file `/java-maven` stored as:
```yaml
    move2kube:
      services:
        java-maven:
          "9080":
            urlpath: /java-maven
```
Every time Move2Kube goes to ask a question, it first checks the config file to see if it has already been answered using the question ID. If the ID is not present in the config file, Move2Kube will usually ask the user for the answer meaning the answer to any question can be provided by storing it in the config file.

4. Run the transform again with the generated config file.

```console
    $ mv myproject old # rename the output directory from the previous run to avoid conflicts
    $ ls
    m2k.plan	m2kconfig.yaml	m2kqacache.yaml	old		language-platforms
    $ move2kube transform --config m2kconfig.yaml
    INFO[0000] Detected a plan file at path /Users/user/Desktop/tutorial/m2k.plan. Will transform using this plan.
    INFO[0000] Starting Plan Transformation                 
    ...
    INFO[0007] Plan Transformation done                     
    INFO[0007] Transformed target artifacts can be found at [/Users/user/Desktop/tutorial/myproject].
    $ ls
    m2k.plan	m2kconfig.yaml	m2kqacache.yaml	myproject	old		language-platforms
```
This time Move2Kube did not ask any questions because all of the answers were provided by editing the config file directly to change the answer to a question. Some answers can be removed from the config file to prompt Move2Kube to ask those questions again. This provides a convenient way to iterate quickly, as well as a way to run Move2Kube non-interatively.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Move2Kube/Tutorials/runNonInteractively.md)