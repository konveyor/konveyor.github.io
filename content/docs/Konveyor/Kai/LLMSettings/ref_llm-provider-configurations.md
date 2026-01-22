---
title: "Configuring the LLM provider settings"
date: 2025-12-08T14:58:52-06:00
draft: false
---
Konveyor AI is large language model (LLM) agnostic and integrates with an LLM of your choice. To enable Konveyor AI to access your large language model (LLM), you must enter the LLM provider configurations in the `provider-settings.yaml` file.

The `provider-settings.yaml` file contains a list of LLM providers that are supported by default. The mandatory environment variables are different for each LLM provider. Depending on the provider that you choose, you can configure additional environment variables for a model in the `provider-settings.yaml` file. You can also enter a new provider
with the required environment variables, the base URL, and the model name.

The provider settings file is available in the Konveyor Visual Studio (VS) Code extension.

Access the `provider-settings.yaml` from the VS Code Command Palette by typing `Open the GenAI model provider configuration file`.

You can select one provider from the list by using the `&active` anchor in the name of the provider. To use a model from another provider, move the `&active` anchor to ***one*** of the desired provider blocks.

For a model named "my-model" deployed in Red Hat OpenShift AI with
"example-model" as the serving name:
  
  ```yaml
    models:
      openshift-example-model: &active
        environment:
          CA_BUNDLE: "<Servers CA Bundle path>"
        provider: "ChatOpenAI"
        args:
          model: "my-model"
          configuration:
            baseURL: "https://<serving-name>-<data-science-project-name>.apps.konveyor-ai.example.com/v1"
  ```

When you change the `model` deployed in Red Hat OpenShift AI, you must also change the `model` argument and the `baseURL` endpoint.

If you want to select a public LLM provider, you must move the `&active` anchor to the desired block and change the provider arguments.

For an OpenAI model:

```yaml
  OpenAI: &active
    environment:
      OPENAI_API_KEY: "<your-API-key>" # Required
    provider: ChatOpenAI
      args:
        model: gpt-4o # Required
```

For Azure OpenAI:

```yaml
  AzureChatOpenAI: &active
    environment:
      AZURE_OPENAI_API_KEY: "" # Required
    provider: AzureChatOpenAI
      args:
        azureOpenAIApiDeploymentName: "" # Required
        azureOpenAIApiVersion: "" # Required
```

For Amazon Bedrock:
```yaml
  AmazonBedrock: &active
    environment:
      ## May have to use if no global `~/.aws/credentials`
      AWS_ACCESS_KEY_ID: "" # Required if a global ~/.aws/credentials file is not present
      AWS_SECRET_ACCESS_KEY: "" # Required if a global ~/.aws/credentials file is not present
      AWS_DEFAULT_REGION: "" # Required
    provider: ChatBedrock
      args:
        model: meta.llama3-70b-instruct-v1:0 # Required
```

It is recommended to use the [AWS CLI](https://aws.amazon.com/cli/) and
verify that you have command line access to AWS services before you proceed with the `provider-settings` configurations.

For Google Gemini:
```yaml
  GoogleGenAI: &active
    environment:
      GOOGLE_API_KEY: "" # Required
    provider: ChatGoogleGenerativeAI
      args:
        model: gemini-2.5-pro # Required
```

For Ollama:
```yaml
  models:
    ChatOllama: &active
      provider: "ChatOllama"
      args:
        model: "granite-code:8b-instruct"
        baseUrl: "127.0.0.1:11434" # example URL
```
