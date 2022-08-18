---
title: "Installing Tackle 1.0"
date: 2022-05-03T11:00:46-06:00
draft: false
---
Follow the steps below to provision the minikube cluster and install Tackle 1.2.  

## Provisioning Minikube
Follow the steps below to provision minikube for single users deploying Tackle on a workstation. These steps will configure minikube and enable:
* Ingress so the Tackle tool can publish outside of the Kubernetes cluster.
* Operator lifecycle manager (OLM) addon. (OpenShift has OLM installed out of the box but Kubernetes does not.)

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


## Installing the Tackle Operator
Follow the steps below to download and install the Tackle Operator on an Enterprise Kubernetes Platform cluster.

**Prerequisites**

* Cluster-admin privileges. This can be minikube

**Procedure**

1. Install the Tackle Operator:

```
[user@user ~]$ kubectl create -f https://operatorhub.io/install/tackle-operator.yaml
```
The Tackle Operator is installed in the `my-tackle-operator` namespace by default.

2. Verify the Tackle Operator installation by viewing its resources:

```
[user@user ~]$ kubectl get all -n my-tackle-operator
```

## Installing the Tackle application
Follow the steps below to install Tackle in a namespace by creating an instance of the Tackle application.

**Prerequisites**

* Tackle Operator installed on the cluster.
* Project-admin privileges.

**Procedure**
1. Create an instance of the Tackle application, specifying its namespace:
```
[user@user ~]$ kubectl apply -f https://raw.githubusercontent.com/konveyor/tackle-operator/main/src/main/resources/k8s/tackle/tackle.yaml -n <namespace>
```
> **Note:** Multiple instances of the Tackle application can be created in the same namespace by specifying a unique name for each instance in the tackle.yaml file.

2. Verify the instance
```
[user@user ~]$ kubectl get pods -n <namespace>
```
3. Repeat this step until all components are Completed or Running.

> **Note:** This can take one to five minutes depending on the cluster resources.

## Logging into the Tackle web console
Follow the steps below to log into the Tackle web console.

**Prerequisites**

* Tackle application installed.

**Procedure**
1. If using minikube, access the minikube dashboard. This will enable the dashboard add-on, and open the proxy in the default web browser. Or navigate to your kubernetes cluster dashboard.
```
[user@user ~]$ minikube dashboard -p <profile name>
```
2. Ensure the top dropdown namespace selector is set to the `<namespace>` you created the Tackle application in.
3. Click **Service** then **Ingresses**
4. Click the endpoint IP for the `tackle-sample` ingress ingress to launch the Tackle web console in a new browser window.
> **Note:** This may default to `http://$IP_ADDR` and fail to load, if so switch to `https://$IP_ADDR`
6. The default auth enabled credentials are: `tackle`/`password`


> **Important:** Change the default password of the tackle user.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/installation1.md)
