---
title: "Migration plan options"
date: 2022-05-19T10:04:15-06:00
draft: false
---
On the Migration plans page of the Forklift web console, click the Options menu kebab beside a migration plan to access the following options:


* **Edit:** Edit the details of a migration plan. A migration plan cannot be edited while it is running or after it has completed successfully.
* **Duplicate:** Create a new migration plan with the same virtual machines (VMs), parameters, mappings, and hooks as an existing plan. Use this feature for the following tasks:
 * Migrating VMs to a different namespace.
 * Editing an archived migration plan.
 * Editing a migration plan with a different status, for example, failed, canceled, running, critical, or ready.
* Archive: Delete the logs, history, and metadata of a migration plan. The plan cannot be edited or restarted. It can only be viewed.

**Important:**The Archive option is irreversible, but an archived plan can be duplicated.

* **Delete:** Permanently remove a migration plan. A running migration plan cannot be deleted.

**Important** The Delete option is irreversible.

Deleting a migration plan does not remove temporary resources such as importer pods, conversion pods, config maps, secrets, failed VMs, and data volumes. (BZ#2018974) A migration plan must be archived before deleting it in order to clean up the temporary resources.

* **View details:** Display the details of a migration plan.
* **Restart:** Restart a failed or canceled migration plan.
* **Cancel scheduled cutover:** Cancel a scheduled cutover migration for a warm migration plan.
