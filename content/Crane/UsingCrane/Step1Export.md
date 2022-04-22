---
title: "Step One: Export"
date: 2022-04-22T10:29:18-06:00
draft: true
---

The first step of the cluster migration process is exporting resources from a source cluster of any namespace to be input for the subsequent commands.

All of the following `export` commands will output the contents of the foo namespace into a local export directory with the context demo defined in KUBECONFIG.
```
crane export -n foo -e export --context demo
```
```
cat << EOF >> conf.yaml
namespace: foo
export-dir: export
context: demo
EOF

crane export -c conf.yaml
```
```
cat << EOF >> conf.yaml
namespace: foo
export-dir: export
context: testing
EOF

crane export -c conf.yaml --context demo

# Note the difference is we are overriding "context" here with flag
```
**Note:** There are multiple ways to input a command, precedence of which is `input from flags > input from config file > env variables > default values` (not all the flags can have a corresponding env variable). This behavior persists across all Crane CLI commands.
