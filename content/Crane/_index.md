+++
title = "Crane"
date = 2022-04-18T19:17:22-06:00
weight = 1
chapter = true
pre = "<b>1 </b>"
+++
Crane is a tool that helps application owners migrate Kubernetes workloads and their state between clusters, remove environment-specific configuration and automate application deployments along the way. The work is spread among a few projects:

crane: The command line tool that brings the ability to migrate applications to the terminal.
crane-lib: This is the brains behind crane actions and is responsible for transforming resources.
crane-plugin-openshift: Plugin specifically tailored to managing the migration of OpenShift workloads. Also serves as an example of codifying knowledge/best-practices in a repeatable way.
crane-plugins: Collection of plugins from the konveyor community based on our experience with Kube migrations.
NOTE:

Additional plugins will be added as new “crane-plugin-” repos
CLI commands and plugins are explained in depth in later part of the document, visit Usage to understand how crane works.
Why crane is needed?
Crane is the product of our team distilling several years of experience performing large-scale production Kubernetes migrations. These operations are large, complex, error-prone, and usually must be peformed under a limited window of time. Because of that challenge, its paramount that a migration tool be designed with transparency and ease-of-diagnostics in mind. Crane is designed to drive a migration via a pipeline of non-destructive tasks that dump their results to disk so the operation can be easily audited and versioned without ever impacting live workloads. The tasks are idempotent, meaning they can be run repeatedly and will output consistent results given the same inputs without side-effects on the system at large.
