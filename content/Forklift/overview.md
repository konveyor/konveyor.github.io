---
title: "Forklift Overview"
date: 2022-05-11T15:15:01-06:00
draft: false
---
Forklift provides a simplistic way for large scale migration virtual machines at scale to Kubernetes from traditional hypervisors like VMware (vSphere) and Red Hat Virtualization (oVirt) to KubeVirt. Forklift is an open source tool based on proven core linux v2v technologies that can rehost a few or hundreds of VMs while minimizing downtime as it lifts and shifts the applications. The benefits of Forklift include:

* **Migration Analytics:** Validate the compatibility of the virtual machine before executing the migration.
* **Warm migrations:** Reduce downtime by using warm migration capabilities, which will pre-copy the data before finalizing the migration.
* **Migration at Scale:** Migrate small or large amount of virtual machines at once.

Forklift operates using the functionality of multiple projects:
* [Forklift Operator](https://github.com/konveyor/forklift-operator). The Forklift Operator deploys and maintains Forklift.
* [Forklift UI](https://github.com/konveyor/forklift-ui). The Forklift UI is based on Patternfly 4.
* [Forklift Controller](https://github.com/konveyor/forklift-controller). The Forklift Controller orchestrates the migration.
* [Forklift Validation](https://github.com/konveyor/forklift-validation). The Forklift Validation service checks the virtual machines for possible issues before migration. Runs with Open Policy Agent.
* [Forklift must-gather](https://github.com/konveyor/forklift-must-gather). Support tool for gathering information about the Forklift environment.

#### Additional resources
* [Performance recommendations for migrating from VMware vSphere to OpenShift Virtualization.](https://access.redhat.com/articles/6242511)
* [Performance recommendations for migrating from Red Hat Virtualization to OpenShift Virtualization.](https://access.redhat.com/articles/6380311)

## Migration Types
Forklift supports cold migration from oVirt (oVirt) and warm migration from both VMware vSphere and from oVirt.

### Cold migration
Cold migration is the default migration type where the source's virtual machines are shut down while the data is copied.

### Warm migration
Warm migration copies most of the data during the precopy stage while the source virtual machines (VMs) are running. During a time of minimal data transactions, the cutover stage shuts down the VMs and the remaining data is copied.

## Migration Stages
Forklift operates in two stages, precopy and cutover.

### Precopy stage
The VMs are not shut down during the precopy stage.  Instead the VM disks are copied incrementally using [changed block tracking (CBT)](https://kb.vmware.com/s/article/1020128) snapshots. The snapshots are created at one-hour intervals by default but can be changed by updating the forklift-controller deployment.

**Important** CBT must be enabled for each source VM and each VM disk.

A VM can support up to 28 CBT snapshots. If the source VM has too many CBT snapshots and the Migration Controller service is not able to create a new snapshot, warm migration might fail. The Migration Controller service deletes each snapshot when it is no longer required.

The precopy stage runs until the cutover stage is started manually or is scheduled to start.

### Cutover stage
The VMs are shut down during the cutover stage and the remaining data is migrated. Data stored in RAM is not migrated.

Start the cutover stage manually by using the Forklift console or by scheduling a cutover time in the Migration manifest.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Forklift/overview.md)
