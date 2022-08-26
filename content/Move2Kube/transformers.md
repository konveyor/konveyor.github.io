---
title: "Transformers"
date: 2022-08-05T10:42:32-06:00
draft: true
---

Move2Kube uses a suite of transformers to achieve object modifications. To customize the output artifacts generated for a specific input, these transformers can be configured or new custom transformers can be created to achieve the required result. Transformer behavior and configuration is determined by the Transformer Class it uses. Though all the transformer classes are equal internally in Move2Kube, from a usage perspective, they are classified into three categories.

1. [Purpose Built](/transformers/purpose-built) - Has a specific job and the customization allows for changing the parameters/configuration required for performing the specific job. Ex: `Kubernetes`, `Parameterizer`, `GolangDockerfileGenerator`, etc..
2. [External](/transformers/external) - Allows you to write custom transformers performing any behavior. It exposes the internal functions of the transformer class through different interfaces to be implemented by the transformer externally. Ex: `Starlark`, `Executable`
3. [Special](/transformers/special) - These classes allow special behaviors. Ex: `Router`

# Purpose Built
These transformer classes do a specific job, and the customization allows for changing the parameters/configuration required for performing the specific job.

In most cases, these classes have one internal implementation and a transformer configuration can be found [here](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers).

The general steps to use these transformer classes are:
1. Find the internal implementation [here](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers).
1. Copy the directory.
1. Change the configuration like the transformer name, templates, etc..
1. Set it to override the internal implementation.

The [custom transformer tutorial](https://move2kube.konveyor.io/tutorials/customizing-the-output/custom-dockerfile-change-built-in-behavior) that uses [node.js/Kubernetes transformers](https://github.com/konveyor/move2kube-transformers/tree/main/custom-dockerfile-change-built-in-behavior) and the [parameterization](https://move2kube.konveyor.io/tutorials/customizing-the-output/custom-parameterization-of-helm-charts-kustomize-octemplates) tutorial that uses the [parameterization transformer](https://github.com/konveyor/move2kube-transformers/tree/main/custom-helm-kustomize-octemplates-parameterization) are examples.

To understand the configuration provided by each of these transformers, the YAML in the built-in transformer should have a good overview. The other location to look for is the structure in the transformer implementation. In most classes which have a configuration, there will be a structure with a name ending in `YamlConfig`. For example, the Kubernetes transformer class has [KubernetesYamlConfig](https://github.com/konveyor/move2kube/blob/171f6d26c195ce1e1f8b0ec6e7b68d17401776f3/transformer/kubernetes/kubernetestransformer.go#L48). This is the configuation specified in the `spec.config` field.


## Parameterizer
[Source code](https://github.com/konveyor/move2kube/blob/39ef793031c2990a0c1f588d85c4237655a49653/transformer/kubernetes/parameterizer/types.go#L43-L91)

[Tutorial](/tutorials/customizing-the-output/custom-parameterization-of-helm-charts-kustomize-octemplates)

### Syntax for parameterizing specific fields
Move2Kube provides a way to parameterize any field in the Kubernetes YAML files, Helm charts, and Openshift Templates that Move2Kube generates. It can also parameterize fields in Kubernetes YAMLs found in the source directory.

In order to parameterize a specific field, Move2Kube needs to use a custom transformer which means a directory with some YAML files inside it needs to be created. Below is an [example parameterizer](https://github.com/konveyor/move2kube-transformers/blob/main/custom-helm-kustomize-octemplates-parameterization) in detail:

```console
$ ls
README.md deployment-parameterizers.yaml parameterizers.yaml
```

Look at the [deployment-parameterizers.yaml](https://github.com/konveyor/move2kube-transformers/blob/main/custom-helm-kustomize-octemplates-parameterization/deployment-parameterizers.yaml) to understand the syntax.

```yaml
apiVersion: move2kube.konveyor.io/v1alpha1
kind: Parameterizer
metadata:
  name: deployment-parameterizers
spec:
  parameterizers:
    - target: "spec.replicas"
      template: "${common.replicas}"
      default: 10
      filters:
        - kind: Deployment
          apiVersion: ".*/v1.*"
Now the `values.yaml` looks like this
    - target: 'spec.template.spec.containers.[containerName:name].image'
      template: '${imageregistry.url}/${imageregistry.namespace}/${services.$(metadataName).containers.$(containerName).image.name}:${services.$(metadataName).containers.$(containerName).image.tag}'
      default: quay.io/konveyor/myimage:latest
      filters:
        - kind: Deployment
          apiVersion: ".*/v1.*"
      parameters:
        - name: services.$(metadataName).containers.$(containerName).image.name
          values:
          - envs: [dev, staging, prod]
            metadataName: frontend
            value: frontend
          - envs: [prod]
            metadataName: orders
            value: orders
            custom:
              containerName: orders
```

Notice the parameterizer YAML follows the same conventions as Kubernetes YAMLs.

- `apiVersion` : `string` - The version of the Move2Kube API being used which is currently set at `move2kube.konveyor.io/v1alpha1`.
- `kind` : `string` - Tells Move2Kube the YAML file type which in this case it is set at `Parameterizer`.
- `metadata` : `object`
    - `name` : `string` - Name of the parameterizer.
- `spec` : `object`
    - `parameterizers` : `array[object]` - List of parameterizer objects.
        - `target` : `string` - Sets the field to parameterize. The syntax is same as `yq` dot notation which is explained in more detail in a later section.
        - `template` : `string` - Specifies how the field should get its value. For example: `"${common.replicas}"` means the generated Helm chart which contains the `values.yaml` there is the `common` field, and inside it `replicas` as shown below.
            ```console
            $ cat values.yaml
            ```
            ```yaml
            common:
                replicas: 10
            ```
            The value of this field will be the same as the value in the original YAML, but it can be overriden using the `default` parameter.

        - `default` : `any` can be used to override the default value for the field being parameterized. For example: if the original value of `spec.replicas` was `2`, then the `values.yaml` would look like this:
            ```console
            $ cat values.yaml
            ```
            ```yaml
            common:
                replicas: 2
            ```
            To set a different value like `10`, specify `default: 10` in the parameterizer YAML and now the `values.yaml` looks like this:
            ```console
            $ cat values.yaml
            ```
            ```yaml
            common:
                replicas: 10
            ```
        - `filters` : `array[object]` - Used to filter the Kubernetes YAML files that being targeted.
            - `kind` : `string` - Only parameterizes Kubernetes YAMLs that match this `kind` field. Specify a regex to match multiple kinds.
              `apiVersion` : `string` - Only parameterizers Kubernetes YAMLs that match this `apiVersion` field. A regex can be specified.
              `name` : `string` - Only parameterizes Kubernetes YAMLs that have the same `metadata.name` field. A regex can be specified.
              `envs` : `array[string]` - Only apply this parameterization when targeting one of the environments listed here.
        - `parameters` : `array[object]` - This can be used to specify defaults for each parameter inside the `template`.
            - `name` : `string` - Name of a parameter inside the `template`.
            - `default` : `string` - Default value for this parameter.

### Template parameter
When parameterizing a specific a field in a Kubernetes YAML, it can be templatized in different ways. If the `template` parameter is not specified, it will default to the `target` parameter.

#### Example 1
For example: `target: "spec.replicas"` would cause the `spec.replicas` to be parameterized (probably in Deployment YAMLs).
Move2Kube would parameterize it under the key `<kind>.<apiVersion>.<metadata.name>.spec.replicas` and the `values.yaml` would look like this:
```yaml
Deployment:
    v1:
        myDeploymentName:
            spec:
                replicas: 2
```
In this example `myDeploymentName` is the `metadata.name` field in the Deployment YAML and `2` is the original value for `spec.replicas` from the YAML file.

By specifying `template: "${common.replicas}"` the default key is overridden and now Move2Kube puts the following in the `values.yaml`
```yaml
common:
    replicas: 2
```

By specifying `default: 10` the default value is overridden and now Move2Kube puts the following in the `values.yaml`
```yaml
common:
    replicas: 10
```

#### Example 2
This example is a more complicated scenario of parameterizing the image name of some container in a Deployment YAML. First, because the `containers` field in the Deployment YAML is a list, use the syntax `[<index>]` to parameterize a single element in the list.

Use `target: "spec.template.spec.containers.[0].image"` to parameterize the first container in the Deployment.
The `values.yaml` looks like this:
```yaml
Deployment:
    v1:
        myDeploymentName:
            spec:
                template:
                    spec:
                        containers:
                            - image: 'my-repo.com/my-namespace/my-image-name:my-image-tag'
```

This may not be enough and may want to parameterize the container image registry URL, registry namespace, image name, and image tag separately.

To do this use:
```yaml
template: '${imageregistry.url}/${imageregistry.namespace}/${containers.[0].image.name}:${containers.[0].image.tag}'
```
This will cause the `values.yaml` to look like this:
```yaml
imageregistry:
    url: 'my-repo.com'
    namespace: 'my-namespace'
containers:
    - image:
        name: 'my-image-name'
        tag: 'my-image-tag'
```
The Helm template will look like this:
```yaml
{% raw %}
spec:
    template:
        spec:
            containers:
                - image: '{{ index .Values "imageregistry" "url" }}/{{ index .Values "imageregistry" "namespace" }}/{{ index .Values "containers" "[0]" "image" "name" }}:{{ index .Values "containers" "[0]" "image" "tag" }}'
{% endraw %}
```

#### Example 3
This is an even more complicated scenario continuing from example 2 that adds a dynamic key in our `values.yaml`.

To do this use the `[]` square brackets and `$` dollar sign syntax:
```yaml
target: 'spec.template.spec.containers.[containerName:name].image'
template: '${imageregistry.url}/${imageregistry.namespace}/${containers.$(containerName).image.name}:${containers.$(containerName).image.tag}'
```
Here `[containerName:name]` in `target` tells Move2Kube to extract the `name` field from the `container` object in the Deployment YAML and make it available as `containerName`.
The `$(containerName)` in `template` gets replaced by the `name` that was extracted.

The `values.yaml` looks like this:
```yaml
imageregistry:
    url: 'my-repo.com'
    namespace: 'my-namespace'
containers:
    myContainerName1:
        image:
            name: 'my-image-name'
            tag: 'my-image-tag'
    myContainerName2:
        image:
            name: 'my-image-name-2'
            tag: 'my-image-tag-2'
```

This provides a very powerful way to parameterize the image name of containers and simultaneously have a common registry URL and namespace for all the images while also parameterizing the image name and tag for each container separately.

---
# External
These transformer classes allow you to write custom transformers performing any behavior. It exposes the internal functions of the transformer class through different interfaces to be implemented by the transformer externally.

## Executable
An `Executable` class based on a transformer can be configured to run commands locally or as containers. These transformers essentially implement the `DirectoryDetect` and `Transform` functions of the transformer as executable commands. This allows using any language to write the function and makes them very powerful.

## Starlark
A `Starlark` class based transformer allows for writing a full fledged transformer in [Starlark](https://docs.bazel.build/versions/2.1.0/skylark/language.html) by implementing the `directory_detect` and `transform` functions.

See examples of using this transform class [here](https://github.com/konveyor/move2kube-transformers/tree/main/add-custom-kubernetes-annotation) and [here](https://github.com/konveyor/move2kube-transformers/tree/main/add-custom-files-directories-in-custom-locations).

# Special
These classes allow special behaviors.

## Router
The Router transformer direct an artifact to one of the eligible transformers, like choosing the server for a WAR file.

[WarRouter](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers/dockerfilegenerator/java/warrouter) and [EarRouter](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers/dockerfilegenerator/java/earrouter) are examples of using this transformer class.
