---
title: "Installing Konveyor"
date: 2022-06-14T14:59:30-06:00
draft: false
---

Follow the steps below to provision the minikube cluster and install Konveyor.  

## Provisioning Minikube
Follow the steps below to provision minikube for single users deploying Konveyor on a workstation. These steps will configure minikube and enable:
* Ingress so the Konveyor tool can publish outside of the Kubernetes cluster.
* Operator lifecycle manager (OLM) addon. 

**Procedure**
1. Provision the minikube cluster with these recommended parameters. Replace `<profile name>` with your choice of minikube profile name.
```
[user@user ~]$ minikube start --memory=10g -p <profile name>
```
2. Enable the ingress addon.
```
[user@user ~]$ minikube addons enable ingress -p <profile name>
```
3. Install Operator Lifecycle Manager (OLM), a tool to help manage the Operators running on your cluster.
```
[user@user ~]$ curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.21.2/install.sh | bash -s v0.21.2
```

## Installing Konveyor Operator
Operators are a structural layer that manages resources deployed on Kubernetes (database, front end, back end) to automatically create a Konveyor instance instead of doing it manually.

**Requirements**
Konveyor requires a total of 5 persistent volumes (PVs) used by different components to successfully deploy, 3 RWO volumes and 2 RWX volumes will be requested via PVCs.

|Name|Default Size|Access Mode|Description|
|--|--|--|--|
|hub database|5Gi|RWO|Hub DB|
|hub bucket|100Gi|RWX|Hub file storage|
|keycloak postgresql|1Gi|RWO|Keycloak backend DB|
|pathfinder postgresql|1Gi|RWO|Pathfinder backend DB|
|maven|100Gi|RWX|maven m2 repository|

Follow the steps below to install the Konveyor Operator in the konveyor-tackle namespace (default) on any Kubernetes distribution, including minikube.

**Procedure**
1. Install the Konveyor Operator.
```
[user@user ~]$ kubectl apply -f https://raw.githubusercontent.com/konveyor/tackle2-operator/main/tackle-k8s.yaml
```
This step will create the konveyor-tackle namespace, catalogsource and other OLM related objects.

### Installing _beta_ (or special branches)

If you need to deploy a beta release (or special branch) please replace the *main* branch in URL with the desired beta branch (i.e v2.0.0-beta.0):

`$ kubectl apply -f https://raw.githubusercontent.com/konveyor/tackle2-operator/<release>-beta.0/tackle-k8s.yaml`

**Note:** Upgrades between beta releases are **not guaranteed** , once installed, we strongly suggest to edit your subscription and switch to Manual upgrade mode for beta releases: `$ kubectl edit subscription` -> installPlanApproval: Manual

2. Verify Konveyor was installed.
```
[user@user ~]$ kubectl get pods -n konveyor-tackle
```
3. Repeat this step until konveyor-tackle-XXX and tackle-operator-XXX show 1/1 Running.

## Create the Tackle instance
Follow the steps below to initiate the Tackle instance and set a custom resource (CR) with the tackle_hub.yaml file. CRs can be customized to meet the project needs.

**Procedure**
1. Create the instance pointing to the CR file.
```
[user@user ~]$ cat << EOF | kubectl apply -f -
kind: Tackle
apiVersion: tackle.konveyor.io/v1alpha1
metadata:
  name: tackle
  namespace: konveyor-tackle
spec:
  rwx_supported: "false"
  feature_auth_required: "false"
EOF
```
2. Verify the instance
```
[user@user ~]$ kubectl get pods -n konveyor-tackle
```
3. Repeat this step until all components are Completed or Running.

> **Note:** This can take one to five minutes depending on the cluster resources.

## Logging in to Konveyor UI 
Follow the steps below to log in to the Konveyor web console.

**Procedure**
1. Access the minikube dashboard. This will enable the dashboard add-on, and open the proxy in the default web browser.
```
[user@user ~]$ minikube dashboard -p <profile name>
```
2. Ensure the top dropdown namespace selector is set to the `konveyor-tackle`
3. Click **Service** then **Ingresses**
4. Click the endpoint IP for the `tackle` ingress ingress to launch the Konveyor web console in a new browser window.
> **Note:** This may default to `http://$IP_ADDR` and fail to load, if so switch to `https://$IP_ADDR`
5. The default auth enabled credentials are: `admin`/`Passw0rd!`

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Konveyor/installation.md)
