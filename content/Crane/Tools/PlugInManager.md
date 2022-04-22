---
title: "Plugin Manager"
date: 2022-04-14T15:19:04-06:00
draft: false
---

The Plugin Manager is an optional utility that assists in adding plugins to the appropriate location to be consumed by the `transform` command.

### List Plugin utility
The List Plugin utility discovers available plugins that that are compatible with the current OS and architecture.
```
crane plugin-manager list
Listing from the repo default
+-----------------+------------------+-------------------+
|      NAME       | SHORTDESCRIPTION | AVAILABLEVERSIONS |
+-----------------+------------------+-------------------+
| OpenshiftPlugin | OpenshiftPlugin  | v0.0.1            |
+-----------------+------------------+-------------------+
```
#### Other valid execution examples
This command lists all installed plugins managed by plugin-manager.
```
crane plugin-manager --installed -p plugin-dir
```
This command lists all version of the foo plugin with detailed information.

```
crane plugin-manager --params -n foo
```
### Add Plugin utility
The Add Plugin utility places the plugin into a directory to be consumed by Transform command.

This command downloads the binary of the `foo` version `0.0.1` plugin  from the appropriate source and places it in the `plugin-dir/managed` directory (the default is `plugins`).

```
crane plugin-manager add foo --version 0.0.1 -p plugin-dir
```
### Remove Plugin utility

The Remove Plugin utility removes unwanted plugins from being consumed by the Transform command.
This command removes the foo plugin from the `plugin-dir/managed` dir.
```
crane plugin-manager remove foo -p plugin-dir
```
**Note:** The `plugin-manager` command operates in the `<plugin-dir>/managed` directory.
Whenever the flag `-p, plugin-dir` is used with `plugin-manager`, the utility operates in the `managed` places folder in ``<plugin-dir>``.

For example:  `plugin-manager add` places the plugin binary within `<plugin-dir>/managed`, `plugin-manager` removes the binary from `<plugin-dir>/managed`, and `plugin-manager list --installed` uses the path `<plugin-dir>/managed` to list installed plugins.

### Manual plugin management
Currently only two plugins are available with more plugins available soon.

Available plugins:
-[Kubernetes](https://github.com/konveyor/crane-lib/tree/main/transform/kubernetes) (build into crane-lib)
-[OpenShift](https://github.com/konveyor/crane-plugin-openshift).

These plugins can be added to the desired plugin directory. (The default directory is `plugin` where `crane` is installed.)

**Important:** The Kubernetes plugin is built into the crane-lib and is not to be added manually or otherwise.

To install the plugins:

Download the binary of the plugin from the release and place it in the `plugin` directory.
```
curl -sL https://api.github.com/repos/konveyor/crane-plugin-<plugin-name>/releases/latest |
jq -r ".assets[] | select(.name | contains(\"<arch>-<os>\")) | .browser_download_url" | wget -i-
chmod +x <binary>
cp <binary> /bin/usr/crane/plugins/<plugin-name>
```
Build the binary locally and place it in the`plugin` directory.
```
cd $GOPATH
git clone https://github.com/konveyor/crane-plugin-<plugin-name>.git
cd crane-plugin-<plugin-name>
go build -f <plugin-name> .
cp <plugin> /bin/usr/crane/plugins/<plugin-name>
```
**Note:** Adding plugins available in the plugin repo manually is not advisable as long as it can be added using`plugin-manager`. For custom plugins or testing plugins under development, manual management is necessary.
