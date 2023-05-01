+++
title = "Konveyor"
date = 2022-04-18T19:17:45-06:00
weight = 1
chapter = false
+++

Konveyor helps organizations safely and predictably modernize their tradional/legacy applications to Kubernetes.

Konveyor’s goal is to deliver a [Unified Experience](https://github.com/konveyor/enhancements/tree/master/enhancements/unified_experience) to the organizations embarking on their modernization journey. It follows a simple yet effective approach of surfacing the information about the application to aid a ‘Decision Maker’ to make decisions about their modernization and migration needs, plan the work in the form of ‘Migration waves’ and provide guidance to the developers to complete the needed migration/modernization by providing assets as well as a catalog of integrated tools to aid specific workflows.

## Components of Konveyor

Konveyor has three main components as follows,
* Application Inventory
* Assessment module
* Analysis module

### Application Inventory
Application Inventory houses the organization’s portfolio of legacy applications. It provides mechanisms to link them to their respective supported business services, define their interdependencies, and use an extensible tagging model to add metadata to describe and categorize them in multiple dimensions.

### Assessment module
Assessment is a questionnaire-based tool that assesses the suitability of applications for modernization. The self-guided questionnaire is designed to bring out the risks associated with the application and the application’s suitability for Kubernetes. The result is available in the form of reports that can be used to generate an adoption plan influenced by various criteria. 

### Analysis module
The analysis module examines the application’s source code/binary and its dependencies with a set of predefined/custom rules. It generates a report with a list of issues that need to be addressed to move the application to Kubernetes and provides an effort estimation for the same.

## Features

Konveyor supports the following exciting features:

* **Administrator perspective:** Dedicated perspective to manage tool-wide configuration.
* **Enhanced RBAC:** Three new differentiated personas with different permissions - Administrator, Architect and Migrator
* **Integration with repositories:** Full integration with source code (Git, Subversion) and binaries (Maven) repositories to automate the retrieval of applications for analysis.
* **Credentials management:** Secure store for multiple credential types (source control, Maven settings files, proxy). Credentials are managed by Administrators and assigned by Architects to applications.
* **Proxy integration:** HTTP and HTTPS proxy configuration can be managed in the Tackle UI.
* **Analysis module:** Full integration with the Windup project to allow the execution of application analysis from the application inventory.
* **Enhanced analysis modes:** Aside from source and binary analysis modes, now Tackle includes the Source + Dependencies mode that parses the POM file available in the source repository to gather dependencies from corporate or public artifact repositories, adding them to the scope of the analysis.
* **Analysis scope selection:** Simplified user experience to configure the analysis scope, with the possibility to force the analysis of known Open Source libraries.
* **Authless deployment:** Tackle can now be optionally deployed without Keycloak, allowing full unauthenticated admin access to the tool. This is especially useful when deploying the tool in resource constrained environments like local instances of Minikube, where only a single user would have access to it.
* **Seamless upgrades:** Tackle lifecycle is now managed by a new operator with Capability Level II, allowing seamless upgrades between GA versions.

## Konveyor Projects
* [UI](https://github.com/konveyor/tackle-ui)
* [Application Inventory](https://github.com/konveyor/tackle-application-inventory)
* [Assessment](https://github.com/konveyor/tackle-pathfinder)
* [Analyzer](https://github.com/konveyor/analyzer-lsp)

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/_index.md)

