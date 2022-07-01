---
title: "Advanced migration options"
date: 2022-05-18T11:04:36-06:00
draft: false
---

## Changing precopy intervals for warm migration
Follow the steps below to change the snapshot interval by patching the ForkliftController custom resource (CR).

**Procedure**
* Patch the ForkliftController CR:
```
$ kubectl patch forkliftcontroller/<forklift-controller> -n konveyor-forklift -p '{"spec": {"controller_precopy_interval": <60>}}' --type=merge (1)
```
The explanation below refers to the callout in the sample code above.
* (1) Specify the precopy interval in minutes. The default value is 60.

> **Note:** The forklift-controller pod does not need to be restarted.

## Creating custom rules for the Validation service
The Validation service uses Open Policy Agent (OPA) policy rules to check the suitability of each virtual machine (VM) for migration. The Validation service generates a list of concerns for each VM, which are stored in the Provider Inventory service as VM attributes. The web console displays the concerns for each VM in the provider inventory.

Custom rules to extend the default ruleset of the Validation service can be created. For example, create a rule that checks whether a VM has multiple disks.

### About Rego files
Validation rules are written in Rego, the Open Policy Agent (OPA) native query language. The rules are stored as .rego files in the /usr/share/opa/policies/io/konveyor/forklift/<provider> directory of the Validation pod.

Each validation rule is defined in a separate .rego file and tests for a specific condition. If the condition evaluates as true, the rule adds a {“category”, “label”, “assessment”} hash to the concerns. The concerns content is added to the concerns key in the inventory record of the VM. The web console displays the content of the concerns key for each VM in the provider inventory.

The following .rego file example checks for distributed resource scheduling enabled in the cluster of a VMware VM:

drs_enabled.rego example:
```
package io.konveyor.forklift.vmware (1)

has_drs_enabled {
    input.host.cluster.drsEnabled (2)
}

concerns[flag] {
    has_drs_enabled
    flag := {
        "category": "Information",
        "label": "VM running in a DRS-enabled cluster",
        "assessment": "Distributed resource scheduling is not currently supported by OpenShift Virtualization. The VM can be migrated but it will not have this feature in the target environment."
    }
}
```
The explanations below refer to the callouts in the sample code above.
* (1) Each validation rule is defined within a package. The package namespaces are io.konveyor.forklift.vmware for VMware and io.konveyor.forklift.ovirt for oVirt.
* (2) Query parameters are based on the input key of the Validation service JSON.
## Checking the default validation rules
Before creating a custom rule, follow the steps below to check the default rules of the Validation service to prevent creating a rule that redefines an existing default value.

**Example:** If a default rule contains the line default valid_input = false and a custom rule that contains the line default valid_input = true is created, the Validation service will not start.

**Procedure**
1. Connect to the terminal of the Validation pod:
```
$ kubectl rsh <validation_pod>
```
2. Go to the OPA policies directory for the provider:
```
$ cd /usr/share/opa/policies/io/konveyor/forklift/<provider> (1)
```
The explanations below refer to the callouts in the sample code above.
* (1) Specify vmware or ovirt.

3. Search for the default policies:
```
$ grep -R "default" *
```

## Retrieving the Inventory service JSON
Follow the steps below to retrieve the Inventory service JSON by sending an Inventory service query to a virtual machine (VM). The output contains an "input" key, which contains the inventory attributes that are queried by the Validation service rules.

Create a validation rule based on any attribute in the "input" key, for example, input.snapshot.kind.

**Procedure**
1. Retrieve the Inventory service route:
```
$ kubectl get route <inventory_service> -n konveyor-forklift
```
2. Retrieve the UUID of a provider:
```
$ GET https://<inventory_service_route>/providers/<provider> (1)
```
The explanations below refer to the callouts in the sample code above.
* (1) Allowed values for the provider are vsphere and ovirt.
3. Retrieve the VMs of a provider:
```
$ GET https://<inventory_service_route>/providers/<provider>/<UUID>/vms
```
4. Retrieve the details of a VM:
```
$ GET https://<inventory_service_route>/providers/<provider>/<UUID>/workloads/<vm>
```
Example output:
```
{
    "input": {
        "selfLink": "providers/vsphere/c872d364-d62b-46f0-bd42-16799f40324e/workloads/vm-431",
        "id": "vm-431",
        "parent": {
            "kind": "Folder",
            "id": "group-v22"
        },
        "revision": 1,
        "name": "iscsi-target",
        "revisionValidated": 1,
        "isTemplate": false,
        "networks": [
            {
                "kind": "Network",
                "id": "network-31"
            },
            {
                "kind": "Network",
                "id": "network-33"
            }
        ],
        "disks": [
            {
                "key": 2000,
                "file": "[iSCSI_Datastore] iscsi-target/iscsi-target-000001.vmdk",
                "datastore": {
                    "kind": "Datastore",
                    "id": "datastore-63"
                },
                "capacity": 17179869184,
                "shared": false,
                "rdm": false
            },
            {
                "key": 2001,
                "file": "[iSCSI_Datastore] iscsi-target/iscsi-target_1-000001.vmdk",
                "datastore": {
                    "kind": "Datastore",
                    "id": "datastore-63"
                },
                "capacity": 10737418240,
                "shared": false,
                "rdm": false
            }
        ],
        "concerns": [],
        "policyVersion": 5,
        "uuid": "42256329-8c3a-2a82-54fd-01d845a8bf49",
        "firmware": "bios",
        "powerState": "poweredOn",
        "connectionState": "connected",
        "snapshot": {
            "kind": "VirtualMachineSnapshot",
            "id": "snapshot-3034"
        },
        "changeTrackingEnabled": false,
        "cpuAffinity": [
            0,
            2
        ],
        "cpuHotAddEnabled": true,
        "cpuHotRemoveEnabled": false,
        "memoryHotAddEnabled": false,
        "faultToleranceEnabled": false,
        "cpuCount": 2,
        "coresPerSocket": 1,
        "memoryMB": 2048,
        "guestName": "Red Hat Enterprise Linux 7 (64-bit)",
        "balloonedMemory": 0,
        "ipAddress": "10.19.2.96",
        "storageUsed": 30436770129,
        "numaNodeAffinity": [
            "0",
            "1"
        ],
        "devices": [
            {
                "kind": "RealUSBController"
            }
        ],
        "host": {
            "id": "host-29",
            "parent": {
                "kind": "Cluster",
                "id": "domain-c26"
            },
            "revision": 1,
            "name": "IP address or host name of the vCenter host or {rhv-short} Engine host",
            "selfLink": "providers/vsphere/c872d364-d62b-46f0-bd42-16799f40324e/hosts/host-29",
            "status": "green",
            "inMaintenance": false,
            "managementServerIp": "10.19.2.96",
            "thumbprint": <thumbprint>,
            "timezone": "UTC",
            "cpuSockets": 2,
            "cpuCores": 16,
            "productName": "VMware ESXi",
            "productVersion": "6.5.0",
            "networking": {
                "pNICs": [
                    {
                        "key": "key-vim.host.PhysicalNic-vmnic0",
                        "linkSpeed": 10000
                    },
                    {
                        "key": "key-vim.host.PhysicalNic-vmnic1",
                        "linkSpeed": 10000
                    },
                    {
                        "key": "key-vim.host.PhysicalNic-vmnic2",
                        "linkSpeed": 10000
                    },
                    {
                        "key": "key-vim.host.PhysicalNic-vmnic3",
                        "linkSpeed": 10000
                    }
                ],
                "vNICs": [
                    {
                        "key": "key-vim.host.VirtualNic-vmk2",
                        "portGroup": "VM_Migration",
                        "dPortGroup": "",
                        "ipAddress": "192.168.79.13",
                        "subnetMask": "255.255.255.0",
                        "mtu": 9000
                    },
                    {
                        "key": "key-vim.host.VirtualNic-vmk0",
                        "portGroup": "Management Network",
                        "dPortGroup": "",
                        "ipAddress": "10.19.2.13",
                        "subnetMask": "255.255.255.128",
                        "mtu": 1500
                    },
                    {
                        "key": "key-vim.host.VirtualNic-vmk1",
                        "portGroup": "Storage Network",
                        "dPortGroup": "",
                        "ipAddress": "172.31.2.13",
                        "subnetMask": "255.255.0.0",
                        "mtu": 1500
                    },
                    {
                        "key": "key-vim.host.VirtualNic-vmk3",
                        "portGroup": "",
                        "dPortGroup": "dvportgroup-48",
                        "ipAddress": "192.168.61.13",
                        "subnetMask": "255.255.255.0",
                        "mtu": 1500
                    },
                    {
                        "key": "key-vim.host.VirtualNic-vmk4",
                        "portGroup": "VM_DHCP_Network",
                        "dPortGroup": "",
                        "ipAddress": "10.19.2.231",
                        "subnetMask": "255.255.255.128",
                        "mtu": 1500
                    }
                ],
                "portGroups": [
                    {
                        "key": "key-vim.host.PortGroup-VM Network",
                        "name": "VM Network",
                        "vSwitch": "key-vim.host.VirtualSwitch-vSwitch0"
                    },
                    {
                        "key": "key-vim.host.PortGroup-Management Network",
                        "name": "Management Network",
                        "vSwitch": "key-vim.host.VirtualSwitch-vSwitch0"
                    },
                    {
                        "key": "key-vim.host.PortGroup-VM_10G_Network",
                        "name": "VM_10G_Network",
                        "vSwitch": "key-vim.host.VirtualSwitch-vSwitch1"
                    },
                    {
                        "key": "key-vim.host.PortGroup-VM_Storage",
                        "name": "VM_Storage",
                        "vSwitch": "key-vim.host.VirtualSwitch-vSwitch1"
                    },
                    {
                        "key": "key-vim.host.PortGroup-VM_DHCP_Network",
                        "name": "VM_DHCP_Network",
                        "vSwitch": "key-vim.host.VirtualSwitch-vSwitch1"
                    },
                    {
                        "key": "key-vim.host.PortGroup-Storage Network",
                        "name": "Storage Network",
                        "vSwitch": "key-vim.host.VirtualSwitch-vSwitch1"
                    },
                    {
                        "key": "key-vim.host.PortGroup-VM_Isolated_67",
                        "name": "VM_Isolated_67",
                        "vSwitch": "key-vim.host.VirtualSwitch-vSwitch2"
                    },
                    {
                        "key": "key-vim.host.PortGroup-VM_Migration",
                        "name": "VM_Migration",
                        "vSwitch": "key-vim.host.VirtualSwitch-vSwitch2"
                    }
                ],
                "switches": [
                    {
                        "key": "key-vim.host.VirtualSwitch-vSwitch0",
                        "name": "vSwitch0",
                        "portGroups": [
                            "key-vim.host.PortGroup-VM Network",
                            "key-vim.host.PortGroup-Management Network"
                        ],
                        "pNICs": [
                            "key-vim.host.PhysicalNic-vmnic4"
                        ]
                    },
                    {
                        "key": "key-vim.host.VirtualSwitch-vSwitch1",
                        "name": "vSwitch1",
                        "portGroups": [
                            "key-vim.host.PortGroup-VM_10G_Network",
                            "key-vim.host.PortGroup-VM_Storage",
                            "key-vim.host.PortGroup-VM_DHCP_Network",
                            "key-vim.host.PortGroup-Storage Network"
                        ],
                        "pNICs": [
                            "key-vim.host.PhysicalNic-vmnic2",
                            "key-vim.host.PhysicalNic-vmnic0"
                        ]
                    },
                    {
                        "key": "key-vim.host.VirtualSwitch-vSwitch2",
                        "name": "vSwitch2",
                        "portGroups": [
                            "key-vim.host.PortGroup-VM_Isolated_67",
                            "key-vim.host.PortGroup-VM_Migration"
                        ],
                        "pNICs": [
                            "key-vim.host.PhysicalNic-vmnic3",
                            "key-vim.host.PhysicalNic-vmnic1"
                        ]
                    }
                ]
            },
            "networks": [
                {
                    "kind": "Network",
                    "id": "network-31"
                },
                {
                    "kind": "Network",
                    "id": "network-34"
                },
                {
                    "kind": "Network",
                    "id": "network-57"
                },
                {
                    "kind": "Network",
                    "id": "network-33"
                },
                {
                    "kind": "Network",
                    "id": "dvportgroup-47"
                }
            ],
            "datastores": [
                {
                    "kind": "Datastore",
                    "id": "datastore-35"
                },
                {
                    "kind": "Datastore",
                    "id": "datastore-63"
                }
            ],
            "vms": null,
            "networkAdapters": [],
            "cluster": {
                "id": "domain-c26",
                "parent": {
                    "kind": "Folder",
                    "id": "group-h23"
                },
                "revision": 1,
                "name": "mycluster",
                "selfLink": "providers/vsphere/c872d364-d62b-46f0-bd42-16799f40324e/clusters/domain-c26",
                "folder": "group-h23",
                "networks": [
                    {
                        "kind": "Network",
                        "id": "network-31"
                    },
                    {
                        "kind": "Network",
                        "id": "network-34"
                    },
                    {
                        "kind": "Network",
                        "id": "network-57"
                    },
                    {
                        "kind": "Network",
                        "id": "network-33"
                    },
                    {
                        "kind": "Network",
                        "id": "dvportgroup-47"
                    }
                ],
                "datastores": [
                    {
                        "kind": "Datastore",
                        "id": "datastore-35"
                    },
                    {
                        "kind": "Datastore",
                        "id": "datastore-63"
                    }
                ],
                "hosts": [
                    {
                        "kind": "Host",
                        "id": "host-44"
                    },
                    {
                        "kind": "Host",
                        "id": "host-29"
                    }
                ],
                "dasEnabled": false,
                "dasVms": [],
                "drsEnabled": true,
                "drsBehavior": "fullyAutomated",
                "drsVms": [],
                "datacenter": null
            }
        }
    }
}
```

## Creating a validation rule
Follow the steps below to create a validation rule by applying a config map custom resource (CR) containing the rule to the Validation service.

* If the rule has the same name as an existing rule, the Validation service performs an OR operation with the rules.

* If the rule contradicts a default rule, the Validation service will not start.

### Validation rule example
Validation rules are based on virtual machine (VM) attributes collected by the Provider Inventory service.

For example, the VMware API uses this path to check whether a VMware VM has NUMA node affinity configured: MOR:VirtualMachine.config.extraConfig["numa.nodeAffinity"].

The Provider Inventory service simplifies this configuration and returns a testable attribute with a list value:
```
"numaNodeAffinity": [
    "0",
    "1"
],
```
Create a Rego query, based on this attribute, and add it to the forklift-validation-config config map:
```
`count(input.numaNodeAffinity) != 0`
```
**Procedure**
1. Create a config map CR according to the following example:
```
$ cat << EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: <forklift-validation-config>
  namespace: konveyor-forklift
data:
  vmware_multiple_disks.rego: |-
    package <provider_package> (1)

    has_multiple_disks { (2)
      count(input.disks) > 1
    }

    concerns[flag] {
      has_multiple_disks (3)
        flag := {
          "category": "<Information>", (4)
          "label": "Multiple disks detected",
          "assessment": "Multiple disks detected on this VM."
        }
    }
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Specify the provider package name. Allowed values are io.konveyor.forklift.vmware for VMware and io.konveyor.forklift.ovirt for oVirt.
* (2) Specify the concerns name and Rego query.
* (3) Specify the concerns name and flag parameter values.
* (4) Allowed values are Critical, Warning, and Information.

2. Stop the Validation pod by scaling the forklift-controller deployment to 0:
```
$ kubectl scale -n konveyor-forklift --replicas=0 deployment/forklift-controller
```
3. Start the Validation pod by scaling the forklift-controller deployment to 1:
```
$ kubectl scale -n konveyor-forklift --replicas=1 deployment/forklift-controller
```
4. Check the Validation pod log to verify that the pod started:
```
$ kubectl logs -f <validation_pod>
```
If the custom rule conflicts with a default rule, the Validation pod will not start.

5. Remove the source provider:
```
$ kubectl delete provider <provider> -n konveyor-forklift
```
6. Add the source provider to apply the new rule:
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
  secret:
    name: <secret> (3)
    namespace: konveyor-forklift
EOF
```
The explanations below refer to the callouts in the sample code above.
* (1) Allowed values are ovirt and vsphere.
* (2) Specify the API end point URL, for example, https://<vCenter_host>/sdk for vSphere or https://<{rhv-short}_engine_host>/ovirt-engine/api/ for oVirt.
* (3) Specify the name of the provider Secret CR.

> **Important:** Update the rules version after creating a custom rule so that the Inventory service detects the changes and validates the VMs.

## Updating the inventory rules version
Follow the steps below to update the inventory rules version each time the rules are updated so that the Provider Inventory service detects the changes and triggers the Validation service.

The rules version is recorded in a rules_version.rego file for each provider.

**Procedure**
1. Retrieve the current rules version:
```
$ GET https://forklift-validation/v1/data/io/konveyor/forklift/<provider>/rules_version (1)
```
Example output:
```
{
   "result": {
       "rules_version": 5
   }
}
```
2. Connect to the terminal of the Validation pod:
```
$ kubectl rsh <validation_pod>
```
3. Update the rules version in the **/usr/share/opa/policies/io/konveyor/forklift/<provider>/rules_version.rego** file.

4. Log out of the Validation pod terminal.

5. Verify the updated rules version:
```
$ GET https://forklift-validation/v1/data/io/konveyor/forklift/<provider>/rules_version (1)
Example output
{
   "result": {
       "rules_version": 6
   }
}
```

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Forklift/MigratingVMs/advancedmigrate.md)
