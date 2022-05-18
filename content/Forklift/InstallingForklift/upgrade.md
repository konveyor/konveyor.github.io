---
title: "Upgrading Forklift"
date: 2022-05-17T14:36:19-06:00
draft: false
---
Upgrade the Forklift Operator using the OKD web console.

**Important:** Do not skip a release when upgrading.  For example, upgrade 2.0 to 2.1 and then 2.1 to 2.2.

## Procedure
1. Open the OKD web console.
2. Click **Operators**, **Installed Operators**, **Migration Tookit for Virtualization Operator**, then **Subscription**.
3. Change the update channel to the correct release.

See [Changing update channel for an Operator](https://docs.okd.io/latest/operators/admin/olm-upgrading-operators.html#olm-changing-update-channel_olm-upgrading-operators) in the OKD documentation.

4. Confirm that the **Upgrade status** changes from **Up to date** to **Upgrade available**. If it does not, restart the CatalogSource pod:

    a. Note the catalog source, for example, redhat-operators.
    b. Open the command line.
    c. Retrieve the catalog source pod:
```
$ kubectl get pod -n openshift-marketplace | grep <catalog_source>
```
    d. Delete the pod:
```
$ kubectl delete pod -n openshift-marketplace <catalog_source_pod>
```
The **Upgrade status** changes from **Up to date** to **Upgrade available**.

**Note:** Update approval settings on the Subscriptions tab:
* **Automatic:** Starts upgrades automatically.
* **Manual** Forces approval to start the upgrade. See [Manually approving a pending Operator upgrade](https://docs.okd.io/latest/operators/admin/olm-upgrading-operators.html#olm-approving-pending-upgrade_olm-upgrading-operators) in the OKD documentation.

5. Verify that the forklift-ui pod is in a **Ready** state before you log in to the web console:
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
### Forklift 2.2 to 2.3 Upgrade Notes
**VMware source providers in Forklift** If VMware source providers were added to 2.2, upgrading to 2.3 changes the state of any VMware providers to Critical.

**Fix:** Edit the VMware provider by adding a VDDK init image and verifying the certificate of the VMware provider. For more information see [Addding a VMSphere source provider](https://access.redhat.com/documentation/en-us/migration_toolkit_for_virtualization/2.2/html/installing_and_using_the_migration_toolkit_for_virtualization/migrating-vms-web-console#adding-source-provider_vmware).

**NFS Mapping**
If the configuration is mapped to NFS on the OKD destination provider in Forklift 2.2, upgrading to Forklift 2.3 invalidates the NFS mapping.

**Fix:** Edit the AccessModes and VolumeMode parameters in the NFS storage profile. For more information, see [Customizing the storage profile](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.9/html-single/virtualization/index#virt-customizing-storage-profile_virt-creating-data-volumes).
