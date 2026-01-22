---
title: "Getting started with Konveyor AI"
date: 2025-12-13T14:58:52-06:00
draft: false
---
The Getting started section contains information to walk you through the
prerequisites, persistent volume requirements, installation, and
workflows that help you to decide how you want to use the Konveyor AI.

## Prerequisites

This section lists the prerequisites required to successfully use the
generative AI features in the Konveyor AI Visual Studio (VS) Code
extension.

Before you install Konveyor AI, you must:

- Install Java v17 and later

- Install Maven v3.9.9 or later

- Install Git and add it to the $PATH variable

- Install the Konveyor Operator 8.0.0

  The Konveyor Operator is mandatory if you plan to enable the Solution
  Server. Solution Server provides context for the large language model (LLM) for generating code changes. It enables you to log in to the `konveyor-ai` project where you must enable the Solution Server in the Tackle custom resources (CR).

- Create an API key for an LLM.

  You must enter the provider value and model name in Tackle CR to
  enable generative AI configuration in the Konveyor VS Code plugin.
  

| LLM Provider (Tackle CR value) | Large language model examples for Tackle CR configuration|
|--------------------------------|--------------------------------|
| OpenShift AI platform          | Models deployed in OpenShift AI                                               |
| Open AI (openai)               | `gpt-4`, `gpt-4o`,`gpt-4o-mini`                                           |
| Azure OpenAI (azure_openai)    | `gpt-4`,`gpt-35-turbo`                                                        |
| Amazon Bedrock (bedrock)       | `anthropic.claude-3-5-sonnet-20241022-v2:0`,   `meta.llama3-1-70b-instruct-v1:0` |
| Google Gemini (google)         | `gemini-2.0-flash-exp`, `gemini-1.5-pro`                                       |
| Ollama (ollama)                | `llama3.1`, `codellama`,`mistral`|


**Note:** The availability of public LLM models is maintained by the respective LLM provider.

## Persistent Volume

The Solution Server component requires a backend database to store code changes from previous analyses.

If you plan to enable Solution Server, you must create a `5Gi` `RWO` persistent volume used by the Konveyor AI database.


## Installation

You can install the Konveyor AI extension from the [Konveyor AI release page in GitHub](https://github.com/konveyor/editor-extensions/releases/tag/v0.2.0).

You can use the Konveyor VS Code plug-in to perform analysis and
optionally enable KAI to use generative AI capabilities. Use KAI to fix code issues before migrating the application to target technologies by using the generative AI capabilities.