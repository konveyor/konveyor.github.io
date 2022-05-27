---
title: "Installing Crane"
date: 2022-04-14T15:17:07-06:00
draft: false
---
Follow the procedure below to install the Crane tool.

### 1. Install the Crane binary.

Enter the following command to install the latest version of Crane binary.
```
curl -sL https://api.github.com/repos/konveyor/crane/releases/latest |
jq -r ".assets[] | select(.name | contains(\"<arch>-<os>\")) | .browser_download_url" | wget -i-
chmod +x <binary>
cp <binary> /usr/bin/crane
```
Crane currently supports three architectures:
- amd64-linux
- amd64-darwin
- arm64-darwin

Run the following command to download the latest version of Crane for amd64-linux.
```
curl -sL https://api.github.com/repos/konveyor/crane/releases/latest |
jq -r ".assets[] | select(.name | contains(\"amd64-linux\")) |
.browser_download_url" | wget -i-
```
Run the following command to download the latest version of Crane for  amd64-darwin.
```
curl -sL https://api.github.com/repos/konveyor/crane/releases/latest |
jq -r ".assets[] | select(.name | contains(\"amd64-darwin\")) |
.browser_download_url" | wget -i-
```
Run the following command to download the latest version of Crane for  arm64-darwin.
```
curl -sL https://api.github.com/repos/konveyor/crane/releases/latest |
jq -r ".assets[] | select(.name | contains(\"arm64-darwin\")) |
.browser_download_url" | wget -i-
```
### 2. Install the most recent version of Crane from the upstream main branch.
`GOPATH`should be configured to build the project.
```
cd $GOPATH
git clone https://github.com/konveyor/crane.git
cd crane
go build -o crane main.go
cp crane /usr/bin/crane
```
**Note:** Install the released version instead of building from upstream main.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Crane/Installation.md)
