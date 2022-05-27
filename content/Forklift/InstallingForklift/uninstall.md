---
title: "Uninstalling Forklift"
date: 2022-05-17T14:16:26-06:00
draft: false
---
Uninstall Forklift using the OKD web console or the command line interface (CLI).

## Uninstalling Forklift with the OKD web console
Follow the steps below to uninstall Forklift using the OKD web console to delete the konveyor-forklift project and custom resource definitions (CRDs).

### Prerequisites
Log in as a user with cluster-admin privileges.

### Procedure
1. Click **Home** and then **Projects**.
2. Locate the konveyor-forklift project.
3. Select **Delete Project** from the **Options** menu kebab on the right side of the project.
4. Navigate to the Delete Project pane, enter the project name, and then click **Delete**.
5. Click **Administration** and then **CustomResourceDefinitions**.
6. Type **forklift** in the **Search** field to locate the CRDs in the forklift.konveyor.io group.
7. Select **Delete CustomResourceDefinition** from the **Options** menu kebab in the right side of each CRD.

## Uninstalling Forklift with the command line interface
Follow the steps below to uninstall Forklift using the command line interface (CLI) by deleting the konveyor-forklift project and the forklift.konveyor.io custom resource definitions (CRDs).

### Prerequisites
Log in as a user with cluster-admin privileges.

### Procedure
1. Delete the project:
```
$ kubectl delete project konveyor-forklift
```
2. Delete the CRDs:
```
$ kubectl get crd -o name | grep 'forklift' | xargs kubectl delete
```
3. Delete the OAuthClient:
```
$ kubectl delete oauthclient/forklift-ui
```

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Forklift/InstallingForklift/uninstall.md)
