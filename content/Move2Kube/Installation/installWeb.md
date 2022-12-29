---
title: "Web Interface"
date: 2022-08-03T15:23:24-06:00
draft: false
---
Minikube can be installed using Docker or Podman web interfaces.

## Installing Move2Kube using Docker
Follow the steps below to install Move2Kube with options of persistence by mounting to the current directory, and advanced features by mounting to the Docker socket allowing Move2Kube to run container based transformers.

Move2Kube can also be installed as a Helm Chart from [ArtifactHub](https://artifacthub.io/packages/helm/move2kube/move2kube/0.3.0?modal=install).  For more information on Helm Chart and Operator see [Move2Kube Operator](https://github.com/konveyor/move2kube-operator)

**Procedure**
.
>**Note** For bleeding edge features, development, and testing, follow the steps below, but replace `v0.3.0` with `latest`.

1. Install Move2Kube.

```
$ docker run --rm -it -p 8080:8080 quay.io/konveyor/move2kube-ui:v0.3.0
```

2. Set persistence (optional).
```
$ docker run --rm -it -p 8080:8080 \
   -v "${PWD}/move2kube-api-data:/move2kube-api/data" \
   quay.io/konveyor/move2kube-ui:v0.3.0
```
3. Set advanced features (optional).
```
$ docker run --rm -it -p 8080:8080 \
   -v "${PWD}/move2kube-api-data:/move2kube-api/data" \
   -v //var/run/docker.sock:/var/run/docker.sock \
   quay.io/konveyor/move2kube-ui:v0.3.0
```

## Installing Move2Kube using Podman
* Run the command below to install Move2Kube.

```
$ podman run --rm -it -p 8080:8080 quay.io/konveyor/move2kube-ui:v0.3.0
```

## Accessing the Move2Kube UI
Open a Web browser and navigate to http://localhost:8080/ to access the UI.

> **Note: There is a known issue when mounting directories in WSL. Some empty directories may be created in the root directory.  If using Windows, use Powershell instead of WSL until this is fixed.
