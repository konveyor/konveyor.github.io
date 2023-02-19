---
title: "Upgrading to 2.0"
date: 2022-06-29T10:53:45-06:00
draft: false
---
> For additional information, see the [Tackle CLI tool directory](https://github.com/konveyor/tackle2-hub/tree/main/hack/tool) in the tackle2-hub repository.

A CLI tool is available to upgrade Tackle that will migrate application data seeds from a running 1.2 instance to a running 2.0 instance, or export the data to be imported at a later time.  The tool has the following functions:

* **`export-tackle1`:** Exports Tackle 1.2 API objects into local JSON files (tags, tag types, and job functions).
* **`tackle --skip-destination-check export-tackle1`:** Exports Tackle 1.2 API objects into a local directory.
* **`import`:** Creates objects in Tackle 2 from local JSON files.
* **`clean`:** Deletes objects uploaded to Tackle 2 from local JSON files.
* **`clean-all`:** Deletes all data returned by Tackle 2 including seeds and additional data to clean, and skips some Pathfinder items without index action.

## Export/Import
To export data from a running 1.2 Tackle instance and import it into a running 2.0 instance, the following commands are used.
```
* tackle export-tackle1
* tackle import
```
The **`export-tackle1`** command identifies seed resources in the Tackle2 instance first, then downloads all resources from Tackle 1.2 API, transforms it to the format expected by the Tackle 2 Hub, and lastly re-maps resources to the seeds already existing in the destination Tackle2 Hub API. The result is serialized and stored into local JSON files.

The **`tackle import`** command connects to Tackle2 Hub, checks existing objects for possible collisions (by IDs) and uploads resources from local JSON files.

### Delayed Export
If a Tackle 2.0 instance is not available or one needs to be cleaned up, all 1.2 instance data is exported to a local directory, regardless of what already exists in Tackle 2.0. To export data for delayed import, the following commands are used.
```
* tackle --skip-destination-check export-tackle1
* tackle clean-all
* tackle import
```

See the CLI options section below for more tool functions.

##
* Git
* Python 3 with a YAML parser
    * RHEL8/Python 3.6: Parser included
    * RHEL9/Python 3.9: PyYAML module needs to be installed

### Verifying and installing YAML Parser
Follow the steps below to install a YAML parser.

> **Note:** Use the **python3 -m pip install pyyaml** command to verify a parser is installed, or to install the parser on non-RHEL-like distributions that have the `dnf` command.

* Install parser for RHEL-like Linux systems.
```
dnf install python39 python3-pyyaml git
```

## Using the Upgrade tool
Follow these procedures to download and start the upgrade tool, then export the 1.2 Tackle instance objects and import them into the Tackle 2.0 instance.

### Getting started
Follow the steps below to clone the tool repository, set credentials, and run the tool.

Use the tackle-config.yml.example file as a template to set your Tackle endpoints and credentials and save it as tackle-config.yml.

**Procedure**

1. Clone GitHub repository.
```
git clone https://github.com/konveyor/tackle2-hub.git
```
2. Change to the tool directory.
```
cd hack/tool
```

3. Set API endpoints and credentials in a config file (tackle-config.yml by default).
```
# Main Tackle 2 endpoint and credentials
url: https://tackle-konveyor-tackle.apps.cluster.local
username: admin
password: Passw0rd!
# Tackle 1.2 endpoint and credentials, e.g. to dump data and migrate to Tackle2
tackle1:
  url: https://tackle-tackle.apps.mta01.cluster.local
  username: tackle
  password: password
```
4. Run the tackle tool.
```
./tackle
```

### Export Tackle 1.2
Run **`tackle export-tackle1`** to create a data dump of Tackle 1.2 objects into JSON files in a default local directory: ./tackle-data.

> **Note:** Unverified HTTPS warnings from Python could be hidden by **`export PYTHONWARNINGS="ignore:Unverified HTTPS request"`** or with the **`-w`** command option.

**Procedure**
1. Run the tackle export on the 1.2 instance.
```
tackle export-tackle1
```
2. Review the JSON files in the **`./tackle-data`** directory and modify if necessary.

### Import to Tackle 2 Hub
After checking the local JSON dump files in **`./tackle-data`** directory (if needed), use the command below to create objects in Tackle 2 Hub by running **`tackle import`**.

**Procedure**
* Run the tackle export on the 1.2 instance.
```
tackle import
```

### Import troubleshooting
The **`tackle import`** command could fail in a pre-import check phase, which ensures there are no resources of a given type with the same ID in the Tackle 2.0 destination (error after checking tagtypes in destination Tackle2, etc.). In this case, run the **`tackle clean`** command, to remove these objects from the destination API, or remove it manually either from the destination or from the JSON data files.

If the import failed in the upload phase (error after Uploading tagtypes, etc.), try running **`tackle clean`** to remove already imported objects followed by tackle clean-all which lists all resources of all known data types in the destination Tackle 2 API and deletes it without looking at local data files.

## Cleaning objects

> **Warning:** All clean actions may delete objects already present in the Tackle 2.0 that are unrelated to the import data.

### Clean objects
In the event existing objects in the Tackle 2.0 instance conflict with the Tackle 1.2 JSON dump files, the following command will delete objects previously created by the **`import`** command.

**Procedure**
* Run the tackle export on the 1.2 instance.
```
tackle clean
```

### Clean all objects
Follow the steps below to list objects from all data types and delete those resources to clean the Tackle 2.0 instance.

> **Note:** The **clean-all** command deletes all resources from the Tackle 2.0 Hub API, however, the Pathfinder API does not support listing assessments without providing an applicationID. The applications may not be present in Hub, so any "orphaned" assessments could stay in Pathfinder. In order to resolve potential collision with imported data, run **tackle clean** together with the **clean-all** command.

**Procedure**
1. Run the tackle export on the 1.2 instance.
```
tackle clean-all
```
2. Review the JSON files in the **`./tackle-data`** directory.


## Command line options
The upgrade tool has the following command line options.

* **`-c / --config`:** This path specifies a YAML file with configuration options, including endpoints and credentials for Tackle APIs (tackle-config.yml by default).

* **`-v / --verbose`:** Verbose output option logs all API requests and responses, providing additional information for possible debugging (off by default).

* **`-d / --data-dir`:** Data directory specifies a path to a local directory for Tackle database records in JSON format (./tackle-data by default).

* **`-s / --skip-destination-check`:** Creates a full export without having access to the Tackle 2.0 destination and executes all seed objects with this option. When importing this data, the destination needs to be empty (run clean-all first).

* **`-w / --disable-ssl-warnings`:** Suppress SSL warnings option for API requests.

* **`-i / --ignore-import-errors`:** Import errors can be skipped.

    **Warning:** This option is not recommended. Only use with high attention to avoid data inconsistency. If the import has failed, it is recommended to use the tackle clean command to only remove imported resources.

**Example:**
```
$ tackle --help
usage: tackle [-h] [-c [CONFIG]] [-d [DATA_DIR]] [-v] [-s] [action ...]

Konveyor Tackle maintenance tool.

positional arguments:
  action                One or more Tackle commands that should be executed, options: export-tackle1 import clean clean-all

options:
  -h, --help            show this help message and exit
  -c [CONFIG], --config [CONFIG]
                        A config file path (tackle-config.yml by default).
  -d [DATA_DIR], --data-dir [DATA_DIR]
                        Local Tackle data directory path (tackle-data by default).
  -v, --verbose         Print verbose output (including all API requests).
  -s, --skip-destination-check
                        Skip connection and data check of Tackle 2 destination.
  -w, --disable-ssl-warnings
                        Do not display warnings during ssl check for api requests.
  -i, --ignore-import-errors
                        Skip to next item if an item fails load.
```

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/upgradeTo2.md)