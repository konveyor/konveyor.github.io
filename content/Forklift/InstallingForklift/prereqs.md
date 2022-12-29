---
title: "Prerequisites"
date: 2022-05-11T15:56:20-06:00
draft: false
---


The following prerequisites ensure the environment is prepared for migration.

## Software compatibility guidelines
Install compatible software versions with Forklift using the table below.

| Forklift |	OKD|	KubeVirt |	VMware vSphere |	oVirt |
|--|--|--|--|--|
| 2.3 | 4.10| 4.10 | 6.5 or later | 4.4.9 or later |

## Storage support and default modes
Forklift uses the following default volume and access modes for supported storage.

> **Note:** The following settings must be applied if the KubeVirt storage does not support [dynamic provisioning](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.10/html/storage/dynamic-provisioning):
* **Filesystem volume mode:** Slower than Block volume mode.
* **ReadWriteOnce access mode:** Does not support live virtual machine migration.

See [Enabling a statically-provisioned storage class](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.10/html/virtualization/virtual-machines#virt-customizing-storage-profile_virt-creating-data-volumes) for details on editing the storage profile.

Default volume and access modes.

|Provisioner|	Volume mode|	Access mode|
|--|--|--|
|kubernetes.io/aws-ebs|Block|ReadWriteOnce|
|kubernetes.io/azure-disk|Block|ReadWriteOnce|
|kubernetes.io/azure-file|Filesystem|ReadWriteMany|
|kubernetes.io/cinder|Block|ReadWriteOnce|
|kubernetes.io/gce-pd|Block|ReadWriteOnce|
|kubernetes.io/hostpath-provisioner|Filesystem|ReadWriteOnce|
|manila.csi.openstack.org|Filesystem|ReadWriteMany|
|openshift-storage.cephfs.csi.ceph.com|Filesystem|ReadWriteMany|
|openshift-storage.rbd.csi.ceph.com|Block|ReadWriteOnce|
|kubernetes.io/rbd|Block|ReadWriteOnce|
|kubernetes.io/vsphere-volume|Block|ReadWriteOnce|

## Network prerequisites
The following prerequisites apply to all migrations:

* IP addresses, VLANs, and other network configuration settings must not be changed before or after migration. The MAC addresses of the virtual machines are preserved during migration.
* The network connections between the source environment, the KubeVirt cluster, and the replication repository must be reliable and uninterrupted.
* A network attachment definition for each additional destination network must be created if mapping more than one source and destination network.

## Ports
Use the following port parameters for migration.

### VMware VSphere
The firewalls must enable traffic over the following required network ports for migrating from VMware vSphere.

|Port|	Protocol|	Source|	Destination|	Purpose|
|--|--|--|--|--|
|443|TCP|OpenShift nodes|VMware vCenter|VMware provider inventory & Disk transfer authentication|
|443|TCP|OpenShift nodes|VMware ESXi hosts|Disk transfer authentication|
|902|TCP|OpenShift nodes|VMware ESXi hosts|Disk transfer data copy|

### oVirt
The firewalls must enable traffic over the following required network ports for migrating from oVirt.

|Port|	Protocol|	Source|	Destination|	Purpose|
|--|--|--|--|--|
|443|TCP|OpenShift nodes|oVirt Engine|oVirt provider inventory|Disk transfer authentication|
|443|TCP|OpenShift nodes|oVirt hosts|Disk transfer authentication|
|54322|TCP|OpenShift nodes|oVirt hosts|Disk transfer data copy|

## Source virtual machine prerequisites
The following prerequisites apply to all migrations:
* ISO/CDROM disks must be unmounted.
* Each NIC must contain one IPv4 and/or one IPv6 address.
* The VM name must contain only lowercase letters (a-z), numbers (0-9), or hyphens (-), up to a maximum of 253 characters.
    * The first and last characters must be alphanumeric.
    * The name must not contain uppercase letters, spaces, periods (.), or special characters.
* The VM name must not duplicate the name of a VM in the KubeVirt environment.
* The VM operating system must be certified and supported for use as a [guest operating system](https://access.redhat.com/articles/973163#ocpvirt) with KubeVirt and for [conversion to KVM with virt-v2v](https://access.redhat.com/articles/1351473).

### oVirt prerequisites
The following prerequisites apply to oVirt migrations:
* Must have the CA certificate of the engine.
* Able to obtain the CA certificate by navigating to:
```
 https://<{rhv-short}_engine_host>/ovirt-engine/services/pki-resource?resource=ca-certificate&format=X509-PEM-CA
```
### VMware prerequisites
The following prerequisites apply to VMware migrations:
* Install [VMware Tools](https://docs.vmware.com/en/VMware-Workstation-Pro/index.html) must include installon of all source virtual machines (VMs).
* If running a warm migration, [changed block tracking (CBT)](https://kb.vmware.com/s/article/1020128) on the VMs and on the VM disks must be enabled.
* Create a VMware Virtual Disk Development Kit (VDDK) image.
* Obtain the SHA-1 fingerprint of the vCenter host.
* The NFC service memory of the host must be increased if migrating more than 10 VMs from an ESXi host in the same migration plan.

## Creating a VDDK image
Forklift uses the VMware Virtual Disk Development Kit (VDDK) SDK to transfer virtual disks from VMware vSphere. Follow the steps below to create the VDDK image.

> **Note:**
The VDDK init image path is required to add a VMware source provider:

1. Download the VMware Virtual Disk Development Kit (VDDK)
2. Build a VDDK image.
3. Push the VDDK image to the image registry.

> **Important:** Storing the VDDK image in a public registry might violate the VMware license terms.

**Prerequisites**

* [OKD image registry](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.10/html/registry/setting-up-and-configuring-the-registry#configuring-registry-storage-baremetal).
* podman installed.
* KubeVirt must be able to access an external registry if used.

**Procedure**
1. Create and navigate to a temporary directory:
```
Example output:
$ mkdir /tmp/<dir_name> && cd /tmp/<dir_name>
```
2. Open a browser and navigate to the [VMware VDDK download page](https://developer.vmware.com/web/sdk/7.0/vddk).
3. Select the latest VDDK version and click **Download**.
4. Save the VDDK archive file in the temporary directory.
5. Extract the VDDK archive:
```
$ tar -xzf VMware-vix-disklib-<version>.x86_64.tar.gz
```
6. Create a Dockerfile:
```
$ cat > Dockerfile <<EOF
FROM registry.access.redhat.com/ubi8/ubi-minimal
COPY vmware-vix-disklib-distrib /vmware-vix-disklib-distrib
RUN mkdir -p /opt
ENTRYPOINT ["cp", "-r", "/vmware-vix-disklib-distrib", "/opt"]
EOF
```
7. Build the VDDK image:
```
$ podman build . -t <registry_route_or_server_path>/vddk:<tag>
```
8. Push the VDDK image to the registry:
```
$ podman push <registry_route_or_server_path>/vddk:<tag>
```
9. Verify the image is accessible to the KubeVirt environment.

## Obtaining the SHA-1 fingerprint of a vCenter host

Follow the steps below to obtain the SHA-1 fingerprint of a vCenter host in order to create a Secret CR.

**Procedure**
1. Run the following command:
```
$ openssl s_client \
    -connect <vcenter_host>:443 \ (1)
    < /dev/null 2>/dev/null \
    | openssl x509 -fingerprint -noout -in /dev/stdin \
    | cut -d '=' -f 2
1	Specify the IP address or FQDN of the vCenter host.
```
Example output:
```
01:23:45:67:89:AB:CD:EF:01:23:45:67:89:AB:CD:EF:01:23:45:67
Increasing the NFC service memory of an ESXi host
```
> **Note:** If more than 10 VMs are migrating from an ESXi host in the same migration plan, increase the NFC service memory of the host. The migration will fail because the NFC service memory is limited to 10 parallel connections.

**Procedure**
1. Log in to the ESXi host as root.
2. Change the value of maxMemory to 1000000000 in /etc/vmware/hostd/config.xml:
```
...
      <nfcsvc>
         <path>libnfcsvc.so</path>
         <enabled>true</enabled>
         <maxMemory>1000000000</maxMemory>
         <maxStreamMemory>10485760</maxStreamMemory>
      </nfcsvc>
...
Restart hostd:

# /etc/init.d/hostd restart
```
The host does not need to be restarted.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Forklift/InstallingForklift/prereqs.md)
