---
title: "Enabling konveyor AI in Tackle custom resource"
date: 2025-12-10T14:58:52-06:00
draft: false
---
Solution Server integrates with the Konveyor Hub backend component to use the database and volumes necessary to store and retrieve the solved examples.

To enable Solution Server and other AI configurations in the Konveyor AI VS Code extension, you must modify the Tackle custom resource (CR) with additional parameters.

## Prerequisites

*  You deployed an additional RWO volume for the `kai-database` if you
    want to use Konveyor AI.

* You installed the Konveyor operator v8.0.0.

## Procedure

1.  Log in to your Kubernetes cluster and switch to the `konveyor-tackle` namespace.

2.  Edit the Tackle CR settings in the `tackle_hub.yml` file with the following command:

        kubectl edit tackle

3.  Enter applicable values for `kai_llm_provider` and `kai_llm_model` variables.

  ```yaml
        kind: Tackle
        apiVersion: tackle.konveyor.io/v1alpha1
        metadata:
          name: konveyor ai
          namespace: konveyor-tackle
        spec:
          kai_solution_server_enabled: true
          kai_llm_provider: <provider-name> #For example, OpenAI.
          # optional, pick a suitable model for your provider
          kai_llm_model: <model-name>
  ```

    For OpenAI models and LLMs deployed in the Red Hat OpenShift AI cluster, enter `OpenAI` as the `kai_llm_provider` value.

4.  Apply the Tackle CR for the `konveyor-ai` project using the
    following command.
    
```shell
    kubectl apply -f tackle_hub.yaml
```
## Verification

1.  Enter the following command to verify the Konveyor AI resources deployed for Solution Server.
```shell
    kubectl get deploy,svc -n konveyor-ai | grep -E 'kai-(api|db|importer)'
```
    **NOTE:** When you enable Solution Server, the Solution Server API endpoint is served through the Konveyor Hub. 
You need not complete any further task, such as creating a route for the Solution Server API.
