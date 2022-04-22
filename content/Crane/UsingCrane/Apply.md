---
title: "Apply"
date: 2022-04-18T18:25:14-06:00
draft: true
---

## Applying patches

The final step of the process is to `apply` all the patches generated by the ```transform``` command to exported resources.

Example
```
    crane apply -e export -t transform -o output
```
Apply the patches in the ```transform``` directory to the resources in the ```export``` directory and save the modified resource files in the ```output``` directory.

After applying the patches, the resources located in output directory can either be deployed to the destination cluster using ```kubectl apply```, or they can be pushed to a repository and then can be applied with the help of the GitOps pipeline. An example of the later scenario can be found [here](https://crane-docs.konveyor.io/content/usage/Advance%20Usage/gitops.md).