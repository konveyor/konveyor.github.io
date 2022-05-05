---
title: "Installation"
date: 2022-05-03T11:00:46-06:00
draft: true
---

## Installing Tackle
This section provides the necessary steps needed to install Tackle.

### Installing the Tackle Operator
Follow these steps to download and install the Tackle Operator on an Enterprise Kubernetes Platform cluster.

**Prerequisites**
* Cluster-admin privileges.

#### Procedure
Install the Tackle Operator:
```
$ kubectl create -f https://operatorhub.io/install/tackle-operator.yaml
```
The Tackle Operator is installed in the my-tackle-operator namespace by default.

Verify the Tackle Operator installation by viewing its resources:
```
$ kubectl get all -n my-tackle-operator

```
### Installing the Tackle application
Follow these steps to install Tackle in a namespace by creating an instance of the Tackle application.

**Prerequisites**
* Tackle Operator installed on the cluster.
* Project-admin privileges.

#### Procedure
Create an instance of the Tackle application, specifying its namespace:
```
$ kubectl apply -n <namespace> -f https://raw.githubusercontent.com/konveyor/tackle-operator/main/src/main/resources/k8s/tackle/tackle.yaml
```
**Note:** Multiple instances of the Tackle application can be created in the same namespace by specifying a unique name for each instance in the tackle.yaml file.

2. Open the Kubernetes dashboard
3. Click Workloads then  Deployments to verify the installation.

### Logging into the Tackle web console
Follow these steps to log into the Tackle web console.

**Prerequisites**
* Tackle application installed.

#### Procedure
1. Open  the Kubernetes dashboard.
2. Click Services then Ingresses.
3. Click the Endpoint of the tackle-sample ingress to launch the Tackle web console in a new browser window.
4. Enter tackle in the Username or email field, password in the Password field, and click Log in.

**Important:** Change the default password of the tackle user.
