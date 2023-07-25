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
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.25.0/install.sh | bash -s v0.25.0

```

## Installing Konveyor Operator
Operators are a structural layer that manages resources deployed on Kubernetes (database, front end, back end) to automatically create a Konveyor instance instead of doing it manually.

Follow the steps below to install the Konveyor Operator in the `my-konveyor-operator` namespace (default) on any Kubernetes distribution, including minikube.

**Procedure**
{{< tabs >}}
{{% tab name="Latest Release" %}}
1. Install the latest released Konveyor Operator.
```
[user@user ~]$ kubectl create -f https://operatorhub.io/install/konveyor-operator.yaml
```
This step will create the `my-konveyor-operator` namespace, catalogsource and other OLM related objects.
{{% /tab %}}
{{% tab name="Alpha Release" %}}
### Installing the alpha version

If you need to deploy a latest alpha release build please use the below url

```
[user@user ~]$ kubectl create -f https://operatorhub.io/install/alpha/konveyor-operator.yaml
```
{{% /tab %}}
{{% tab name="Development" %}}
### Installing the latest version

If you need to deploy a latest available build please follow the steps below,
* Create a namespace
```
apiVersion: v1
kind: Namespace
metadata:
  name: my-konveyor-operator
```
* Create a custom CatalogSource
```
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
  name: konveyor
  namespace: my-konveyor-operator
spec:
  displayName: Konveyor Operator
  publisher: Konveyor
  sourceType: grpc
  image: quay.io/konveyor/tackle2-operator-index:latest
```

* Create an operator group and Subscription using the custom Catalog Source
```
---
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  name: konveyor
  namespace: my-konveyor-operator
spec:
  targetNamespaces:
    - my-konveyor-operator
---
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: konveyor-operator
  namespace: my-konveyor-operator
spec:
  channel: development
  installPlanApproval: Automatic
  name: konveyor-operator
  source: konveyor
  sourceNamespace: my-konveyor-operator
```

_Note: Latest builds are built nightly. It strictly for developmental purpose and not to be used in production._

{{% /tab %}}
{{< /tabs >}}
2. Verify Konveyor was installed.
```
[user@user ~]$ kubectl get pods -n my-konveyor-operator 
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
  namespace: my-konveyor-operator
spec:
  rwx_supported: "false"
  feature_auth_required: "false"
EOF
```
2. Verify the instance
```
[user@user ~]$ kubectl get pods -n my-konveyor-operator
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
2. Ensure the top dropdown namespace selector is set to the `my-konveyor-operator`
3. Click **Service** then **Ingresses**
4. Click the endpoint IP for the `tackle` ingress ingress to launch the Konveyor web console in a new browser window.
> **Note:** This may default to `http://$IP_ADDR` and fail to load, if so switch to `https://$IP_ADDR`
> In some cases, it could still fail to load, open the terminal and type in `kubectl port-forward service/tackle-ui 8080:8080 -n my-konveyor-operator` and then access the webpage by visiting `localhost:8080`
5. The default auth enabled credentials are: `admin`/`Passw0rd!`

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Konveyor/installation.md)
