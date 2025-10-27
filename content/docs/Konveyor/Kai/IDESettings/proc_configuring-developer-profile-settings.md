---
title: "Configuring the Konveyor AI profile settings"
date: 2025-12-03T14:58:52-06:00
draft: false
---
You can use the Visual Studio (VS) Code plugin to run an analysis to discover issues in the code. You can optionally enable Konveyor AI to get AI-assisted code suggestions.

To generate code changes using the Konveyor extension, you must
configure a profile that contains all the necessary configurations, such as source and target technologies and the API key to connect to your chosen large language model (LLM).

## Prerequisites

- You completed the Solution Server configurations in Tackle custom
  resource if you opt to use the Solution Server.

- You opened a Java project in your VS Code workspace.

## Procedure

1.  Open the `Konveyor View Analysis` page in either of the following
    ways:

    1.  Click the book icon on the `Konveyor: Issues` pane of the
        Konveyor extension.

    2.  Type `Ctrl + Shift + P` or `Cmd + Shift + P` on the search bar
        to open the Command Palette and enter
        `Konveyor:Open Analysis View`.

2.  Click the settings button on the `Konveyor View Analysis` page to
    configure a profile for your project. The `Get Ready to Analyze`
    pane lists the following basic configurations required for an
    analysis:

| Profile settings         | Description  |
|--------------------------|--------------|
| Select profile           | Create a profile that you can reuse for<br>multiple analyses. The profile name is part of the context provided to<br>the LLM for analysis. |
| Configure label selector | A label selector filters rules for<br>analysis based on the source or target technology.<br>Specify one or more target or source technologies (for example,<br>cloud-readiness). The Konveyor extension uses this configuration to<br>determine the rules that are applied to a project during analysis.<br>If you mentioned a new target or a source technology in your custom<br>rule, you can type that name to create and add the new item to the<br>list.<br><br><br>&#10;<br>You must configure either target or source technologies before<br>running an analysis.<br> |
| Set rules                | Enable default rules and select your<br>custom rule that you want Konveyor to use for an analysis. You can use<br>the custom rules in addition to the default rules.  |
| Configure generative AI  | This option opens the<br>provider-settings.yaml file that contains API keys and<br>other parameters for all supported LLMs. By default, Konveyor AI is<br>configured to use OpenAI LLM. To change the model, update the anchor<br>&amp;active to the desired block. Modify this file with the<br>required arguments, such as the model and API key, to complete the<br>setup.  See [Configuring the LLM provider settings](../LLMSettings/ref_llm-provider-configurations.md). |


## Verification

After you complete the profile configuration, close the `Get Ready to Analyze` pane. You can verify that your configuration works by running an analysis.

