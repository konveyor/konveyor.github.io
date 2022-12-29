---
title: "Command line tool"
date: 2022-08-03T15:23:12-06:00
draft: false
---
## Installing on Linux / MacOS / Windows WSL (Recommended):

The easiest way to install Move2Kube is to download the pre-built binaries for Linux, MacOS, or Windows from the [list of releases on Github](https://github.com/konveyor/move2kube/releases). Follow the steps below to install the latest stable version.

**Procedure**

1. Install Move2Kube with one of the following options.

* Latest stable version:

```
bash <(curl https://raw.githubusercontent.com/konveyor/move2kube/main/scripts/install.sh)
```
* A specific version (for example version v0.3.0-beta.0):

```
MOVE2KUBE_TAG='v0.3.0-beta.0' bash <(curl https://raw.githubusercontent.com/konveyor/move2kube/main/scripts/install.sh)
```

* Bleeding edge version:

```
BLEEDING_EDGE='true' bash <(curl https://raw.githubusercontent.com/konveyor/move2kube/main/scripts/install.sh)
```

* Without sudo:

```
USE_SUDO=false bash <(curl https://raw.githubusercontent.com/konveyor/move2kube/main/scripts/install.sh)
````

2. Consider setting the following options.

* The script installs to /usr/local/bin by default. To install to a different directory:

```
MOVE2KUBE_INSTALL_DIR=/my/new/install/dir bash <(curl https://raw.githubusercontent.com/konveyor/move2kube/main/scripts/install.sh)
```
* Make the installation directory in the PATH to run Move2Kube as move2kube instead of /my/new/install/dir/move2kube.
* Combine the above two environment variables to install without sudo and install to a different directory.

```
USE_SUDO=false MOVE2KUBE_INSTALL_DIR=/my/new/install/dir bash <(curl https://raw.githubusercontent.com/konveyor/move2kube/main/scripts/install.sh)
```

### Alternate installations
Move2Kube can be installed using Homebrew and Go.

**Homebrew**

```
brew tap konveyor/move2kube
brew install move2kube
```

To install a specific version (for example version v0.3.0-beta.0):

```
brew install move2kube@0.3.0-beta.0
```

**Go**

Install using go get pulls from the main branch of [Move2Kube](https://github.com/konveyor/move2kube) with the latest development changes.

```
go get -u github.com/konveyor/move2kube
```
