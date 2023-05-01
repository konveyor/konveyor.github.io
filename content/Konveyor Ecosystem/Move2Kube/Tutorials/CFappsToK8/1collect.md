---
layout: default
title: "1. Collect"
draft: false
permalink: /tutorials/migration-workflow/collect
parent: "Migrating Enterprise Scale Cloud Foundry Apps to Kubernetes"
grand_parent: Tutorials
weight: 1
---

> **Note:** This is an optional step. If you are not familiar with Cloud Foundry or you do not want to collect information from the running app, skip to Customizing the output.

To analyze the running application in Cloud Foundry (CF), the Move2Kube CLI tool provides a command called `collect`. As the name suggests, it collects information about applications running in the cloud.

* For collecting information from a CF running instance, consider requiring `cf` CLI for logging into Cloud Foundry. To target a specific Kubernetes cluster for the YAMLs, either `oc` or `kubectl` to collect information about the target cluster is necessary.

* If logged into the Cloud Foundry instance, information about the apps such as environment variables, services, and more are collected. If logged into Kubernetes clusters, it collects information about the types of resources that are installed on the cluster, such as whether it has Tekton, BuildConfigs, etc.

* All the information that was collected gets written into a directory called `m2k_collect` as YAML files. In this case, the info about Cloud Foundry apps is written to a sub-directory called `cf`. These YAMLs can then be used during the plan phase to get a holistic plan combining the source and metadata.

For example: Some of the information that is collected is port and environment variable information. This allows Move2Kube to select the right ports and set the right environment variables for each service when generating Dockerfiles for containerizing them.

## Collecting info from e2e-demo app

**Prerequisites**

* The `cf` tool installed.
* Logged into the Cloud Foundry instance. Run `cf target` to verify. The output should be similar to this:

```console
$ cf target
API endpoint:   https://api.cf.my.cloud.provider.com
API version:    3.107.0
user:           user@gmail.com
org:            my-org
space:          dev
```

* The [enterprise-app](https://github.com/konveyor/move2kube-demos/tree/main/samples/enterprise-app) app in the Cloud Foundry instance is deployed.

```console
$ cf apps

Getting apps in org my-org / space dev as user@gmail.com...

name          requested state   processes           routes
frontend      started           web:1/1             frontend-1234.my.cloud.provider.com
gateway       started           web:1/1, task:0/0   gateway-5678.my.cloud.provider.com
orders        started           web:1/1, task:0/0   orders-1234.my.cloud.provider.com
...
```
**Procedure**

1. Run `move2kube collect` to collect information about the app from Cloud Foundry.
```console
$ move2kube collect
INFO[0000] Begin collection                             
INFO[0000] [*collector.ClusterCollector] Begin collection
INFO[0000] [*collector.ClusterCollector] Done           
INFO[0000] [*collector.ImagesCollector] Begin collection
INFO[0000] [*collector.ImagesCollector] Done            
INFO[0000] [*collector.CfAppsCollector] Begin collection
INFO[0011] [*collector.CfAppsCollector] Done            
INFO[0011] [*collector.CfServicesCollector] Begin collection
INFO[0026] [*collector.CfServicesCollector] Done        
INFO[0026] Collection done                              
INFO[0026] Collect Output in [/Users/user/Desktop/tutorial/m2k_collect]. Copy this directory into the source directory to be used for planning.
```

The output will be in a directory called `m2k_collect` with a sub-directory called `cf` containing two YAML files: `CfApps` and `CfServices`.

```console
$ ls m2k_collect/
cf		clusters	images
$ ls m2k_collect/cf/
cfapps-e3a2f9d68a7a5ecc.yaml		cfservices-32194c9906854947.yaml
```
The `CfApps` file contains all the information that was collected about the app such as service names, environment variables, ports, etc. An example is provided [here](https://github.com/konveyor/move2kube-demos/blob/main/samples/enterprise-app/collect/cf/cfapps.yaml)

```console
<details markdown="block">
<summary markdown="block">
# click to see the full yaml
apiVersion: move2kube.konveyor.io/v1alpha1
kind: CfApps
......
```
The returned YAML.
```yaml
apiVersion: move2kube.konveyor.io/v1alpha1
kind: CfApps
spec:
  applications:
    - application:
    guid: id1
    createdat: "2021-12-14T10:01:40Z"
    updatedat: "2021-12-14T10:03:08Z"
    name: orders
    memory: 1024
    instances: 1
```
{{%expand "Click to see the rest of the yaml."%}}
```yaml
    diskquota: 1024
    spaceguid: space-id1
    stackguid: stack-id1
    state: STARTED
    packagestate: STAGED
    command: ""
    buildpack: https://github.com/cloudfoundry/java-buildpack
    detectedbuildpack: java
    detectedbuildpackguid: ""
    healthcheckhttpendpoint: ""
    healthchecktype: port
    healthchecktimeout: 0
    diego: true
    enablessh: true
detectedstartcommand: 'JAVA_OPTS="-agentpath:$PWD/.java-buildpack/open_jdk_jre/bin/jvmkill-1.16.0_RELEASE=printHeapHistogram=1 -Djava.io.tmpdir=$TMPDIR -XX:ActiveProcessorCount=$(nproc) -Djava.ext.dirs=$PWD/.java-buildpack/container_security_provider:$PWD/.java-buildpack/open_jdk_jre/lib/ext -Djava.security.properties=$PWD/.java-buildpack/java_security/java.security $JAVA_OPTS" && CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-3.13.0_RELEASE -totMemory=$MEMORY_LIMIT -loadedClasses=23193 -poolType=metaspace -stackThreads=250 -vmOptions="$JAVA_OPTS") && echo JVM Memory Configuration: $CALCULATED_MEMORY && JAVA_OPTS="$JAVA_OPTS $CALCULATED_MEMORY" && MALLOC_ARENA_MAX=2 SERVER_PORT=$PORT eval exec $PWD/.java-buildpack/open_jdk_jre/bin/java $JAVA_OPTS -cp $PWD/. org.springframework.boot.loader.JarLauncher'
    dockerimage: ""
    dockercredentialsjson: {}
    dockercredentials:
      username: ""
      password: ""
    environment: {}
    stagingfailedreason: ""
    stagingfaileddescription: ""
    ports:
      - 8080
    spaceurl: /v2/spaces/space-id1
    spacedata:
      meta:
        guid: space-id1
        url: /v2/spaces/space-id1
        createdat: "2020-10-05T05:29:46Z"
        updatedat: "2020-10-05T05:29:46Z"
      entity:
        guid: space-id1
        createdat: ""
        updatedat: ""
        name: dev
        organizationguid: org-id1
        orgurl: /v2/organizations/org-id1
        orgdata:
          meta:
            guid: org-id1
            url: /v2/organizations/org-id1
            createdat: "2020-10-05T05:29:31Z"
            updatedat: "2020-10-05T05:29:31Z"
          entity:
            guid: org-id1
            createdat: ""
            updatedat: ""
            name: org-name
            status: active
            quotadefinitionguid: quota-id
            defaultisolationsegmentguid: ""
        quotadefinitionguid: ""
        isolationsegmentguid: ""
        allowssh: true
    packageupdatedat: "2021-12-14T10:01:49Z"
  environment:
    environment: {}
    stagingenv:
      BLUEMIX_REGION: region
    runningenv:
      BLUEMIX_REGION: region
    systemenv:
      VCAP_SERVICES: '{}'
    applicationenv:
      VCAP_APPLICATION: 
'{"application_id":"id1","application_name":"orders","application_uris":["orders-proud-bilby-rf.net"],"application_version":"app-ver1","cf_api":"app-url","limits":{"disk":1024,"fds":16384,"mem":1024},"name":"orders","organization_id":"org-id1","organization_name":"org-name","process_id":"id1","process_type":"web","space_id":"space-id1","space_name":"dev","uris":["orders-proud-bilby-rf.net"],"users":null,"version":"app-ver1"}'
  - application:
      guid: id2
      createdat: "2021-12-14T10:04:00Z"
      updatedat: "2021-12-14T10:05:43Z"
      name: gateway
      memory: 1024
      instances: 1
      diskquota: 1024
      spaceguid: space-id1
      stackguid: stack-id1
      state: STARTED
      packagestate: STAGED
      command: ""
      buildpack: https://github.com/cloudfoundry/java-buildpack
      detectedbuildpack: java
      detectedbuildpackguid: ""
      healthcheckhttpendpoint: ""
      healthchecktype: port
      healthchecktimeout: 0
      diego: true
      enablessh: true
      detectedstartcommand: 
'JAVA_OPTS="-agentpath:$PWD/.java-buildpack/open_jdk_jre/bin/jvmkill-1.16.0_RELEASE=printHeapHistogram=1 -Djava.io.tmpdir=$TMPDIR -XX:ActiveProcessorCount=$(nproc) -Djava.ext.dirs=$PWD/.java-buildpack/container_security_provider:$PWD/.java-buildpack/open_jdk_jre/lib/ext -Djava.security.properties=$PWD/.java-buildpack/java_security/java.security $JAVA_OPTS" && CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-3.13.0_RELEASE -totMemory=$MEMORY_LIMIT -loadedClasses=24458 -poolType=metaspace -stackThreads=250 -vmOptions="$JAVA_OPTS") && echo JVM Memory Configuration: $CALCULATED_MEMORY && JAVA_OPTS="$JAVA_OPTS $CALCULATED_MEMORY" && MALLOC_ARENA_MAX=2 SERVER_PORT=$PORT eval exec $PWD/.java-buildpack/open_jdk_jre/bin/java $JAVA_OPTS -cp $PWD/. org.springframework.boot.loader.JarLauncher'
      dockerimage: ""
      dockercredentialsjson: {}
      dockercredentials:
        username: ""
        password: ""
      environment: {}
      stagingfailedreason: ""
      stagingfaileddescription: ""
      ports:
        - 8080
      spaceurl: /v2/spaces/space-id1
      spacedata:
        meta:
          guid: space-id1
          url: /v2/spaces/space-id1
          createdat: "2020-10-05T05:29:46Z"
          updatedat: "2020-10-05T05:29:46Z"
        entity:
          guid: space-id1
          createdat: ""
          updatedat: ""
          name: dev
          organizationguid: org-id1
          orgurl: /v2/organizations/org-id1
          orgdata:
            meta:
              guid: org-id1
              url: /v2/organizations/org-id1
              createdat: "2020-10-05T05:29:31Z"
              updatedat: "2020-10-05T05:29:31Z"
            entity:
              guid: org-id1
              createdat: ""
              updatedat: ""
              name: org-name
              status: active
              quotadefinitionguid: quota-id
              defaultisolationsegmentguid: ""
          quotadefinitionguid: ""
          isolationsegmentguid: ""
          allowssh: true
      packageupdatedat: "2021-12-14T10:04:09Z"
    environment:
      environment: {}
      stagingenv:
        BLUEMIX_REGION: region
      runningenv:
        BLUEMIX_REGION: region
      systemenv:
        VCAP_SERVICES: '{}'
      applicationenv:
        VCAP_APPLICATION: 
'{"application_id":"id2","application_name":"gateway","application_uris":["gateway-restless-fossa-ws.net"],"application_version":"app-ver2","cf_api":"app-url","limits":{"disk":1024,"fds":16384,"mem":1024},"name":"gateway","organization_id":"org-id1","organization_name":"org-name","process_id":"id2","process_type":"web","space_id":"space-id1","space_name":"dev","uris":["gateway-restless-fossa-ws.net"],"users":null,"version":"app-ver2"}'
  - application:
      guid: id3
      createdat: "2021-12-14T14:54:25Z"
      updatedat: "2021-12-14T15:15:38Z"
      name: frontend
      memory: 1024
      instances: 1
      diskquota: 1024
      spaceguid: space-id1
      stackguid: stack-id1
      state: STARTED
      packagestate: STAGED
      command: npm run start
      buildpack: https://github.com/cloudfoundry/nodejs-buildpack
      detectedbuildpack: nodejs
      detectedbuildpackguid: ""
      healthcheckhttpendpoint: ""
      healthchecktype: port
      healthchecktimeout: 0
      diego: true
      enablessh: true
      detectedstartcommand: npm start
      dockerimage: ""
      dockercredentialsjson: {}
      dockercredentials:
        username: ""
        password: ""
      environment: {}
      stagingfailedreason: ""
      stagingfaileddescription: ""
      ports:
        - 8080
      spaceurl: /v2/spaces/space-id1
      spacedata:
        meta:
          guid: space-id1
          url: /v2/spaces/space-id1
          createdat: "2020-10-05T05:29:46Z"
          updatedat: "2020-10-05T05:29:46Z"
        entity:
          guid: space-id1
          createdat: ""
          updatedat: ""
          name: dev
          organizationguid: org-id1
          orgurl: /v2/organizations/org-id1
          orgdata:
            meta:
              guid: org-id1
              url: /v2/organizations/org-id1
              createdat: "2020-10-05T05:29:31Z"
              updatedat: "2020-10-05T05:29:31Z"
            entity:
              guid: org-id1
              createdat: ""
              updatedat: ""
              name: org-name
              status: active
              quotadefinitionguid: quota-id
              defaultisolationsegmentguid: ""
          quotadefinitionguid: ""
          isolationsegmentguid: ""
          allowssh: true
      packageupdatedat: "2021-12-14T14:59:40Z"
    environment:
      environment: {}
      stagingenv:
        BLUEMIX_REGION: region
      runningenv:
        BLUEMIX_REGION: region
      systemenv:
        VCAP_SERVICES: '{}'
      applicationenv:
        VCAP_APPLICATION: 
'{"application_id":"id3","application_name":"frontend","application_uris":["frontend-patient-oryx-mc.net"],"application_version":"app-ver3","cf_api":"app-url","limits":{"disk":1024,"fds":16384,"mem":1024},"name":"frontend","organization_id":"org-id1","organization_name":"org-name","process_id":"id3","process_type":"web","space_id":"space-id1","space_name":"dev","uris":["frontend-patient-oryx-mc.net"],"users":null,"version":"app-ver3"}'
```
{{% /expand%}}

Now that the runtime information has been collected from the app running in the Cloud Foundry instance, it can be used during the planning phase by simply copying it into the source directory before starting the planning. All the steps are the same as the Plan step.

## Next steps

Next is customizing the output that Move2Kube produces using customizations.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Move2Kube/Tutorials/CFappsToK8/1collect.md)