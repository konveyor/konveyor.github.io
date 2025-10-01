---
author: SavithaRaghunathan, DylanMurray
date: 2025-10-01
description: >-
    We are thrilled to announce the release of Konveyor 0.8.0! This milestone release brings together months of work from the community and introduces powerful new features, smoother user experiences, and deeper platform awareness to make application modernization faster, more reliable, and more developer-friendly.
featured: true
image: header.png
tags:
    - Konveyor
    - Kai
    - GenAI
    - AppModernization
    - Release
title: "Konveyor 0.8.0 Release Announcement"
---

**Author**: [Savitha Raghunathan](https://github.com/savitharaghunathan), [Dylan Murray](https://github.com/dymurray)

We are thrilled to announce the release of Konveyor 0.8.0!

This milestone release brings together months of work from the community and introduces powerful new features, smoother user experiences, and deeper platform awareness to make application modernization faster, more reliable, and more developer-friendly.

Konveyor’s mission has always been to help organizations modernize their applications to run in Kubernetes and cloud native environments with confidence. With 0.8.0, we take another major step toward that goal.

## What's New in 0.8.0

### Konveyor AI Enhancements

Konveyor AI (Kai), the AI-powered component of Konveyor, continues to evolve with new features:

#### Solution server
The new solution server acts as an institutional memory for modernization. It captures and reuses migration knowledge, so teams no longer need to start from scratch each time. This ensures more consistent, accurate, and scalable modernization guidance across projects.

Check out our demo scenario for a hands-on example [here](https://github.com/konveyor/kai/blob/main/docs/scenarios/migrating_custom_library_apps_using_solution_server_short_edition/Partially_Migrated_Apps_Scenario.md).

#### Improved User Experience (UX)
We've refined the user experience across the extension. The focus is on making Kai feel lightweight and natural in the developer workflow.

#### Agentic Workflow
This release introduces early support for agent-driven workflows. Instead of a single suggestion at a time, Kai can now coordinate multiple steps to help resolve complex migration issues. After an issue has been fixed, the agentic workflow communicates with Vscode to gather diagnostics information and determine if its changes caused any new issues such as linter errors, compilation errors, etc. In the process, it makes changes across multiple files such as configuration files, build files, dependencies. It iterates on issues until either they are completely resolved or maximum iterations are reached.This paves the way for richer, more automated modernization flows that still keep developers in the loop.

#### IDE Extension Marketplace Release
We're excited to announce that the Konveyor IDE extension is now available in the [Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=konveyor.konveyor)! 

### Platform Awareness & Asset Generation

In 0.8.0, we’ve added support for Platform Awareness and Asset Generation. Platform awareness enables users to associate source and target platforms with an application in the inventory, allowing users to import and discover applications from the source platform directly. In v0.8.0, Konveyor has support for Cloud Foundry as a source platform enabling users to directly discover running applications from a Cloud Foundry instance into their application inventory.

Additionally, new to v0.8.0 is the ability to create asset generators. Asset generators are templates that use facts from the application discovery process along with analysis details to dynamically generate deployment and build manifests for the application given a specified target profile. This allows migrators to use Konveyor to assist in the migration of applications from Cloud Foundry to a new target platform such as Kubernetes.

### Community & Contributions

This release wouldn’t have been possible without the help of the Konveyor community. From code contributions and bug fixes to design discussions and testing, your work continues to shape Konveyor into a robust and inclusive project.

A heartfelt thank you to everyone who contributed to 0.8.0!

## Get Started with Konveyor 0.8.0

We invite you to try out the latest release today: [Download and explore Konveyor 0.8.0](https://github.com/konveyor/operator)

Have feedback, ideas, or questions? We'd love to hear from you:

- [Create an issue on GitHub](https://github.com/konveyor/operator/issues/new)
- [Join the discussion in the community channels](https://github.com/konveyor/community/blob/main/README.md#communication)
- Share your modernization stories with us!


## Conclusion

Konveyor 0.8.0 marks a major step forward in making AI-powered, cloud native modernization practical and developer-friendly. With the new solution server, improved UX, agentic workflows, and platform awareness, we are confident this release will help teams modernize applications more smoothly than ever before.

Thank you for being part of this journey — and we can’t wait to see what you build and modernize with Konveyor!
