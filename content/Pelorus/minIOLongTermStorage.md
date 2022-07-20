---
title: "MinIO and Pelorus Quickstart"
date: 2022-07-14T15:14:55-06:00
draft: true
---

Deploy MinIO on OpenShift and then configuring Pelorus to consume it as a long term storage solution by:

* Configuring a namespace and storage security to allow MinIO to run.
* Deploying MinIO from Helm Chart to retain Pelorus dashboard data long-term.
    * Securing MinIO object storage.
    * Updating the Pelorus Configuration

1. Add a security constraint context in the root repository directory.
```
oc create namespace minio
oc apply -f storage/minio-scc.yaml
```
2. Deploy an instance of MinIO and create a bucket named thanos.
```
helm install --namespace minio --set "buckets[0].name=thanos" \
--set "buckets[0].policy=none" \
--set "buckets[0].purge=false" \
--set "configPathmc=/tmp/minio/mc" \
--set "DeploymentUpdate.type=\"Recreate\"" pelorus-minio stable/minio
```
> Note: Recreate mode is used. RollingDeployments will not allow re-deployment while a PVC is in use.
The configuration and certificate path changed to work with OpenShift.

3. Secure MinIO using a service serving certificate.
```
helm upgrade --namespace minio --set "certsPath=/tmp/minio/certs" \
--set "tls.enabled=true" \
--set "tls.certSecret=pelorus-minio-tls" \
--set "tls.privateKey=tls.key,tls.publicCrt=tls.crt" \
--set "service.annotations.service\.beta\.openshift\.io/serving-cert-secret-name=pelorus-minio-tls" \
--set "DeploymentUpdate.type=\"Recreate\"" pelorus-minio stable/minio
```
4. Update the Pelorus stack in the root repository directory.
```
./runhelm.sh -n pelorus \
-s "bucket_access_point=pelorus-minio.minio.svc:9000" \
-s "bucket_access_key=AKIAIOSFODNN7EXAMPLE" \
-s "bucket_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
```

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Pelorus/minIOLongTermStorage.md)
