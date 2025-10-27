---
title: "Solution Server configurations"
date: 2025-12-12T14:58:52-06:00
draft: false
---
Solution Server is a component that allows Konveyor AI to build a
collective memory of source code changes from all analysis performed in an organization. When you request code fix for issues in the Visual Studio (VS) Code, the Solution Server augments previous patterns of how source code changed to resolve issues (also called solved examples) that were similar to those in the current file, and suggests a resolution that has a higher confidence level derived from previous solutions.

After you accept a suggested code fix, the Solution Server works with the large language model (LLM) to improve the hints about the issue that becomes part of the context. An improved context enables the LLM to generate more reliable code fix suggestions in future cases.

The Solution Server delivers two primary benefits to users:

- **Contextual Hints**: It surfaces examples of past migration solutions
  — including successful user modifications and accepted fixes —
  offering actionable hints for difficult or previously unsolved
  migration problems.

- **Migration Success Metrics**: It exposes detailed success metrics for
  each migration rule, derived from real-world usage data. These metrics
  can be used by IDEs or automation tools to present users with a
  “confidence level” or likelihood of Konveyor AI successfully migrating
  a given code segment.

Solution Server is an optional component in Konveyor AI. You must
complete the Tackle configurations before you can place a code
resolution request.

| LLM Provider (Tackle CR value) | Large language model examples for Tackle CR configuration|
|--------------------------------|--------------------------------|
| OpenShift AI platform          | Models deployed in an OpenShift AI                                               |
| Open AI (openai)               | `gpt-4`, `gpt-4o`,`gpt-4o-mini`                                           |
| Azure OpenAI (azure_openai)    | `gpt-4`,`gpt-35-turbo`                                                        |
| Amazon Bedrock (bedrock)       | `anthropic.claude-3-5-sonnet-20241022-v2:0`,   `meta.llama3-1-70b-instruct-v1:0` |
| Google Gemini (google)         | `gemini-2.0-flash-exp`, `gemini-1.5-pro`                                       |
| Ollama (ollama)                | `llama3.1`, `codellama`,`mistral`  |
