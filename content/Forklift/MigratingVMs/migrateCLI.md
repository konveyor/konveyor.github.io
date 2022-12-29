---
title: "Migrating virtual machines using the command line"
date: 2022-05-19T10:04:06-06:00
draft: false
---

Follow the steps below to migrate virtual machines (VMs) to KubeVirt using the command line (CLI) by creating Forklift custom resources (CRs) and specifying:
* A name for cluster-scoped CRs.
* A name and a namespace for namespace-scoped CRs.

**Prerequisites**

* Must be logged in as a user with cluster-admin privileges.

* **VMware only:**
  * Must have the vCenter SHA-1 fingerprint.
  * Must create a VMware Virtual Disk Development Kit (VDDK) image in a secure registry that is accessible to all clusters.

**Procedure**
1. Create a secret manifest for the source provider credentials:
```
$ cat << EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: <secret>
  namespace: konveyor-forklift
type: Opaque
stringData:
  user: <user> (1)
  password: <password> (2)
  cacert: | (3)
    <oVirt_ca_certificate>
  thumbprint: <vcenter_fingerprint> (4)
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Specify the vCenter user or the oVirt Engine user.
* (2) Specify the user password.
* (3) oVirt only: Specify the CA certificate of the Engine. Retrieve it at https://<{rhv-short}_engine_host>/ovirt-engine/services/pki-resource?resource=ca-certificate&format=X509-PEM-CA.
* (4) VMware only: Specify the vCenter SHA-1 fingerprint.

2. Create a provider manifest for the source provider:
```
$ cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: Provider
metadata:
  name: <provider>
  namespace: konveyor-forklift
spec:
  type: <provider_type> (1)
  url: <api_end_point> (2)
  settings:
    vddkInitImage: <registry_route_or_server_path>/vddk: (3)
  secret:
    name: <secret> (4)
    namespace: konveyor-forklift
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Allowed values are ovirt and vsphere.
* (2) Specify the API end point URL, for example, https://<vCenter_host>/sdk for vSphere or https://<{rhv-short}_engine_host>/ovirt-engine/api/ for oVirt.
* (3) VMware only: Specify the VDDK image created.
* (4) Specify the name of provider Secret CR.

3. **VMware only:** Create a Host manifest:
```
$ cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: Host
metadata:
  name: <vmware_host>
  namespace: konveyor-forklift
spec:
  provider:
    namespace: konveyor-forklift
    name: <source_provider> (1)
  id: <source_host_mor> (2)
  ipAddress: <source_network_ip> (3)
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Specify the name of the VMware Provider CR.
* (2) Specify the managed object reference (MOR) of the VMware host.
* (3) Specify the IP address of the VMware migration network.

4. Create a NetworkMap manifest to map the source and destination networks:
```
$  cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: NetworkMap
metadata:
  name: <network_map>
  namespace: konveyor-forklift
spec:
  map:
    - destination:
        name: <pod>
        namespace: konveyor-forklift
        type: pod (1)
      source: (2)
        id: <source_network_id> (3)
        name: <source_network_name>
    - destination:
        name: <network_attachment_definition> (4)
        namespace: <network_attachment_definition_namespace> (5)
        type: multus
      source:
        id: <source_network_id>
        name: <source_network_name>
  provider:
    source:
      name: <source_provider>
      namespace: konveyor-forklift
    destination:
      name: <destination_cluster>
      namespace: konveyor-forklift
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Allowed values are pod and multus.
* (2) Use either the id or the name parameter to specify the source network.
* (3) Specify the VMware network MOR or oVirt network UUID.
* (4) Specify a network attachment definition for each additional KubeVirt network.
* (5) Specify the namespace of the KubeVirt network attachment definition.

5. Create a StorageMap manifest to map source and destination storage:
```
$ cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: StorageMap
metadata:
  name: <storage_map>
  namespace: konveyor-forklift
spec:
  map:
    - destination:
        storageClass: <storage_class>
        accessMode: <access_mode> (1)
      source:
        id: <source_datastore> (2)
    - destination:
        storageClass: <storage_class>
        accessMode: <access_mode>
      source:
        id: <source_datastore>
  provider:
    source:
      name: <source_provider>
      namespace: konveyor-forklift
    destination:
      name: <destination_cluster>
      namespace: konveyor-forklift
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Allowed values are ReadWriteOnce and ReadWriteMany.
* (2) Specify the VMware data storage MOR or oVirt storage domain UUID, for example, f2737930-b567-451a-9ceb-2887f6207009.

6. **Optional:** Create a hook manifest to run custom code on a VM during the phase specified in the Plan CR:
```
$  cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: Hook
metadata:
  name: <hook>
  namespace: konveyor-forklift
spec:
  image: quay.io/konveyor/hook-runner (1)
  playbook: | (2)
    LS0tCi0gbmFtZTogTWFpbgogIGhvc3RzOiBsb2NhbGhvc3QKICB0YXNrczoKICAtIG5hbWU6IExv
    YWQgUGxhbgogICAgaW5jbHVkZV92YXJzOgogICAgICBmaWxlOiAiL3RtcC9ob29rL3BsYW4ueW1s
    IgogICAgICBuYW1lOiBwbGFuCiAgLSBuYW1lOiBMb2FkIFdvcmtsb2FkCiAgICBpbmNsdWRlX3Zh
    cnM6CiAgICAgIGZpbGU6ICIvdG1wL2hvb2svd29ya2xvYWQueW1sIgogICAgICBuYW1lOiB3b3Jr
    bG9hZAoK
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Use the default hook-runner image or specify a custom image. A playbook does not need to be specified if a custom image is.
* (2) **Optional:** Base64-encoded Ansible playbook. The image must be hook-runner if a playbook is specified.

7. Create a Plan manifest for the migration:
```
$ cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: Plan
metadata:
  name: <plan> (1)
  namespace: konveyor-forklift
spec:
  warm: true (2)
  provider:
    source:
      name: <source_provider>
      namespace: konveyor-forklift
    destination:
      name: <destination_cluster>
      namespace: konveyor-forklift
  map:
    network: (3)
      name: <network_map> (4)
      namespace: konveyor-forklift
    storage:
      name: <storage_map> (5)
      namespace: konveyor-forklift
  targetNamespace: konveyor-forklift
  vms: (6)
    - id: <source_vm> (7)
    - name: <source_vm>
      hooks: (8)
        - hook:
            namespace: konveyor-forklift
            name: <hook> (9)
          step: <step> (10)
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Specify the name of the Plan CR.
* (2) Specify whether the migration is warm or cold. Only the precopy stage will run in a warm migration if the cutover parameter in the Migration manifest value is not specified.
* (3) Add multiple network mappings if needed.
* (4) Specify the name of the NetworkMap CR.
* (5) Specify the name of the StorageMap CR.
* (6) Use either the id or the name parameter to specify the source VMs.
* (7) Specify the VMware VM MOR or oVirt VM UUID.
* (8) **Optional:** Specify up to two hooks for a VM. Each hook must run during a separate migration step.
* (9) Specify the name of the Hook CR.
* (10) Allowed values are PreHook before the migation plan starts, or PostHook after the migration is complete.

8. Create a migration manifest to run the Plan CR:
```
$ cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: Migration
metadata:
  name: <migration> (1)
  namespace: konveyor-forklift
spec:
  plan:
    name: <plan> (2)
    namespace: konveyor-forklift
  cutover: <cutover_time> (3)
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Specify the name of the Migration CR.
* (2) Specify the name of the Plan CR being run. The Migration CR creates a VirtualMachine CR for each VM that is migrated.
* (3) **Optional:** Specify a cutover time according to the ISO 8601 format with the UTC time offset, for example, 2021-04-04T01:23:45.678+09:00. Multiple Migration CRs can be associated with a single Plan CR. If a migration does not complete, create a new Migration CR, without changing the Plan CR, to migrate the remaining VMs.

9. Retrieve the Migration CR to monitor the progress of the migration:
```
$ kubectl get migration/<migration> -n konveyor-forklift -o yaml
```
## Obtaining the SHA-1 fingerprint of a vCenter host
Obtain the SHA-1 fingerprint of a vCenter host in order to create a Secret CR.

**Procedure**
1. Run the following command:
```
$ openssl s_client \
    -connect <vcenter_host>:443 \ (1)
    < /dev/null 2>/dev/null \
    | openssl x509 -fingerprint -noout -in /dev/stdin \
    | cut -d '=' -f 2
```
The explanation below refers to the callouts in the sample code above.
* (1) Specify the IP address or FQDN of the vCenter host.

Example output:
```
01:23:45:67:89:AB:CD:EF:01:23:45:67:89:AB:CD:EF:01:23:45:67
```
## Canceling a migration
Forklift functionality can cancel an entire migration or individual virtual machines (VMs) while a migration is in progress from the command line interface (CLI).

### Canceling an entire migration
Follow the steps below to cancel an entire migration.

**Procedure**
1. Delete the Migration CR:
```
$ kubectl delete migration <migration> -n konveyor-forklift (1)
```
The explanation below refers to the callouts in the sample code above.
* (1) Specify the name of the Migration CR.

### Canceling individual VMs migration
Follow the steps below to cancel migrating an individual VM.

**Procedure**
1. Add the individual VMs to the spec.cancel block of the Migration manifest:
```
$ cat << EOF | kubectl apply -f -
apiVersion: forklift.konveyor.io/v1beta1
kind: Migration
metadata:
  name: <migration>
  namespace: konveyor-forklift
...
spec:
  cancel:
  - id: vm-102 (1)
  - id: vm-203
  - name: rhel8-vm
EOF
```
The explanation below refers to the callouts in the sample code above.
* (1) Specify a VM by using the id key or the name key.
The value of the id key is the managed object reference, for a VMware VM, or the VM UUID, for a oVirt VM.

2. Retrieve the Migration CR to monitor the progress of the remaining VMs:
```
$ kubectl get migration/<migration> -n konveyor-forklift -o yaml
```

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Forklift/MigratingVMs/migratecli.md)
