---
title: "Managing Plugins"
date: 2022-04-14T15:19:04-06:00
draft: false
---
## Plugin management
A helper to manage plugins that gets consumed by `transform` command. This is an optional step rather a utility to get any needed plugins and add them to appropriate location to be consumed by `transform` command.

### List plugin utility
List plugins allows user to discover all the available plugins, meaning that all the plugins that are compatible with current os/arch.

#### Example
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
```
crane plugin-manager --installed -p plugin-dir
```
The above command lists all the installed plugins managed by plugin-manager.
```
crane plugin-manager --params -n foo
```
The above command lists all the version of plugin foo with detailed information.

### Add plugin utility
Add any plugin to a plugin directory to be consumed by transform command.

#### Example
```
crane plugin-manager add foo --version 0.0.1 -p plugin-dir
```
The above command downloads the binary of plugin `foo` versioned `0.0.1` from appropriate source and places it in plugin directory `plugin-dir/managed` (default is `plugins`).

### Remove plugin utility
Remove any unwanted plugins that are not needed anymore to exempt them being consumed by transform command.

#### Example
```
crane plugin-manager remove foo -p plugin-dir
```
The above command will remove the plugin `foo` from the path `plugin-dir/managed`.

**Note:** The `plugin-manager` command always operates in the path `<plugin-dir>/managed`, meaning whenever the flag `-p, plugin-dir` is used with `plugin-manager` the utility always operates within a folder `managed` places within ``<plugin-dir>``. I.e `plugin-manager add` places the plugin binary within `<plugin-dir>/managed`, `plugin-manager` remove removes the binary from `<plugin-dir>/managed`, and `plugin-manager list --installed` looks at the path `<plugin-dir>/managed` to list installed plugins.

### Manual plugin management
Right now there are only two plugins available, there might be more plugins available soon. Currently available plugins are [Kubernetes](https://github.com/konveyor/crane-lib/tree/main/transform/kubernetes) (build into crane-lib) and [OpenShift](https://github.com/konveyor/crane-plugin-openshift). With the exception of the kubernetes plugin, which doesn’t need to be installed as it’s built in, these plugins can be added to desired plugin directory (default is `plugin` directory where `crane` is installed) by following methods -

Download the binary of the plugin from release and place it in `plugin` directory
```
curl -sL https://api.github.com/repos/konveyor/crane-plugin-<plugin-name>/releases/latest |
jq -r ".assets[] | select(.name | contains(\"<arch>-<os>\")) | .browser_download_url" | wget -i-
chmod +x <binary>
cp <binary> /bin/usr/crane/plugins/<plugin-name>
```
Build the binary locally and place it in `plugin` directory
```
cd $GOPATH
git clone https://github.com/konveyor/crane-plugin-<plugin-name>.git
cd crane-plugin-<plugin-name>
go build -f <plugin-name> .
cp <plugin> /bin/usr/crane/plugins/<plugin-name>
```
**Note:** Adding plugins manually that are available in the plugin repo is not advisable as long as it can be added with the help of `plugin-manager`. For custom plugins or testing plugins under development, however, manual management is necessary.

**Note:** Kubernetes plugin is always baked in crane-lib and is not supposed to be added manually or otherwise.
