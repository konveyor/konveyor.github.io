---
title: "How to use Konveyor AI"
date: 2025-12-15T14:58:52-06:00
draft: false
---
You can opt to use Konveyor AI features to request a code fix suggestion after running a static code analysis of an application. Konveyor AI augments the manual changes made to code throughout your organization in different migration waves and creates a context that is shared with a large language model (LLM). 

The LLM suggests code resolutions based on the issue description, context, and previous examples of code changes to resolve issues.

To make code changes by using the LLM, you must enable the generative AI option. 

You can use Konveyor in one of the three ways after enabling generative AI in VS Code:

* Use LLM to generate code fix suggestion. 

* Use LLM along with the Solution Server 

* Use the LLM with the Agent AI. 

The configurations that you complete before you request code fixes depend on how you prefer to request code resolutions.

**NOTE:** If you make any configuration change after enabling the generative AI settings in the extension, you must restart the extension for the change to take effect.

To use the LLM for code fix suggestions:

- Enable the generative AI option in the Konveyor plugin extension
  settings.

- Activate the LLM provider in the `provider-settings.yaml` file.

- `Start` the RPC server to run the analysis and get code fix suggestions for the identified issues.

To use the Solution Server to provide an additional context for the LLM:

- Create a secret for your LLM key in the Kubernetes cluster.

- Enable the Solution Server in the Tackle custom resource (CR).

- Configure the LLM base URL and model in the Tackle CR.

- Enable the generative AI option in the Konveyor plugin extension
  settings.

- Add the Solution Server configuration in the `settings.json` file.

- Configure the profile settings and activate the LLM provider in the `provider-settings.yaml` file.

To use the agent mode for code fix suggestions:

- Enable the generative AI and the agent mode in the Konveyor plugin extension settings.

- Configure the profile settings and activate the LLM provider in the `provider-settings.yaml` file.
