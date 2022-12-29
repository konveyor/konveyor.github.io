---
title: "Migrating a Kubernetes cluster"
date: 2022-04-14T15:14:19-06:00
draft: false
---
This tutorial is an example of how to use the Konveyor tool Crane to migrate an application (inventory) from the source Kubernetes cluster (src) to the destination cluster (dest).

Refer to the Crane Documentation for more detailed information.

> **Note:** In addition to migrating with Crane, it is helpful to push the application to Git so it can be automatically deployed to any cluster in the future. This demo includes those steps.

1. View details of the destination and source clusters.
```
    > $ minikube profile list
```
2. View the applications on the source cluster.
```
    > $ kubectl --context src get namespace
```
3. View the inventory and postgres services.
```
    > $ kubectl --context src --namespace inventory get all
```
4. View the inventory storage capacity.
```
    > $ kubectl --context src --namespace inventory get pvc
```
5. View the front-end database content.
```
    > $ curl http://…
```
6. Set the context to the source.
```
    > $ kubectx src
```
7. List the application namespaces on the source.
```
    > $ kubectl get ns
```
**Tip:**  Use the `kubectx` and `kubectl` get `ns commands` to change the context to the destination and verify the migrating application does not exist.

8. Create an export folder.
```
    > $crane export --namespace=inventory
```
9. View what was extracted from the source cluster into YAML files.
```
    > $ tree export
```
> **Note:** These files cannot be imported into another cluster because of the existing IP addresses, timestamps, etc. which should not be pushed to Git. View this data using the `cat command`.

When the application is migrated, this information must be updated to the new cluster.

10. Clean the manifest files before pushing to Git.
```
    > $ crane transform list-plugins
```
> **Note:** This example uses the Kubernetes plugin. Use the Crane Plugin Manager `list command` to view available plugins to install and translate the manifest into OpenShift for example.

11. View available options that can be used to change the manifests before pushing to Git.
```
    > $ crane transform optionals
```
12. Create a transform folder using the default Kubernetes transform options.
```
    > $ crane transform
```
13. View the patches in the transform folder that will be applied to the original manifests to create new ones.
```
    > $ tree transform
```
14. Open the patches and verify the data that will be changed before the transform.
```
    > $ cat [directory path]
```
15. Clean up the manifest files using the patches and create an output folder.
```
    > $ crane apply
```
16. View the cleaned manifest files.
```
    > $ tree output
```
17. Copy the cleaned manifests to the Git folder for future migrations.
```
    > $ crane apply -o
```
18. Change the context to the destination.
```
    > $ kubectx dest
```
19. Create a new namespace.
```
    > $ kubectl --context dest create ns inventory \ namespace/inventory created
```
20. Verify the name of the pvc needed for migration.
```
    > $ kubectl --context src --namespace inventory get pvc
```
21. Begin the migration.
```
    > $ crane transfer-pvc --source-context=src \ --pvc-name=postgres-pv-claim --destination-context=dest
```
> **Tip:** Perform the migration while the application is running and then again during a maintenance window when the application has been shut down to only migrate over the delta between the migrations.

22. List the contents of the demo directory.
```
    > $ ls
```
23. View the migrated files.
```
    > $ tree output
```
24. View the Argo inventory file and verify the Git repo URL, namespace, and server.
```
    > $ cat inventory.argo.yaml
```
25. Copy the file into the Argo CD.
```
    > $ kubectl --context dest --namespace argocd apply -f inventory.argo.yaml
```
26. Open the Argo CD user interface.
27. Open the migrated application.
28. Verify Argo picked up and provisioned all the manifests from the Git repo.
29. Click the **Sync** button at the top of the screen where the application can be resynced from the Git repo.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Crane/Tutorials/MigrateK8Cluster.md)
