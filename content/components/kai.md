---
title: "Konveyor AI (Kai)"
type: "components"
header_image: /images/kai.gif
subtitle: > 
    Combine static code analysis with Generative AI. Seamlessly integrate LLMs into your modernization to speed up your refactoring journey.
features:
- src: /images/code-gen-kai.svg
  title: Contextual code generation
  description: > 
    Static code analysis provides context for the LLM to generate meaningful results. Large language models have a context limit, and they do not have a memory to remember each generation. It's not possible to send thousands of lines of code with its dependencies and context. By leveraging the Konveyor static code analysis, we break down complex problems into more manageable ones with more context. And use this to create more meaningful code generations.
- src: /images/improve-output-kai.svg
  title: Improve output with each migrated application
  description: > 
    Calling an LLM repeatedly for the same code generations can quickly become expensive. For example, a typical pattern like logging is used in almost all applications. Konveyor AI remembers and learns each code generation that solves an issue, reducing the number of calls to an LLM for similar generations.
- src: /images/model-agnostic-kai.svg
  title: Model Agnostic
  description: > 
    LLMs are powerful tools, but without explicit guidance, they can generate useless content. Kai combines Retrieval Augmented Generation (RAG) with your choice of LLM to extract relevant information from Konveyor static-code analysis reports and change-log history of successfully migrated applications in your repos. Because of our RAG approach, Kai can generate solutions without needing to fine-tune the model.
cta_primary: 
  title: Get Started
  url: https://github.com/konveyor/kai/tree/main/docs#installation
cta_secondary: 
  title: Give us feedback
  url: /docs/konveyor/contributetokonveyor/
---