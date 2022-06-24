---
title: "Installing Tackle 2.0"
date: 2022-06-14T14:59:30-06:00
draft: false
---

Follow the steps below to provision the Minikube cluster and install Tackle 2.0.  

## Provisioning Minikube
Follow the steps below to provision Minikube for single users deploying Tackle on a workstation. These steps will configure Minikube and enable:
* Ingress so the Tackle tool can publish outside of the Kubernetes cluster.
* Operator lifecycle manager (OLM) addon. (OpenShift has OLM installed out of the box but Kubernetes does not.)

#### Procedure
1. Provision the Minikube cluster with these recommended parameters.
```
[user@user ~]$ Minikube start -- driver=kvm3  -p <project name> --memory=10g
```
2. Enable the ingress addon.
```
[user@user ~]$ Minikube addons enable ingress -p docs
```
3. Enable the operator lifecycle manager (OLM) addon.
```
[user@user ~]$ Minikube addons enable olm -p docs
```

## Installing Tackle Operator
Operators are a structural layer that manages resources deployed on Kubernetes (database, front end, back end) to automatically create a Tackle instance instead of doing it manually.

### Requirements
Tackle requires a total of 5 persistent volumes (PVs) used by different components to successfully deploy, 3 RWO volumes and 2 RWX volumes will be requested via PVCs.

|Name|Default Size|Access Mode|Description|
|--|--|--|--|
|hub database|5Gi|RWO|Hub DB|
|hub bucket|100Gi|RWX|Hub file storage|
|keycloak postgresql|1Gi|RWO|Keycloak backend DB|
|pathfinder postgresql|1Gi|RWO|Pathfinder backend DB|
|maven|100Gi|RWX|maven m2 repository|

Follow the steps below to install the Tackle Operator in the my-tackle-operator namespace (default) on any Kubernetes distribution, including Minikube.

#### Procedure
1. Install the Tackle Operator.
```
[user@user ~]$ $ kubectl create -f https://operatorhub.io/install/tackle-operator.yaml
```
2. Verify Tackle was installed.
```
[user@user ~]$ kubectl get pods -n my-tackle-operator
```
3. Repeat this step until konveyor-tackle-XXX and tackle-operator-XXX show 1/1 Running.

## Create the Tackle instance
Follow the steps below to initiate the Tackle instance and set a custom resource (CR) with the tackle_hub.yaml file. CRs can be customized to meet the project needs.
```
    $ cat << EOF | kubectl apply -f -
    kind: Tackle
    apiVersion: tackle.konveyor.io/v1alpha1
    metadata:
      name: tackle
      namespace: <your-tackle-namespace>
    spec:
    EOF
```
**Note:** For more information about altering the operator defaults, see the Tackle CR Settings section.

#### Procedure
1. Create the instance pointing to the CR file.
```
[user@user ~]$ Kubectl create -f tackle_hub.yaml -n my-tackle-operator
```
2. Verify the instance
```
[user@user ~]$ kubectl get pods -n my-tackle-operator
```
3. Repeat this step until all components are Completed or Running.

**Note:** This can take one to five minutes depending on the cluster resources.

### Logging into Tackle with auth enabled
The default auth enabled credentials are: admin/password

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/Tackle2/installation.md)
