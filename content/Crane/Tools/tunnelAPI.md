---
title: "Tunnel API"
date: 2022-04-14T15:21:57-06:00
draft: false
---
The `tunnel-api` sub-command can be used to access an on-premise cluster from a cloud cluster to allow orchestrating migrations from on-premise clusters using MTC where access is not possible otherwise.

An OpenVPN client on the on-premise cluster will connect to a server running on the cloud cluster and the OpenVPN server is exposed to the client using a load balancer address on the cloud cluster.

A service created on the cloud cluster is used to expose the on-premise clusters API to MTC running on the cloud cluster.

## Requirements
- The system used to create the VPN tunnel must have access and be logged in to both clusters.
- It must be possible to create a load balancer on the cloud cluster.
- An available namespace on each cluster to run the tunnel that was not created in advance.

**Note:** To connect multiple on-premise source clusters to the cloud cluster use a separate namespace for each.

## api-tunnel options
- **namespace:** The namespace used to launch the VPN tunnel defaults to OpenVPN.
- **destination-context:** The cloud destination cluster context where the OpenVPN server will be launched.
- **destination-image:** The container image to use on the destination cluster. (Default: quay.io/konveyor/openvpn:latest)
- **source-context:** The on-premise source cluster context where the OpenVPN client will be launched.
- **source-image:** The container image to use on the source cluster. (Default: quay.io/konveyor/openvpn:latest)
- **proxy-host:** The hostname of an http-proxy to use on the source cluster for connecting to the destination cluster.
- **proxy-pass:** The password for the http-proxy. If specified, also specify a username or it will be ignored.
- **proxy-port:** The port the http-proxy is listening on. If none is specified it will default to 3128
- **proxy-user:** The username for the http-proxy. If specified, a password must also be specified or it will be ignored.

**Example**
```
crane tunnel-api --namespace openvpn-311 \
      --destination-context openshift-migration/c131-e-us-east-containers-cloud-ibm-com/admin \
      --source-context default/192-168-122-171-nip-io:8443/admin \
      --source-image: my.registry.server:5000/konveyor/openvpn:latest \
      --proxy-host my.proxy.server \
      --proxy-port 3128 \
      --proxy-user foo \
      --proxy-pass bar
```
## MTC Configuration
When configuring the source cluster in MTC, the API URL is `https://proxied-cluster.${namespace}.svc.cluster.local:8443`.

**Optional:** Set the image registry for direct image migrations to `proxied-cluster.${namespace}.svc.cluster.local:5000`.

Replace ``${namespace}`` with either `openvpn` or the specified namespace when running the command to set up the tunnel.

## Demo
[https://youtu.be/wrPVcZ4bP1M](https://youtu.be/wrPVcZ4bP1M)

## Troubleshooting
It may take three to five minutes after setup completes for the load balancer address to become resolvable. During this time the client will be unable to connect and establish a connection and the tunnel will not function.

During this time, run `oc get pods` in the specified namespace for setup, and monitor the logs of the OpenVPN container to see the connection establish.

**Example**
```
oc logs -f -n openvpn-311 openvpn-7b66f65d48-79dbs -c openvpn
```

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Crane/Tools/TunnelAPI.md)
