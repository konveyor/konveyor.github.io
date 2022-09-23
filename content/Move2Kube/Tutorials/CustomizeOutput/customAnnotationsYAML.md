---
title: "Add custom annotations to Kubernetes YAMLs"
date: 2022-08-04T19:22:02-06:00
draft: true
---

Move2Kube generates Kubernetes YAMLs based on the needs of the application, but there might be situations where you might require specific fields to be different in the output. In this example, we illustrate how we can add an annotation to the Ingress YAML specifying an ingress class.

1. Create an empty workspace directory mamed `workspace` and make it the current working directory. Assume all commands are executed within this directory.
```console
$ mkdir workspace && cd workspace
```

2. Use the [enterprise-app](https://github.com/konveyor/move2kube-demos/tree/main/samples/enterprise-app) as the input for this flow.
```console
$ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/enterprise-app/src -r move2kube-demos
$ ls src
README.md		config-utils		customers	docs			frontend		gateway			orders
```

3. Run Move2Kube **without** any customization and the output ingress does not have any annotation. Once done, delete the `myproject` directory.
```console
$ move2kube transform -s src/ --qa-skip && cat myproject/deploy/yamls/myproject-ingress.yaml && rm -rf myproject
```
```yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    creationTimestamp: null
    labels:
      move2kube.konveyor.io/service: myproject
    name: myproject
```

4. Get the Starlark based custom transformer [here](https://github.com/konveyor/move2kube-transformers/tree/main/add-custom-kubernetes-annotation) and copy it into the `customizations` sub-directory.
```console
$ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d add-custom-kubernetes-annotation -r move2kube-transformers -o customizations
```

5. Transform using this customization and specify the customization using the `-c` flag.
```console
$ move2kube transform -s src/ -c customizations/ --qa-skip
```

Once the output is generated, we can observe from the snippet of the ingress file (`myproject/deploy/yamls/myproject-ingress.yaml`) that there is an annotation for the ingress class added (`kubernetes.io/ingress.class: haproxy`):
```console
$ cat myproject/deploy/yamls/myproject-ingress.yaml
```
```yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    annotations:
      kubernetes.io/ingress.class: haproxy
    creationTimestamp: null
    labels:
      move2kube.konveyor.io/service: myproject
    name: myproject
```

## Anatomy of ingress annotator transformer

This custom transformer uses a configuration YAML (`ingress-annotator.yaml`) and a Starlark script (`ingress-annotator.star`) to add an annotation to the ingress YAML. The contents of custom transformer are as shown below:
```console
$ ls customizations/add-custom-kubernetes-annotation
ingress-annotator.star  ingress-annotator.yaml
```
The configuration YAML specifies that the custom transformer consumes and produces a Kubernetes YAML artifact type as shown in the `consumes` and `produces` section.
```console
$ cat customizations/add-custom-kubernetes-annotation/ingress-annotator.yaml
```
```yaml
  apiVersion: move2kube.konveyor.io/v1alpha1
  kind: Transformer
  metadata:
    name: IngressAnnotator
    labels:
      move2kube.konveyor.io/built-in: false
  spec:
    class: "Starlark"
    consumes:
      KubernetesYamls:
        merge: false
        # Ensures a artifact of this type gets immediately intercepted by this transformer
        mode: "MandatoryPassThrough"
    produces:
      KubernetesYamls:
        disabled: false
    config:
      starFile: "ingress-annotator.star"
```

The code of the Starlark script is shown below. At a high-level, the code requires only the `transform()` function as it acts upon any Kubernetes YAML generated within Move2Kube. The `transform()` function loops through every YAML generated for every detected service, checks whether it is an ingress YAML, and if so adds the annotation. The path mappings are meant to persist these changes.
```console
$ cat customizations/add-custom-kubernetes-annotation/ingress-annotator.star
```
```python
  {% raw %}def transform(new_artifacts, old_artifacts):
      pathMappings = []
      artifacts = []

      for a in new_artifacts:
          yamlsPath = a["paths"]["KubernetesYamls"][0]
          serviceName = a["name"]
          artifacts.append(a)

          fileList = fs.readdir(yamlsPath)
          yamlsBasePath = yamlsPath.split("/")[-1]
          # Create a custom path template for the service, whose values gets filled and can be used in other pathmappings
          pathTemplateName = serviceName.replace("-", "") + yamlsBasePath
          tplPathData = {'PathTemplateName': pathTemplateName}
          pathMappings.append({'type': 'PathTemplate', \
                              'sourcePath': "{{ OutputRel \"" + yamlsPath + "\" }}", \
                              'templateConfig': tplPathData})
          for f in fileList:
              filePath = fs.pathjoin(yamlsPath, f)
              s = fs.read(filePath)
              yamlData = yaml.loads(s)
              if yamlData['kind'] != 'Ingress':
                  continue
              if 'annotations' not in yamlData['metadata']:
                  yamlData['metadata']['annotations'] = {'kubernetes.io/ingress.class': 'haproxy'}
              else:
                  yamlData['metadata']['annotations']['kubernetes.io/ingress.class'] = 'haproxy'
              s = yaml.dumps(yamlData)
              fs.write(filePath, s)
              pathMappings.append({'type': 'Default', \
                      'sourcePath': yamlsPath, \
                      'destinationPath': "{{ ." + pathTemplateName + " }}"})

      return {'pathMappings': pathMappings, 'artifacts': artifacts}{% endraw %}
```

This tutorial can be replicated in the UI by uploading the zip file of the custom transformer as a `customization`. You can get the zip of the source and customization by adding a `-z` to the end of the commands used in steps 2 and 4.

The next step is parameterizing custom fields in Helm Chart, Kustomize, and OC Templates.
