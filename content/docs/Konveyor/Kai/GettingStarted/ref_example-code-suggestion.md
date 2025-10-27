---
title: "Generating code fix suggestion example"
date: 2025-12-14T14:58:52-06:00
draft: false
---
This example will walk you through generating code fixes for a `Java` application that must be migrated to the target technology `quarkus`. To generate resolutions for issues in the code, we use the Agentic AI mode and the `my-model` as the large language model (LLM) that you deployed in OpenShift AI.

## Procedure

1.  Open the `my-Java` project in Visual Studio (VS) Code.

2.  Download the Konveyor AI extension from the [Konveyor AI release page in GitHub](https://github.com/konveyor/editor-extensions/releases/tag/v0.2.0).

3.  Open Command Palette:

    1.  Type `Ctrl+Shift+P` in Windows and Linux systems.

    2.  Type `Cmd+Shift+P` in Mac systems.

4.  Type `Preferences: Open Settings (UI)` in the Command Palette to
    open the VS Code settings and select `Extensions > Konveyor AI`.

5.  Select `Gen AI:Agent Mode`.

6.  In the Konveyor AI extension, click `Open Analysis View`.

7.  Type `Konveyor: Manage Analysis Profile` in the Command Palette to open
    the analysis profile page.

8.  Configure the following fields:

    1.  **Profile Name**: Type a profile name

    2.  **Target Technologies**: `quarkus`

    3.  **Custom Rules**: Select custom rules if you want to include
        them while running the analysis. By default, Konveyor AI enables
        **Use Default Rules** for `quarkus`.

9.  Close the profile manager.

10. Type `Konveyor: Open the Gen AI model provider configuration file` in the
    Command Palette.

11. Configure the following in the `provider-settings` file and close
    it:
    ```yaml
        models:
          openshift-example-model: &active
            environment:
              OPENAI_API_KEY: "<Server's OPENAI_API_KEY>"
              CA_BUNDLE: "<Servers CA Bundle path>"
            provider: "ChatOpenAI"
            args:
              model: "my-model"
              configuration:
                baseURL: "https://<serving-name>-<data-science-project-name>.apps.konveyor-ai.example.com/v1"
    ```
    You must change the `provider-setting` configuration if you plan to
    use a different LLM provider.

12. Type `Konveyor AI: Open Analysis View` in the Command Palette.

13. Click **Start** to start the Konveyor AI RPC server.

14. Select the profile you configured.

15. Click **Run Analysis** to scan the Java application.

    Konveyor AI identifies the issues in the code.

16. Click the solutions icon in an issue
    to request suggestions to resolve the issue.

    Konveyor AI streams the issue description, a preview of the code
    changes that resolve the issue, and the file(s) in which the changes
    are to be made.

    You can review the code changes in the editor and accept or reject
    the changes. If you accept the changes, Konveyor AI creates a new
    file with the accepted code changes.

17. Click **Continue** to allow Konveyor AI to run a follow-up analysis.

    This round of analysis detects lint issues, compilation issues, or
    diagnostic issues that may have occurred when you accepted the
    suggested code change.

    Repeat the review and accept or reject the resolutions. Konveyor AI
    continues to run repeated iterations of scan if you allow until all
    issues are resolved.
