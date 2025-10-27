---
title: "Configuring the LLM for analysis"
date: 2025-12-09T14:58:52-06:00
draft: false
---
Konveyor AI provides the large language model (LLM) with the contextual prompt, migration hints, and solved examples to generate suggestions for resolving issues identified in the current code.

Konveyor AI is designed to be model agnostic. It works with LLMs that are run in different environments (in local containers, as local AI, oras a shared service) to support analyzing Java applications in a wide range of scenarios. You can choose an LLM from well-known providers, local models that you run from Ollama or Podman desktop, and OpenAI API-compatible models.

The code fix suggestions produced to resolve issues detected through an analysis depend on the LLM’s capabilities.

You can run an LLM from the following generative AI providers:

- OpenAI

- Azure OpenAI

- Google Gemini

- Amazon Bedrock

- Ollama

You can also run OpenAI API-compatible LLMs deployed as:

- A service in your Kubernetes cluster

- Locally in the Podman AI Lab in your system.


