---
author: rnissen
date: 2022-06-16T07:00:00Z
description: >-
  New management, assessment, and analysis capabilities will help you streamline the modernization of your application portfolio to Kubernetes. See what is new with the open source tool Tackle 2.0.
featured: true
image: tackle-2.png
tags:
  - Tackle
title: 'Tackle 2: New capabilities for modernizing applications to leverage Kubernetes'
---

By Ramón Román Nissen

We’re thrilled to announce that Tackle 2.0 is now available in [operatorhub.io](https://operatorhub.io/). This new release marks a major milestone for the Tackle project, with a new architecture focused on enhancing performance, scalability and more importantly, extensibility of the tooling available in the Tackle toolkit.

This version includes the seamless integration with the analysis capabilities of the Windup project, enabling Tackle to effectively manage, assess and now also analyze applications to have a holistic view at your portfolio when dealing with large scale modernization and Kubernetes adoption initiatives. This is essential to provide key insights that allow Architects leading these massive projects to make informed decisions, thus reducing risks and making the required effort measurable and predictable.

The following is a list of the exciting new features included in Tackle 2.0:

- **Administrator perspective**: Dedicated perspective to manage tool-wide configuration, with a similar approach and design to the OpenShift Administrator Perspective.
- **Enhanced RBAC**: Three new differentiated personas with different permissions — Administrator, Architect and Migrator
- **Integration with repositories**: Full integration with source code (Git, Subversion) and binaries (Maven) repositories to automate the retrieval of applications for analysis.
- **Credentials management**: Secure store for multiple credential types (source control, Maven settings files, proxy). Credentials are managed by Administrators and assigned by Architects to applications.
- **Proxy integration**: HTTP and HTTPS proxy configuration can be managed in the Tackle UI.
- **Analysis module**: Full integration with the Windup project to allow the execution of application analysis from the application inventory.
- **Enhanced analysis modes**: Aside from source and binary analysis modes, now Tackle includes the Source + Dependencies mode that parses the POM file available in the source repository to gather dependencies from corporate or public artifact repositories, adding them to the scope of the analysis.
- **Analysis scope selection**: Simplified user experience to configure the analysis scope, with the possibility to force the analysis of known Open Source libraries.
- **Authless deployment**: Tackle can now be optionally deployed without Keycloak, allowing full unauthenticated admin access to the tool. This is especially useful when deploying the tool in resource constrained environments like local instances of Minikube, where only a single user would have access to it.
- **Seamless upgrades**: Tackle lifecycle is now managed by a new operator with Capability Level II, allowing seamless upgrades between GA versions.

I would like to take the chance to congratulate the engineering teams involved in the development of this new release of Tackle for making this major leap forward possible. Of course, this has to be extended as well to the UI/UX, QE and Documentation teams that made sure our tool is usable and reliable.

Make sure you visit [the new official documentation website](https://konveyor.github.io/tackle2/) for Tackle 2 to get started with the tool. We will keep updating the documentation to make sure we cover all the use cases and scenarios in which Tackle can be used. In case you find anything missing, please let us know by [creating an issue in the Konveyor documentation repository](https://github.com/konveyor/konveyor.github.io/issues).

Happy modernizing!

---

_**Bio**: Ramón Román is the Product Manager for the Migration Toolkit for Applications within the Modernization and Migration Solutions team in Red Hat. Prior to that, he was part of Red Hat's Consulting organization, where he worked as an Architect in the field, helping customers succeed in large-scale application migrations and Red Hat OpenShift adoption projects._
