---
title: "Step Two: Transform"
date: 2022-04-22T10:29:06-06:00
draft: false
---
The Transform command facilitates the changes to the exported resources that are frequently necessary when migrating workloads between one environment to another. For example:

- Stripping the resource status information that is no longer relevant after the resource is serialized out of a cluster.
- Adjusting resource quotas to fit the destination environment.
- Altering node selectors to match the new environment if the node labels do not match the source environment.
- Applying custom labels or annotations to resources during the migration.

Because most changes are specific to an environment, Crane is designed for total customization and the transform command accepts a “plugins” directory argument.

Each plugin is an executable with a well defined stdin/out interface allowing for customization or installation and use of published generic plugins.

After exporting the resources into a local directory and installing the desired transformation plugins, the `crane transform` command can run. The output is placed in a directory with a set of transform files that describe the changes that need to be applied to the original resources before their final import. The changes are written in the JSONPatch format, are human readable, and easily hackable.

This command generates a patch to add an annotation `transform-test:test` for objects in the `export` directory and the `transform` directory to be used as input for `apply` command.

```
crane transform -e export -p plugins -t transform --optional-flags="add-annotations=transform-test:test"
```

Run the following to see the list of available optional commands for configured plugins.
```
crane transform optionals
```
See Managing PlugIns for more information on plugins that can be consumed by the transform command.
