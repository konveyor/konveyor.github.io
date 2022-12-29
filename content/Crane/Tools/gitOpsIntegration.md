---
title: "Integrating GitOps"
date: 2022-04-14T15:22:46-06:00
draft: false
---
All Crane commands are individual utilities, but when used together in sequence, they form a pipeline.

Crane makes it easy to integrate GitOps that apply the patches/resources generated at the end of the `apply` command on the destination cluster. The resources generated at the end of the process (i.e `export`, `transform`, `apply`) can be pushed to a GitHub repository, and a pipeline can be created to deploy the resources on a cluster on every push.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Crane/Tools/GitOpsIntegration.md)
