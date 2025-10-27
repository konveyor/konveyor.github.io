---
title: "Debugging Konveyor AI"
date: 2025-11-28T14:58:52-06:00
draft: false
---
Konveyor AI generates logs to debug issues specific to the extension host and the Konveyor analysis and RPC server. You can also configure the log level for the Konveyor AI in the extension settings. The default log level is **debug**.

Extension logs are stored as `extension.log` with automatic rotation. The maximum size of the log file is 10 MB and three files are retained. Analyzer RPC logs are stored as `analyzer.log` without rotation.

## Archiving the logs

To archive the logs as a zip file, type `Konveyor: Generate Debug Archive` in the VS Code Command Palette and select the information type that must be archived as a log file.

The archive command allows capturing all relevant log files in a zip archive at the specified location in your project. By default, you can access the archived logs in the .vscode directory of your project.

The archival feature helps you to save the following information:

- Large language model (LLM) provider configuration: Fields from the provider settings that can be included in the archive. All fields are redacted for security reasons by default. Ensure that you do not expose any secrets.

- LLM model arguments

- LLM traces: If you enabled tracing LLM interactions, you can choose to
  include LLM traces in the logs.

## Accessing the logs

You can access the logs in the following ways:

- **Log file**: Type `Developer: Open Extension Logs Folder` to open the `exthost` directory. Open the `output_logging` directory and use the `Konveyor Extension for VSCode` file to check the extension log and the analyzer log.

- **Output panel**: Select `Konveyor` from the drop-down menu.

- **Webview logs**: You can also inspect webview content by using the webview logs. To access the webview logs, type `Open Webview Developer Tools` in the VS Code Command Palette.
