---
title: "Migrating virtual machines using the web console"
date: 2022-05-19T10:03:16-06:00
draft: false
---

Migrate virtual machines (VMs) to KubeVirt using the Forklift web console by performing the folloing tasks:
* Adding source and destination providers
* Creating network and storage mapping
* Creating and running a migration plan

**Prerequisites**
* Ensure that all [Prerequisites](https://konveyor.github.io/forklift/installingforklift/prereqs/) are set.
* VMware only: Create a [VMware Virtual Disk Development Kit (VDDK)](https://forklift-docs.konveyor.io/documentation/doc-Migration_Toolkit_for_Virtualization/master/index.html#creating-vddk-image_forklift) image.


### Adding source providers
Follow the procedures below to add VMware or oVirt source providers using the Forklift web console.

### Adding a VMware source provider
Add a VMware source provider by using the Forklift web console.

**Prerequisites**
VMware Virtual Disk Development Kit (VDDK) image in a secure registry that is accessible to all clusters.

**Procedure**
1. Open the Forklift web console.
2. Click **Providers**, then **Add provider**.
3. Select VMware from the Type list.
4. Fill in the following fields:
* **Name:** Name to display in the list of providers
* **Hostname or IP address:** vCenter host name or IP address
* **Username:** vCenter user, for example, user@vsphere.local
* **Password:** vCenter user password
* **VDDK init image:** VDDKInitImage path
5. Click **Verify certificate**.
6. Select the **I trust the authenticity of this certificate** checkbox.
7. Click **Add** to add and save the provider.

The source provider appears in the list of providers.

### Adding an oVirt source provider
Add an oVirt source provider by using the Forklift web console.

**Prerequisites**
CA certificate of the Engine.

**Procedure**
1. Open the Forklift web console.
2. Click **Providers**, then **Add provider**.
3. Select **Red Hat Virtualization** from the **Type** list.
4. Fill in the following fields:
* **Name:** Name toU display in the list of providers
* **Hostname or IP address:** Engine host name or IP address
* **Username:** Engine user
* **Password:** Engine password
* **CA certificate:** CA certificate of the Engine
5. Click **Add** to add and save the provider.

The source provider appears in the list of providers.


## Selecting a migration network for a source provider
Select a migration network in the Forklift web console for a source provider to reduce risk to the source environment and to improve performance.

> **Important:** Using the default network for migration can result in poor performance because the network might not have sufficient bandwidth which can have a negative effect on the source platform because the disk transfer operation might saturate the network.

**Prerequisites**
* The migration network must a minimum speed of 10 Gbps for disk transfer.
* The migration network must be accessible to the KubeVirt nodes through the default gateway.
* The source virtual disks are copied by a pod that is connected to the pod network of the target namespace.
* The migration network must have jumbo frames enabled.

**Procedure**
1. Open the Forklift web console.
2. Click **Providers**, the the **oVirt8** or **VMware** tab.
3. Click the **host number** in the **Hosts** column beside a provider to view a list of hosts.
4. Select one or more hosts and click Select migration network.
5. Select a Network.

> **Note:** Clear the selection by selecting the default network.

6. Complete the following fields:

For VMware:
* **ESXi host admin username:** Specify the ESXi host admin user, for example, root.
* **ESXi host admin password:** Specify the ESXi host admin password.

For oVirt:
* **Username:** Specify the Engine user.
* **Password:** Specify the Engine password.

7. Click **Save**.
8. Verify that the status of each host is Ready.

> **Note:** If a host status is not Ready, the host might be unreachable on the migration network or the credentials might be incorrect. Modify the host configuration and save the changes to correct.

## Adding a KubeVirt destination provider
Add a KubeVirt destination provider to the Forklift web console in addition to the default KubeVirt destination provider, which is the provider where Forklift.

**Prerequisites**
KubeVirt service account token with cluster-admin privileges.

**Procedure**
1. Open the Forklift web console.
2. Click **Providers**, the **Add provider**.
3. Select **KubeVirt** from the **Type** list.
4. Complete the following fields:
* **Cluster name:** Specify the cluster name to display in the list of target providers.
* **URL:** Specify the API endpoint of the cluster.
* **Service account token:** Specify the cluster-admin service account token.
5. Click **Check connection** to verify the credentials.
6. Click **Add**.

The provider appears in the list of providers.

## Selecting a migration network for a KubeVirt provider
Select a default migration network for a KubeVirt provider in the Forklift web console to improve performance. The default migration network is used to transfer disks to the namespaces in which it is configured.

> **Note:** If a migration network is not selected, the default migration network is the pod network which might not be optimal for disk transfer.

Override the default migration network of the provider by selecting a different network when creating a migration plan.

**Procedure**
1. Open the Forklift web console.
2. Click **Providers** then the **KubeVirt tab**.
3. Select a provider and click **Select migration network**.
4. Select a **network** from the list of available networks and click **Select**.
5. Click the **network number** in the **Networks column** beside the provider to verify that the selected network is the default migration network.

## Creating a network mapping
Create one or more network mappings  using the Forklift web console to map source networks to KubeVirt networks.

**Prerequisites**
Source and target providers added to the web console.

> **Note:** If more than one source and target network are mapped, each additional KubeVirt network requires its own network attachment definition.

**Procedure**
1. Click **Mappings**.
2. Click the **Network tab** then click **Create mapping**.
3. Complete the following fields:
* **Name:** Enter a name to display in the network mappings list.
* **Source provider:** Select a source provider.
* **Target provider:** Select a target provider.
* **Source networks:** Select a source network.
* **Target namespaces/networks:** Select a target network.

4. **Optional:** Click **Add** to create additional network mappings or to map multiple source networks to a single target network.

> **Note:** If an additional network mapping is created, select the network attachment definition as the target network.

5. Click Create.

The network mapping is displayed on the Network mappings screen.

## Creating a storage mapping
Create a storage mapping  using the Forklift web console to map source data stores to KubeVirt storage classes.

**Prerequisites**
* Source and target providers added to the web console.
* Local and shared persistent storage that support VM migration.

**Procedure**
1. Click **Mappings**.
2. Click the **Storage tab** and then click **Create mapping**.
3. Set the following parameters:
* Name of the storage mapping.
* Source provider
* Target provider
* VMware:
 * Source datastore
 * Target storage class
* oVirt:
 * Source storage domain
 * Target storage class

4. **Optional:** Click **Add** to create additional storage mappings or to map multiple source data stores or storage domains to a single storage class.
5. Click **Create**.

The mapping is displayed on the Storage mappings page.

## Creating a migration plan
Create a migration plan by using the Forklift web console.

A migration plan allows virtual machines to be grouped and migrated together or with the same migration parameters. For example: a percentage of the members of a cluster or a complete application.

Configure a hook to run an Ansible playbook or custom container image during a specified stage of the migration plan.

**Prerequisites**
If Forklift is not installed on the target cluster, add a target provider on the Providers page of the web console.

**Procedure**
1. Open the web console.
2. Click **Migration plans** then click **Create migration plan**.
3. Complete the following fields:
* **Plan name:** Enter a migration plan name to display in the migration plan list.
* **Plan description:** Optional: Brief description of the migration plan.
* **Source provider:** Select a source provider.
* **Target provider:** Select a target provider.
* **Target namespace:** Type to search for an existing target namespace or create a new namespace.

4. Change the migration transfer network for this plan by clicking **Select a different network**, selecting a **network** from the list, and clicking **Select**.

If a migration transfer network is defined for the KubeVirt provider and if the network is in the target namespace, that network is the default network for all migration plans. Otherwise, the pod network is used.

5. Click **Next**.
6. Select options to filter the list of source VMs and click **Next**.
7. Select the VMs to migrate and then click **Next**.
8. Select an existing network mapping or create a new network mapping.

### Creating a new network mapping
Follow the steps below to create a network mapping.

**Procedure**
1. Select a target network for each source network.
2. Optional: Select **Save mapping** to use again and enter a network mapping name.
3. Click **Next**.
4. Select an existing storage mapping or create a new storage mapping.

### Creating a new storage mapping
Follow the steps below to create a storage mapping.

**Procedure**
1. Select a target storage class for each VMware data store or oVirt storage domain.
2. Optional: Select Save mapping to use again and enter a storage mapping name.
3. Click **Next**.
4. Select a migration type and click Next.
* **Cold migration:** The source VMs are stopped while the data is copied.
* **Warm migration:** The source VMs run while the data is copied incrementally and the cutover runs later which stops the VMs and copies the remaining VM data and metadata.
5. Optional: Create a migration hook to run an Ansible playbook before or after migration:
6. Click **Add** hook.
7. Select the step when the hook will run.
8. Select a hook definition:
* **Ansible playbook:** Browse to the Ansible playbook or paste it into the field.
* **Custom container image:** Enter the image path: <registry_path>/<image_name>:<tag> to not use the default hook-runner image.

> **Important:** The registry must be accessible to the OKD cluster.

9. Click **Next**.
10. Review the migration plan and click **Finish**.

The migration plan is saved in the migration plan list.

11. Click the **Options** menu kebab of the migration plan and select **View details** to verify the migration plan details.

## Running a migration plan
Run a migration plan and view its progress in the Forklift web console.

**Prerequisites**
Valid migration plan.

**Procedure**
1. Click Migration plans.

The Migration plans list displays the source and target providers, the number of virtual machines (VMs) being migrated, and the status of the plan.

2. Click **Start** beside a migration plan to start the migration.

>Warm migration only: When the precopy stage starts.
1. Click Cutover to complete the migration.
2. Expand a migration plan to view the migration details.

The migration details screen displays the migration start and end time, the amount of data copied, and a progress pipeline for each VM being migrated.

3. Expand a VM to view the migration steps, elapsed time of each step, and its state.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Forklift/MigratingVMs/migrateweb.md)
