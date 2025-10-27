---
title: "Running an analysis and resolving issues"
date: 2025-12-02T14:58:52-06:00
draft: false
---
After you complete the configurations, the next step is running an
analysis to identify the issues in the code and generate suggestions to resolve the issues. You can get suggestions to fix code by using Konveyor AI.

When you run an analysis, Konveyor displays the issues in the **Analysis Results** view.

When you request code fix suggestions, Konveyor AI performs the
following tasks:

- Streams LLM messages that describe the issue description, resolution, and the file in which the updates are applied.

- Generates new files in the **Resolutions** pane. These files have the updates to the code to resolve the issues detected in the current analysis. You can review the changes, apply, or revert the updates.

If you apply all the resolutions, Konveyor AI applies the changes and triggers another analysis to check if there are more issues. Subsequent analysis reports fewer issues and incidents.
