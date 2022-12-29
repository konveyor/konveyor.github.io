+++
title = "Tackle"
date = 2022-04-18T19:17:45-06:00
weight = 4
chapter = false
pre = "<b>4 </b>"
+++

Tackle is a collection of tools that support modernizing and migrating applications to Kubernetes. These tools assess applications to determine which option is the appropriate migration strategy for each application:
* Rehosting
* Replatforming
* Refactoring

Tackle uses an interactive questionnaire for the assessment which enables key stakeholders to gather information about applications, discuss risks flagged by Tackle, and reach a consensus in formulating recommendations for each application.

## Tackle Refactoring Tools
The tools are cloud-native micro-services that are accessible from a common [Tackle UI](https://github.com/konveyor/tackle-ui/).
* Application Inventory
* Pathfinder
* Controls

### Application Inventory
[Tackle Application Inventory](https://github.com/konveyor/tackle-application-inventory) is the vehicle which selects applications for assessment by Pathfinder. It provides users four  main functions:
* Maintain a portfolio of applications.
* Link applications to the business services they support.
* Define interdependencies.
* Add metadata using an extensible tagging model to describe and categorize applications in multiple dimensions.

### Pathfinder
[Tackle Pathfinder](https://github.com/konveyor/tackle-pathfinder) is  an interactive questionnaire based tool that assesses the suitability of applications for modernization in order to deploy them into containers on an enterprise Kubernetes platform. The tool output includes::
* Application’s suitability for Kubernetes
* Associated risks
* Adoption plan with  the applications’ prioritization, business criticality and dependencies.

### Controls
[Tackle Controls](https://github.com/konveyor/tackle-controls) are a collection of entities that add value to Application Inventory and the Pathfinder assessment. They comprise business services, stakeholders, stakeholder groups, job functions, tag types, and tags.

Tackle Controls are a collection of entities that add value to the Application Inventory and the Pathfinder assessment including:
* Business services
* Stakeholders
* Stakeholder groups
* Job functions
* Tag types
* Tag

## Tackle Projects
* [Tackle Web UI](https://github.com/konveyor/tackle-ui)
* [Tackle Application Inventory](https://github.com/konveyor/tackle-application-inventory)
* [Tackle Pathfinder](https://github.com/konveyor/tackle-pathfinder)
* [Tackle Controls](https://github.com/konveyor/tackle-controls)
* [Tackle Documentation](https://github.com/konveyor/tackle-documentation)
* [Tackle Commons REST](https://github.com/konveyor/tackle-commons-rest)
* [Tackle Keycloak Theme](https://github.com/konveyor/tackle-keycloak-theme)
* [Tackle DiVA](https://github.com/konveyor/tackle-diva)
* [Tackle Test Generator](https://github.com/konveyor/tackle-test-generator-cli)
* [Tackle Container Advisor](https://github.com/konveyor/tackle-container-advisor)

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/_index.md)

