---
title: "Installing Crane"
date: 2022-04-14T15:17:07-06:00
draft: false
---
## Installing Crane
Install latest released crane binary
```
curl -sL https://api.github.com/repos/konveyor/crane/releases/latest |
jq -r ".assets[] | select(.name | contains(\"<arch>-<os>\")) | .browser_download_url" | wget -i-
chmod +x <binary>
cp <binary> /usr/bin/crane
````
Currently only three architectures are supported which are

amd64-linux
amd64-darwin
arm64-darwin
For Example:

To download latest version of crane for amd64-linux, run the following command
```
curl -sL https://api.github.com/repos/konveyor/crane/releases/latest |
jq -r ".assets[] | select(.name | contains(\"amd64-linux\")) |
.browser_download_url" | wget -i-
```
To download latest version of crane for amd64-darwin, run the following command
```
curl -sL https://api.github.com/repos/konveyor/crane/releases/latest |
jq -r ".assets[] | select(.name | contains(\"amd64-darwin\")) |
.browser_download_url" | wget -i-
```
To download latest version of crane for arm64-darwin, run the following command
```
curl -sL https://api.github.com/repos/konveyor/crane/releases/latest |
jq -r ".assets[] | select(.name | contains(\"arm64-darwin\")) |
.browser_download_url" | wget -i-
```
### Install most recent crane from upstream main branch
```GOPATH```should be configured to build the project.
```
cd $GOPATH
git clone https://github.com/konveyor/crane.git
cd crane
go build -o crane main.go
cp crane /usr/bin/crane
```
Note: It is recommended to install the released version instead of building from upstream main
