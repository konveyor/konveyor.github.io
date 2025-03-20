---
author: konveyor
date: 2022-10-20T07:00:00Z
description: >-
  We recently revealed upcoming changes to the Konveyor Project. These adjustments are designed to solve the most important application modernizations problem better and to encourage participation with our community.
featured: true
image: banner.jpg
tags:
  - Crane
  - Forklift
  - Move2Kube
  - Pelorus
  - Tackle
title: 'Community Update: Konveyor Refocuses Efforts on Replatforming and Refactoring Use Cases'
---

In the latest [community update](https://youtu.be/jyd3mpvVCC4), we revealed upcoming changes to the Konveyor Project. These adjustments are designed to solve the most important application modernizations problem better and to encourage participation with our community. Here’s what we announced:

- We will solely focus on solving for the replatforming and refactoring use cases.
- It’s easier to contribute and collaborate with Konveyor thanks to our new special interest group (SIG) model.
- You can learn the Konveyor project, see upcoming features, and give us feedback at our workshop — part of the OpenShift Commons Gathering at KubeCon NA 2022. [Register for the Gathering](https://hopin.com/events/openshift-commons-gathering-2022-at-kubecon-na-detroit) to secure your spot.
- Tackle 2.1 is available, future Tackle releases will be branded Konveyor.
- Konveyor roadmap includes migration wave management, multi language support, and integrations with modernization tools.

You can get the details of each announcement below.

## We are refocusing efforts on replatforming and refactoring use cases

A key insight the [State of Application Modernization Report](/modernization-report/) surfaced is that users are more interested in replatforming and refactoring then just rehosting workloads or “lifting and shifting”. Paired with feedback from users, this revealed a gap in the tooling for refactoring which Konveyor is now addressing

Originally, Konveyor took a “big tent” approach to rehosting, replatforming and refactoring. We brought the tooling to a community of engineers, developers, and users to figure out what problems they need to solve. Now, due to their feedback, Konveyor is refocusing on just replatforming and refactoring, meaning:

- Pelorus, Tackle and Move2Kube will be integrated and moved into a single project, now called Konveyor.
- Crane (rehosting from one Kubernetes cluster to another) will be moved into a separate GitHub organization.
- Forklift (rehosting to VMs) will move into a KubeVirt project focused on running VM’s with Kubernetes.

Crane and Forklift will continue to exist but will no longer be part of the core Konveyor toolset.

![Project changes: chart showing what will happen to each of the subprojects after the refocus.](project-changes.svg)

## Special interest group (SIG) model makes it easier to contribute and collaborate with the project

One of the major changes to the Konveyor project is the refocusing on the major areas of code, UI, analyzers, and add-ons, each of which is represented by a special interest group (SIG) modeled after the SIGs created by the Kubernetes community. These groups are responsible for the development, integration, maintenance, and collaboration of the project.

The SIG Core is responsible for all of the core features of the Konveyor platform. These features include but are not limited to modernization, report generation, integration of program management tools, and the design of implementation of the hub itself.

SIG UI is responsible for the user interface and experience across the platform. This SIG is also responsible for creating a pattern where all of the add-ons can be dynamically integrated into the hub.

The SIG Analyzers are working on creating an extensible pattern and standardization of input and output for the community to collaborate on the analyzers. The analyzers will each target specific programming languages.

Finally SIG Add-ons are responsible for the development and integration of add-ons that will enhance Konveyor’s capabilities.

The community meetings bring together contributors and collaborators to provide a forum to discuss topics and new ideas and generate innovation, and happen on every first and third Thursday at 9 AM ET ([meetings are recorded](https://youtu.be/tiEJEiT8XtI)). Topics include milestones and features of upcoming releases, contributor showcases with member feedback. Above all, these meetings are intended to foster the community around Konveyor.

There are also many asynchronous opportunities to communicate and collaborate around Konveyor.

- There is a #konveyor channel on the Kubernetes Slack channel. You can use it to ask any question about the project.
- Contributors can subscribe to the [_Konveyor-community email list_](https://groups.google.com/u/0/g/konveyor-community) for community meeting invites and discussions to influence the direction of the community.
- To get general updates, you can subscribe to the [email list](/community/#sign-up).
- Or for more information, look at the Konveyor community repo — it includes information around governance documents, contributor letter, Get Started Guide, meeting agendas and developer notes.

## Attend the Konveyor workshop at KubeCon North America 2022

We will run a workshop in the OpenShift Commons Gathering on 25 October, 2022. At this free, hands-on workshop, workshop leaders will take you through an assesment and analysis of a sample application using the Konveyor platform, followed by discussion of the Konveyor Roadmap and Q&A. Whether you are starting or continuing your application modernization journey, this is a great opportunity to learn how to contribute, or just how to use the tools. If you have any questions or just want to connect with the community or workshop, please reach out to Savita Raghunathan at [sraghuna@redhat.com](mailto:sraghuna@redhat.com) or on [Slack](/slack/).

### How to register:

- [Visit registration site](https://hopin.com/events/openshift-commons-gathering-2022-at-kubecon-na-detroit) and follow the directions below
- Click "register"
- Select "Konveyor workshop"
- Click "continue"
- Provide your info and submit

## Tackle 2.1 is available, future Tackle releases will be branded Konveyor

Tackle 2.1 is available, with the ability to upgrade your current Tackle 2.0. Tackle 2.1 will be the final Tackle release. Future releases will bear the name Konveyor, and will begin with version 3.

The release of Konveyor 3 is envisioned for Q1 2023 pending the restructuring of the architecture, and some specific components to make contribution easier. For some of the more visionary components of Konveyor 4, the goal is to have these features ready for release in 2H 2023.

In Tackle 2.1 there are several important features:

Integration with [**DiVA** (_Data intensive Validity Advisor_)](https://github.com/konveyor/tackle-diva), which is now seamlessly integrated with Tackle: everything is available in the domain user interface to enable the new analysis mode. A transcription report tab is now included in analysis reports with detail on the data layer of your applications.

The next important feature is the ability to bulk delete applications. Before 2.1 it was possible to import applications in bulk, but not delete them. It will also include automated creation of missing entities on CSV import.

New rules will be included in the next patch to 2.1.1, allowing new migration paths for open JDK 11 to 17 rules, and with Microsoft as a new major player in the Konveyor community, migration to EAP and Spring Boot applications on top of Azure App Service is supported.

Finally, the Konveyor community did a ton of bug fixing and optimization making this release much more stable than Tackle 2.0.

## Konveyor roadmap

![Diagram showing Konveyor roadmap for the next year](roadmap.svg)

Looking forward to next year, several important features are visualized for the first release of Konveyor 3. First of these is **Migration Waves Management**. No one does a “big bang migration” when dealing with thousands of applications, but rather distributes application portfolio modernization across multiple waves. This new feature offers an assisted method for managing these waves, integrated into the main Konveyor user interface.

Full integration with [**Tackle Test Generator**](https://github.com/konveyor/tackle-test-generator-cli) and [**Tackle Container Advisor**](https://github.com/konveyor/tackle-container-advisor#readme) in the main user interface is envisioned for this release as well. With Tackle Test Generator, tests for an application can be stored in your Git repository with a single click.

Tackle Container Advisor will leverage its’ entity standardization capability using the natural language description of the technology stack of the application, making it easier to ingest data at scale, simplifying CSV imports and Excel importing, and eliminating the need to code the technology stack as a series of separate tasks

_Integration with Jira_ will allow the user to track and manage the whole migration process. Tackle will allow delegation of implementation to external issue managers or task managers. By creating issues in Jira related to the applications within the portfolio, a project leader can then assign them to developers to perform changes in the application portfolio.

_Custom Migration Targets_ will be improved by abstracting users away from the complexities of having to assign custom rules without having to deal with these custom rules directly. Information and status on this feature should be available soon.

_Custom rule upload_ to repositories will be integrated going forward to allow these rules to be uploaded automatically, and to be retrieved to and from source code repositories.

_Tag management_ will also be improved, with numerous tags assigned to applications it is difficult for the UI to scale this information or consume it. This feature will help make these tags easier to consume.

Looking even further into the future, Konveyor 4 will bring a whole new set of advanced features including multi language support for analysis, manifest generation and enhanced assessment module.

Every time Konveyor has been presented to an organization, invariably there is a request for multi language support. In an early prototype the Konveyor community has demonstrated the ability to use language server technology from Microsoft, decoupling the analysis from data models supporting analysis results. It’s very possible that this feature will be ready to ship with Konveyor 4 in May.

_Manifest generation_ is another important feature: once you’ve successfully adapted source code to run on containers, this will then install the manifest on Kubernetes.

Finally, feedback on the current **Assessment Module** is that it’s too opinionated, so the goal is to make it more intelligent and flexible.

Altogether, the Konveyor Project has undergone a major transformation improving and simplifying, and creating new and better opportunities for building a community around Application Modernization centered around replatforming and refactoring.
