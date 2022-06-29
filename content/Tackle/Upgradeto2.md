---
title: "Upgrading from 1.2 to 2.0"
date: 2022-06-29T10:53:45-06:00
draft: true
---
A CLI tool is available to upgrade Tackle 1.2 to 2.0 written in Python.

## Prerequisites
* Git
* Python 3 with a YAML parser
    * RHEL8/Python 3.6: Parser included
    * RHEL9/Python 3.9: PyYAML module needs to be installed

### Verifying and installing YAML Parser
Follow the steps below to determine if a YAML parser is installed using the Python PIP tool instead of the operation system package manager, and then install it if necessary.

1. Verify a parser is installed.
```
python3 -m pip install pyyaml
```
2. If necessary, install parser for RHEL-like Linux or corresponding operating system:
```
dnf install python39 python3-pyyaml git
```
## Using the Upgrade tool

To use the CLI tool and find additional information, see the [Tackle CLI tool directory](https://github.com/konveyor/tackle2-hub/tree/main/hack/tool) in the tackle2-hub repository.
