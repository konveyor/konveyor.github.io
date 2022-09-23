---
title: "Overview"
date: 2022-07-08T14:41:25-06:00
draft: false
weight: 1
---
The Move2Kube tool helps application owners migrate legacy workloads to run on Kubernetes clusters and eventually automate their deployments after multiple iterations. It analyzes Docker Compose files, Cloud Foundry manifest files, and even source code to generate Kubernetes deployment files including object YAML files, Helm charts, and operators.

Move2Kube has a very modular architecture making it easy to custom functionality for a large variety of migration use-cases.

The project includes three tools:

- [move2kube](https://github.com/konveyor/move2kube): The primary tool is the command line interface (CLI) that takes in application source code and generates Kubernetes artifacts.
- [move2kube-ui](https://github.com/konveyor/move2kube-ui): A UI for interacting with the Move2Kube CLI tool for running fully-managed Move2Kube runtimes.
- [move2kube-transformers](https://github.com/konveyor/move2kube-transformers): A collection of useful transformers for extending Move2Kube's functionality that has been built by the Konveyor community based on experience from performing migrations for clients.

View this presentation for an overview of Move2Kube.

<object data="../../assets/pdfs/Move2Kube.pdf" type='application/pdf' width="1000" height="600"></object>


[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Move2Kube/overview.md)
