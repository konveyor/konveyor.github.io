---
title: "Overview"
date: 2022-07-08T14:32:29-06:00
draft: true
---
The Crane tool helps application owners migrate Kubernetes workloads and their state between clusters, remove environment-specific configuration, and automate application deployments along the way.

The process uses a few tools:

- **crane:** The command line tool that migrates applications to the terminal.
- **crane-lib:** The brains behind Crane functionality responsible for transforming resources.
- **crane-plugin-openshift:** Plugin specifically tailored to manage OpenShift migration workloads and an example of a repeatable best-practice.
- **crane-plugins:** Collection of plugins from the Konveyor community based on experience from performing Kube migrations.

## Why crane is needed?

Crane is the product of several years of experience performing large-scale production Kubernetes migrations that are large, complex, error-prone, and usually peformed in a limited window of time.

To face those challenges, the Crane migration tool is designed with transparency and ease-of-diagnostics in mind. It drives migration through a pipeline of non-destructive tasks that output results to disk so the operation can be easily audited and versioned without ever impacting live workloads. The tasks can be run repeatedly and will output consistent results given the same inputs without side-effects on the system at large.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Crane/overview.md)
