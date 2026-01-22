---
title: "Configuring the LLM secret key"
date: 2025-12-11T14:58:52-06:00
draft: false
---
You must configure the Kubernetes secret for the large language model (LLM) provider in the Kubernetes namespace where you installed the Konveyor Operator.

You must create a LLM API key secret in your Kubernetes cluster to produce the resources necessary for the Solution Server. If you do not configure the LLM API key secret, Konveyor AI does not create the resources necessary to run the Solution Server.

## Procedure

1.  Create a credentials secret named `kai-api-keys` in the
    `konveyor-ai` project.

    1.  For Amazon Bedrock as the provider, type:
        ```shell
            kubectl create secret generic aws-credentials \
             --from-literal=AWS_ACCESS_KEY_ID=<YOUR_AWS_ACCESS_KEY_ID> \
             --from-literal=AWS_SECRET_ACCESS_KEY=<YOUR_AWS_SECRET_ACCESS_KEY>
        ```
    2.  For Azure OpenAI as the provider, type:
        ```shell
            kubectl create secret generic kai-api-keys -n konveyor-ai \
             --from-literal=AZURE_OPENAI_API_KEY='<YOUR_AZURE_OPENAI_API_KEY>'
        ```
    3.  For Google as the provider, type:
        ```shell
            kubectl create secret generic kai-api-keys -n konveyor-ai \
             --from-literal=GEMINI_API_KEY='<YOUR_GOOGLE_API_KEY>'
        ```
    4.  For the OpenAI-compatible providers, type:
        ```shell
            kubectl create secret generic kai-api-keys -n konveyor-ai \
             --from-literal=OPENAI_API_BASE='https://example.openai.com/v1' \
             --from-literal=OPENAI_API_KEY='<YOUR_OPENAI_KEY>'
        ```
        You can also set the base URL as the `kai_llm_baseurl` variable
        in the Tackle custom resource.

2.  (Optional) Force a reconcile so that the Konveyor operator picks up
    the secret immediately
     ```shell
        kubectl patch tackle tackle -n konveyor-ai --type=merge -p \
        '{"metadata":{"annotations":{"konveyor.io/force-reconcile":"'"$(date +%s)"'"}}}'
     ```