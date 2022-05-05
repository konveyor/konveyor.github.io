---
title: "About"
date: 2022-05-03T09:57:46-06:00
draft: true
---

Tackle is a collection of tools that support the modernization and migration of applications to Kubernetes. These tools assess applications to determine which option is the appropriate migration strategy for each application:
* Rehosting
* Replatforming
* Refactoring

Tackle uses an interactive questionnaire for the assessment which enables key stakeholders to gather information about applications, discuss risks flagged by Tackle, and reach a consensus in formulating recommendations for each application.

## Tackle Refactoring Tools
The tools are cloud-native microservices that are accessible from a common Tackle UI.
* Application Inventory
* Pathfinder
* Controls

### Application Inventory
Tackle Application Inventory is the vehicle which selects applications for assessment by Pathfinder. It provides users four  main functionalities:
* Maintain a portfolio of applications.
* Link applications to the business services they support.
* Define interdependencies.
* Add metadata using an extensible tagging model to describe and categorize applications in multiple dimensions.

### Pathfinder
Tackle Pathfinder is  an interactive questionnaire based tool that assesses the suitability of applications for modernization in order to deploy them into containers on an enterprise Kubernetes platform. The tool output includes::
* Application’s suitability for Kubernetes
* Associated risks
* Adoption plan with  the applications’ prioritization, business criticality and dependencies.

### Controls
Tackle Controls are a collection of entities that add value to Application Inventory and the Pathfinder assessment. They comprise business services, stakeholders, stakeholder groups, job functions, tag types, and tags.

Tackle Controls are a collection of entities that add value to the Application Inventory and the Pathfinder assessment including:
* Business services
* Stakeholders
* Stakeholder groups
* Job functions
* Tag types
* Tag
