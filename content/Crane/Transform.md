---
title: "Transforming workloads"
date: 2022-04-14T15:21:35-06:00
draft: true
---
## Transforming workloads

Frequently when migrating workloads between one environment to another, There arises a need to change something about the resources before they’re imported into your target environment. A few examples include:

- Stripping the status information about a resources that isn’t relevant after the resource has been serialized out of a cluster.
- Adjusting resource quotas to fit destination environment.
- Altering node selectors to match new environment, if the node labels don’t match source environment.
- Applying custom labels or annotation to resources during the migration

A lot of these reasons are specific to an environment, so crane is designed to allow for total customization. The transform command accepts a “plugins” directory argument. Each plugin is an executable that has a well defined stdin/out interface, so you can easily write your own, or install and use those that have been published and are generically useful

After exporting all the resources in to a local directory and installing desired plugins that will help in the transformation of the exported resources, the actual step to transform all the resources can be carried out by `crane transform` command.

Think of the transform command as a function that accepts the set of exported resources you discovered initially, plus a set of plugins that are applied to each of those resources. Its output is going to be a directory with a set of “transform” files that describe the mutation that should be applied to the original resources before their final import. These mutations are expressed with the JSONPatch format, are human readable, and easily hackable.

Example
```
crane transform -e export -p plugins -t transform --optional-flags="add-annotations=transform-test:test"
```
The above command generate a patch to add an annotation `transform-test:test` for objects in `export` directory and places the same in `transform` directory to be used as input for `apply` command.

To see the list of available optional command for configured plugins, run the following command -
```
crane transform optionals
```
