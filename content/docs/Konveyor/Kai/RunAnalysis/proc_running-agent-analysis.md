---
title: "Generating code resolutions in the agent mode"
date: 2025-11-29T14:58:52-06:00
draft: false
---
In the agent mode, the Konveyor AI planning agent creates the context for an issue and picks a sub-agent that is most suited to resolve the issue. The sub-agent runs an automated scan to describe how the issue can be resolved and generates files with the updated resolutions in one stream.

You can review the updated files and approve or reject the changes to the code. The agent runs another automated analysis to detect new issues in the code that may have occurred because of the accepted changes or diagnostic issues that your tool may generate following a previous analysis. If you allow the process to continue, Konveyor runs the stream again and generates a new file with the latest updates.

When using the agent mode, you can reject the changes or discontinue the stream but you cannot edit the updated files during the stream.

## Prerequisites

- You opened a Java project in your VS Code workspace.

- You configured an analysis profile on the **Konveyor Analysis View** page.

## Procedure

1.  Verify that agent mode is enabled in one of the following ways:

    1.  Type `Ctrl + Shift + P` in VS Code search (Linux/Windows system) and `Cmd + Shift + P` for Mac to go to the command palette.

    2.  Enter `Preferences: Open User Settings (JSON)` to open the
        `settings.json` file.

    3.  Ensure that `konveyor.genai.agentMode` is set to `true`.

        OR

    4.  Go to **Extensions &gt; Konveyor &gt; settings**

    5.  Click the **Agent Mode** option to enable the server.

2.  Click the Konveyor extension and click **Open Konveyor Analysis View**.

3.  Select a profile for the analysis.

4.  Click **Start** to start the Konveyor RPC server.

5.  Click **Run Analysis** on the **Konveyor Analysis View** page. The **Resolution Details** tab opens, where you can view the automated analysis that makes changes in applicable files.

6.  Click the **Review Changes** option to open the editor that shows the diff view of the modified file.

7.  Review the changes and click **Apply** to update the file with all the changes or **Reject** to reject all changes. If you applied the changes, then Konveyor AI creates the updated file with code changes.

8.  Open **Source Control** to access the updated file.

9.  In the **Resolution Details** view, accept the proposal from
    Konveyor AI to continue with further analysis. The stream of analysis repeats, after which you can review and accept change. Konveyor AI creates the file with the code changes, and the stream continues until you reject the proposal for further analysis.
