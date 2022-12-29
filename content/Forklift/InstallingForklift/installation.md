---
title: "Installing the Forklift Operator"
date: 2022-05-11T15:57:06-06:00
draft: false
---

The Forklift Operator can be installed using the OKD web console or the command line interface (CLI).

## Installing the Forklift Operator using the OKD web console
Follow the steps below to install the Forklift Operator using the OKD web console.

**Prerequisites**

* OKD 4.10 installed.
* KubeVirt Operator installed.

**Procedure**
1. Log in with cluster-admin permissions.
2. Open the OKD web console, click **Operators**, then **OperatorHub**.
3. Use the **Filter by keyword** field to search for forklift-operator.

> **Note:** The Forklift Operator is a Community Operator. Red Hat does not support Community Operators.

4. Click **Migration Tookit for Virtualization Operator** and then click **Install**.
5. Click **Install** on the Install Operator page.
6. Click **Operators** then **Installed Operators** to verify that Migration Tookit for Virtualization Operator appears in the konveyor-forklift project with the status Succeeded.
7. Click **Migration Tookit for Virtualization Operator**.
8. Locate the **ForkliftController**, and click **Create Instance** under Provided APIs.
9. Click **Create**.
10. Click **Workloads**, then **Pods** to verify that the Forklift pods are running.
11. Log in to the OKD web console.
12. Click **Networking** then **Routes**.
13. Select the **konveyor-forklift** project in the **Project:** list.

The URL for the forklift-ui service that opens the login page for the Forklift web console is displayed.

14. Click the URL to navigate to the Forklift web console.

### Getting the Forklift web console URL Using OKD
Follow the steps below to retrieve the Forklift web console URL at any time using the OKD web console.

**Prerequisites**

* KubeVirt Operator installed.
* Forklift Operator installed.

**Procedure**
1. Log in with cluster-admin privileges.
2. Log in to the OKD web console.
3. Click **Networking** then **Routes**.
4. Select the **konveyor-forklift** project in the **Project:** list.

The URL for the forklift-ui service that opens the login page for the Forklift web console is displayed.

5. Click the URL to navigate to the Forklift web console.

## Installing the Forklift Operator from the command line interface
Follow the steps below to install the Forklift Operator from the command line interface (CLI).

**Prerequisites**

* OKD 4.10 installed.
* KubeVirt Operator installed.

**Procedure**

1. Log in with cluster-admin permissions.
2. Create the konveyor-forklift project:
```
$ cat << EOF | kubectl apply -f -
apiVersion: project.openshift.io/v1
kind: Project
metadata:
  name: konveyor-forklift
EOF
```
3. Create an OperatorGroup CR called migration.
```
$ cat << EOF | kubectl apply -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  name: migration
  namespace: konveyor-forklift
spec:
  targetNamespaces:
    - konveyor-forklift
EOF
```
4. Create a Subscription CR for the Operator:
```
$ cat << EOF | kubectl apply -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: forklift-operator
  namespace: konveyor-forklift
spec:
  channel: development
  installPlanApproval: Automatic
  name: forklift-operator
  source: community-operators
  sourceNamespace: openshift-marketplace
  startingCSV: "konveyor-forklift-operator.2.3.0"
EOF
```
5. Create a ForkliftController CR:
```
$ cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: ForkliftController
metadata:
  name: forklift-controller
  namespace: konveyor-forklift
spec:
  olm_managed: true
EOF
```
6. Verify that the Forklift pods are running:
```
$ kubectl get pods -n konveyor-forklift
```
Example output:
```
NAME                                  READY  STATUS   RESTARTS  AGE
forklift-controller-788bdb4c69-mw268  2/2    Running  0         2m
forklift-operator-6bf45b8d8-qps9v     1/1    Running  0         5m
forklift-ui-7cdf96d8f6-xnw5n          1/1    Running  0         2m
```
7. Get the Forklift web console URL with the following command:
```
$ kubectl get route virt -n konveyor-forklift \
  -o custom-columns=:.spec.host
```
The URL for the forklift-ui service that opens the login page for the Forklift web console is displayed.

Example output:
```
https://virt-konveyor-forklift.apps.cluster.openshift.com.
```

### Getting the Forklift web console URL Using CLI
Follow the steps below to retrieve the Forklift web console URL at any time using the command line.

1. Enter the following command to get the Forklift web console URL:
```
$ kubectl get route virt -n konveyor-forklift \
  -o custom-columns=:.spec.host
```
The URL for the forklift-ui service that opens the login page for the Forklift web console is displayed.

Example output:
```
https://virt-konveyor-forklift.apps.cluster.openshift.com.
```
2. Launch a browser and navigate to the Forklift web console.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Forklift/InstallingForklift/installation.md)
