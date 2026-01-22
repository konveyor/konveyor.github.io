+++
title = "Introduction to Konveyor AI"
date = 2023-05-22T14:58:52-06:00
weight = 1
chapter = false
type = "docs"
+++

Konveyor integrates with large language models (LLM) through the Konveyor AI (Kai) component in the Visual Studio (VS) Code extension. You can use Kai to apply LLM-driven code changes to resolve issues found through static code analysis of Java applications.

## Use case for AI-driven code fixes

Kai performs the static code analysis for a specified target technology to which you want to migrate your applications. Konveyor provides 2400+ analysis rules for various Java technologies and you can extend the ruleset for custom frameworks or new technologies by creating custom rules.

The static code analysis describes the issues in your code that must be resolved. As you perform analysis for a large portfolio of applications, the issue description and the rule definition that may contain
additional information form a large corpus of data that contains repetitive patterns of problem definitions and solutions.

Migrators do duplicate work by resolving issues that are repeated across applications in different migration waves.

## How does Kai work

Kai works by collecting and storing the changes in the code for a large collection of applications, finding context to generate prompts for the LLM of your choice, and by generating code resolutions produced by the LLM to address specific issues.

Kai uses Retrieval Augmented Generation for context-based resolutions of issues in code. By using RAG, Kai improves the context shared with the LLM to generate more accurate suggestions to fix the issue in the code. The context allows the LLM to "reason" and generate suggestions for issues detected in the code. This mechanism helps to overcome the limited context size in LLMs that prevents them from analyzing the entire source code of an application.

The context is a combination of the source code, the issue description, and solved examples:

- Description of issues detected by Konveyor when you run a static code analysis for a given set of target technologies.

- (Optional) The default and custom rules may contain additional information that you include which can help Kai to define the context.

- Solved examples constitute code changes from other migrations and a pattern of resolution for an issue that can be used in the future. A solved example is created when a Migrator accepts a resolution in a previous analysis that results in updated code or an unfamiliar issue in a legacy application that the Migrator manually fixed. Solved examples are stored in the Solution Server.

More instances of solved examples for an issue enhances the context and improve the success metrics of rules that trigger the issue. A higher success metrics of an issue refers to the higher confidence level associated with the accepted resolutions for that issue in previous analyses.

- (Optional) If you enable the Solution Server, it extracts a pattern of resolution, called the migration hint, that can be used by the LLM to generate a more accurate fix suggestion in a future analysis.

The improvement in the quality of migration hints results in more accurate code resolutions. Accurate code resolutions from the LLM result in the user accepting an update to the code. The updated code is stored in the Solution Server to generate a better migration hint in the future.

This cyclical improvement of resolution pattern from the Solution Server and improved migration hints lead to more reliable code changes as you migrate applications in different migration waves.

## Requesting code fixes in Kai

You can request AI-assisted code resolutions that obtain additional context from several potential sources, such as analysis issues, IDE diagnostic information, and past migration data via the Solution Server.

The Solution Server acts as an institutional memory that stores changes to source codes after analyzing applications in your organization. This helps you to leverage the recurring patterns of solutions for issues that are repeated in many applications.

It also enables you to control the analysis through manual reviews of the suggested AI resolutions: you can accept, reject or edit the suggested code changes while reducing the overall time and effort required to prepare your application for migration.

In the agentic AI mode, Kai streams an automated analysis of the code in a loop until all issues are resolved and changes the code with the updates. In the initial run, the AI agent:

- Plans the context to define the issues.

- Chooses a suitable sub agent for the analysis task. Works with the LLM to generate fix suggestions. The reasoning transcript and files to be changed are displayed to the user.

- Applies the changes to the code once the user approves the updates.

If you accept that the agentic AI must continue to make changes, it compiles the code and runs a partial analysis. In this iteration, the agentic AI attempts to fix diagnostic issues (if any) generated by tools that you installed in the VS Code IDE. You can review the changes and accept the agentic AI’s suggestion to address these diagnostic issues.

After each iteration of applying changes to the code, the agentic AI asks if you want the agent to continue fixing more issues. When you accept, it runs another iteration of automated analysis until it has resolved all issues or it has made a maximum of two attempts to fix an issue.

Agentic AI generates a new preview in each iteration when it updates the code with the suggested resolutions. The time taken by the agentic AI to complete all iterations depends on the number of new diagnostic issues that are detected in the code.

## Benefits of using Kai

- **Model agnostic** - Kai follows a "Bring Your Own Model" approach, allowing your organization to use a preferred LLM.

- **Iterative refinement** - Kai can include an agent that iterates through the source code to run a series of automated analyses that resolves both the code base and diagnostic issues.

- **Contextual code generation** - By leveraging AI for static code analysis, Kai breaks down complex problems into more manageable ones, providing the LLM with focused context to generate meaningful results. This helps overcome the limited context size of LLMs when dealing with large codebases.

- **No fine-tuning** - You also do not need to fine-tune your model with a suitable data set for analysis which leaves you free to use and switch LLM models to respond to your requirements.

- **Learning and Improvement** - As more parts of a codebase are migrated with Kai, it can use RAG to learn from the available data and provide better recommendations in subsequent application analysis.
