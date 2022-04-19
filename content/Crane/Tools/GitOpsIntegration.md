---
title: "Integrating GitOps"
date: 2022-04-14T15:22:46-06:00
draft: false
---
## Integrating Gitops

All the crane commands are individual utilities, but when used together in sequence, they form a pipeline. Crane makes it easy to integrate a gitops that applies the patches/resources generated at the end of apply on the destination cluster. The resources generated at the end of the process (i.e export, transform, apply), can be pushed to a github repository, and a pipeline can be setup that deploys the resources on a cluster on every push.
