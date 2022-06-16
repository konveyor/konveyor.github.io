---
title: "Welcome to Tackle 2.0"
date: 2022-06-14T14:59:30-06:00
draft: false
---
[Tackle documentation](https://konveyor.github.io/tackle/tackle2/)

Tackle 2.0 includes the seamless integration with the analysis capabilities of the Windup project, also known downstream as Migration Toolkit for Applications, enabling Tackle to effectively manage, assess and now also analyze applications to have a holistic view at your portfolio when dealing with large scale modernization and Kubernetes adoption initiatives. This is essential to provide key insights that allow Architects leading these massive projects to make informed decisions, thus reducing risks and making the required effort measurable and predictable.

The following is a list of the exciting new features included in Tackle 2.0:

* **Administrator perspective:** Dedicated perspective to manage tool-wide configuration, with a similar approach and design to the OpenShift Administrator Perspective..
* **Enhanced RBAC:** Three new differentiated personas with different permissions - Administrator, Architect and Migrator
* **Integration with repositories:** Full integration with source code (Git, Subversion) and binaries (Maven) repositories to automate the retrieval of applications for analysis.
* **Credentials management:** Secure store for multiple credential types (source control, Maven settings files, proxy). Credentials are managed by Administrators and assigned by Architects to applications.
* **Proxy integration:** HTTP and HTTPS proxy configuration can be managed in the Tackle UI.
* **Analysis module:** Full integration with the Windup project to allow the execution of application analysis from the application inventory.
* **Enhanced analysis modes:** Aside from source and binary analysis modes, now Tackle includes the Source + Dependencies mode that parses the POM file available in the source repository to gather dependencies from corporate or public artifact repositories, adding them to the scope of the analysis.
* **Analysis scope selection:** Simplified user experience to configure the analysis scope, with the possibility to force the analysis of known Open Source libraries.
* **Authless deployment:** Tackle can now be optionally deployed without Keycloak, allowing full unauthenticated admin access to the tool. This is especially useful when deploying the tool in resource constrained environments like local instances of Minikube, where only a single user would have access to it.
* **Seamless upgrades:** Tackle lifecycle is now managed by a new operator with Capability Level II, allowing seamless upgrades between GA versions.

[Find more documentation here](https://konveyor.github.io/tackle/tackle2/) or by clicking Tackle 1.0 & 2.0 in the left pane. In case you find anything missing, please let us know by [creating an issue in the Konveyor documentation repository](https://github.com/konveyor/konveyor.github.io/issues).
