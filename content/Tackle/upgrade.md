---
title: "Upgrading Tackle"
date: 2022-05-05T18:47:38-06:00
draft: false
---
Tackle application instances are upgraded manually.

## Upgrading from version 1.1.0 to 1.2.0
Follow the steps below to manually upgrade an instance of the Tackle application from 1.1.0 to 1.2.0.

**Prerequisites**

Project administrator privileges.

**Procedure**

Specify the namespace and the Tackle instance name for each step.

1. Update the Keycloak deployment of the Tackle instance:
```
$ kubectl set image -n <namespace> deployment/<tackle_instance>-keycloak keycloak-theme=quay.io/konveyor/tackle-keycloak-init:1.2.0
Update the application-inventory-rest deployment:

$ kubectl set image -n <namespace> deployment/<tackle_instance>-application-inventory-rest \
  <tackle_instance>-application-inventory-rest=quay.io/konveyor/tackle-application-inventory:1.2.0-native
```
2. Update the controls-rest deployment:
```
$ kubectl set image -n <namespace> deployment/<tackle_instance>-controls-rest \
  <tackle_instance>-controls-rest=quay.io/konveyor/tackle-controls:1.2.0-native
Update the pathfinder-rest deployment:

$ kubectl set image -n <namespace> deployment/<tackle_instance>-pathfinder-rest \
  <tackle_instance>-pathfinder-rest=quay.io/konveyor/tackle-pathfinder:1.2.0-native
```
3. Update the UI deployment:
```
$ kubectl set image -n <namespace> deployment/<tackle_instance>-ui \
  <tackle_instance>-ui=quay.io/konveyor/tackle-ui:1.2.0
```
4. Log in to the web console and click the **Help** icon beside the user name to verify the upgrade.

The About Tackle window opens and displays the version number.

## Upgrading from version 1.0.0 to 1.1.0
Follow the steps below to manually upgrade an instance of the Tackle application from 1.0.0 to 1.1.0.

**Prerequisites**

Project administrator privileges.

**Procedure**

Specify the namespace and the Tackle instance name for each step.

1. Update the Keycloak deployment of the Tackle instance:

```
$ kubectl -n <namespace> exec deployments/<tackle_instance>-keycloak \
  -c <tackle_instance>-keycloak -- bash -c '/opt/jboss/keycloak/bin/kcadm.sh \
  update realms/tackle -s internationalizationEnabled=true -s supportedLocales+=en \
  -s supportedLocales+=es -s defaultLocale=en --server http://localhost:8080/auth \
  --realm master --user $KEYCLOAK_USER --password $KEYCLOAK_PASSWORD'
```

2. Update the application-inventory-rest deployment:
```
$ kubectl set image -n <namespace> deployment/<tackle_instance>-application-inventory-rest \
  <tackle_instance>-application-inventory-rest=quay.io/konveyor/tackle-application-inventory:1.1.0-native
```
3. Update the controls-rest deployment:
```
$ kubectl set image -n <namespace> deployment/<tackle_instance>-controls-rest \
  <tackle_instance>-controls-rest=quay.io/konveyor/tackle-controls:1.1.0-native
Update the pathfinder-rest deployment:

$ kubectl set image -n <namespace> deployment/<tackle_instance>-pathfinder-rest \
  <tackle_instance>-pathfinder-rest=quay.io/konveyor/tackle-pathfinder:1.1.0-native
```
4.Update the UI deployment:
```
$ kubectl set image -n <namespace> deployment/<tackle_instance>-ui \
  <tackle_instance>-ui=quay.io/konveyor/tackle-ui:1.1.0
```
5. Log in to the web console and click the **Help** icon beside the user name to verify the upgrade.

The About Tackle window opens and displays the version number.
