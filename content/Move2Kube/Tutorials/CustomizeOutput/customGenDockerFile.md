---
title: "Customizing generated Dockerfile and behavior of built-in transformer"
date: 2022-08-04T19:21:31-06:00
draft: true
---

In this tutorial we will make Move2Kube add custom Dockerfile, and a custom file.

1. Create an empty workspace directory named `workspace` and make it the current working directory. Assume all commands are executed within this directory.
  ```console
  $ mkdir workspace && cd workspace
  ```

1. Use the [enterprise-app](https://github.com/konveyor/move2kube-demos/tree/main/samples/enterprise-app) as input for this flow.
  ```console
  $ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/enterprise-app/src -r move2kube-demos
  $ ls src
  README.md		config-utils		customers	docs			frontend		gateway			orders
  ```

1. Run Move2Kube **without** any customization.
  - If the `Dockerfile` is generated for the `frontend` app, it uses `registry.access.redhat.com/ubi8/nodejs-12` as the base image.
  - There are no scripts named `start-nodejs.sh` in the `frontend` service directory.
  - The Kubernetes YAMLs are generated in `myproject/deploy/yamls` directory.

    ```console
    $ move2kube transform -s src/ --qa-skip && ls myproject/source/frontend && cat myproject/source/frontend/Dockerfile && ls myproject/deploy && rm -rf myproject
    Dockerfile		README.md		dr-surge.js		manifest.yml		package-lock.json	server.js		stories			test-setup.js		webpack.common.js	webpack.prod.js LICENSE		__mocks__		jest.config.js		nodemon.json		package.json		src			stylePaths.js		tsconfig.json		webpack.dev.js

    FROM registry.access.redhat.com/ubi8/nodejs-12
    COPY . .
    RUN npm install
    RUN npm run build
    EXPOSE 8080
    CMD npm run start

    cicd			compose			knative			knative-parameterized	yamls			yamls-parameterized
    ```

For this tutorial we want to change:
  - The base image of the Dockerfile generated for Node.js from `registry.access.redhat.com/ubi8/nodejs-12` to `quay.io/konveyor/nodejs-12`.
  - Add a new script named `start-nodejs.sh` in the Node.js app directories along with the Dockerfile in the `frontend` directory.
  - Change the location of Kubernetes YAMLs from `myproject/deploy/yamls` to `myproject/yamls-elsewhere`.

1. Use a [custom configured version](https://github.com/konveyor/move2kube-transformers/tree/main/custom-dockerfile-change-built-in-behavior) of the [Node.js built-in transformer](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers/dockerfilegenerator/nodejs) and the [Kubernetes built-in transformer](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers/kubernetes/kubernetes) to achieve this. Copy [it](https://github.com/konveyor/move2kube-transformers/tree/main/custom-dockerfile-change-built-in-behavior) into the `customizations` sub-directory.
  ```console
  $ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d custom-dockerfile-change-built-in-behavior -r move2kube-transformers -o customizations
  ```

1. Transform using this customization and specify the customization using the `-c` flag.
  ```console
  $ move2kube transform -s src/ -c customizations/ --qa-skip
  ```

Once the output is generated, we can observe:
- The Dockerfile generated for the `frontend` app contains the custom base image.
- A new file named `start-nodejs.sh` was generated in the `frontend` directory.
- The Kubernetes YAMLs are now generated in `myproject/yamls-elsewhere` directory and the parameterized YAMLs are also in `myproject/yamls-elsewhere-parameterized` directory.
  ```console
  $ ls myproject/source/frontend
  Dockerfile		README.md		dr-surge.js		manifest.yml		package-lock.json	server.js		start-nodejs.sh		stylePaths.js		tsconfig.json		webpack.dev.js LICENSE		__mocks__		jest.config.js		nodemon.json		package.json		src			stories			test-setup.js		webpack.common.js	webpack.prod.js
  $ cat myproject/source/frontend/Dockerfile
  FROM quay.io/konveyor/nodejs-12
  COPY . .
  RUN npm install
  RUN npm run build
  EXPOSE 8080
  CMD sh start-nodejs.sh
  $ ls myproject
  Readme.md			deploy				scripts				source				yamls-elsewhere			yamls-elsewhere-parameterized
  ```

## Anatomy of transformers in `custom-dockerfile-change-built-in-behavior`

The two customized transformers in the directory are `nodejs` and `kubernetes`.
The contents of `custom-dockerfile-custom-files` are shown below:
  ```console
  $ tree customizations
  customizations
  └── custom-dockerfile-change-built-in-behavior
      ├── kubernetes
      │   └── kubernetes.yaml
      └── nodejs
          ├── nodejs.yaml
          └── templates
              ├── Dockerfile
              └── start-nodejs.sh
  ```
To custom configure a built-in transformer, copy the [built-in transformer's configuration directory](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers) from `move2kube` source, change the configurations, and use it as a customization. You can make it override the built-in transformer using the `override` config in the yaml.

In this case, change the Dockerfile template, add a script, and change the transformer configuration YAML.

1. To change the template, we have added our custom template in `customizations/custom-dockerfile-change-built-in-behavior/nodejs/templates/Dockerfile`. The template is the [same as the one used in the built-in transformer](https://github.com/konveyor/move2kube/blob/main/assets/built-in/transformers/dockerfilegenerator/nodejs/templates/Dockerfile), except we are using a custom base image and a custom `CMD` here.
  ```
  {% raw %}$ cat customizations/custom-dockerfile-change-built-in-behavior/nodejs/templates/Dockerfile
  FROM quay.io/konveyor/nodejs-12
  COPY . .
  RUN npm install
  {{- if .Build }}
  RUN npm run build
  {{- end}}
  EXPOSE {{ .Port }}
  CMD sh start-nodejs.sh{% endraw %}
  ```

1. Add `customizations/custom-dockerfile-change-built-in-behavior/nodejs/templates/start-nodejs.sh`.
  ```console
  $ ls customizations/custom-dockerfile-change-built-in-behavior/nodejs/templates/
  Dockerfile	start-nodejs.sh
  ```

1. The `transformer.yaml` is the transformer configuration with [two changes](https://github.com/konveyor/move2kube/blob/main/assets/built-in/transformers/dockerfilegenerator/nodejs/transformer.yaml) compared to the built-in transformer:
  - The name of our custom transformer is `Nodejs-CustomFiles` (see `name` field in the `metadata` section).
  - We are also specifying an `override` section which is asking Move2Kube to disable the transformer named `Nodejs-Dockerfile` if it is present.
  ```console
  $ cat customizations/custom-dockerfile-change-built-in-behavior/nodejs/nodejs.yaml
  ```
  ```yaml
  apiVersion: move2kube.konveyor.io/v1alpha1
  kind: Transformer
  metadata:
    name: Nodejs-CustomFiles
    labels:
      move2kube.konveyor.io/task: containerization
      move2kube.konveyor.io/built-in: true
  spec:
    class: "NodejsDockerfileGenerator"
    directoryDetect:
      levels: -1
    consumes:
      Service:
        merge: false
    produces:
      Dockerfile:
        disabled: false
      DockerfileForService:
        disabled: false
    override:
      matchLabels:
        move2kube.konveyor.io/name: Nodejs-Dockerfile
    config:
      defaultNodejsVersion: "12"
  ```

1. In the `kubernetes` transformer, change the name and override the config. Also change the default behavior of the transformer, which is to put the Kubernetes yamls in `deploy/yamls` directory by changing the `spec.config.outputPath` to `yamls-elsewhere`.
  ```console
  $ cat customizations/custom-dockerfile-change-built-in-behavior/kubernetes/kubernetes.yaml
  ```
  ```yaml
  {% raw %}apiVersion: move2kube.konveyor.io/v1alpha1
  kind: Transformer
  metadata:
    name: KubernetesWithCustomOutputDirectory
    labels:
      move2kube.konveyor.io/built-in: true
  spec:
    class: "Kubernetes"
    directoryDetect:
      levels: 0
    consumes:
      IR:
        merge: true
    produces:
      KubernetesYamls:
        disabled: false
    override:
      matchLabels:
        move2kube.konveyor.io/name: Kubernetes
    dependency:
      matchLabels:
        move2kube.konveyor.io/kubernetesclusterselector: "true"
    config:
      outputPath: "yamls-elsewhere"
      ingressName: "{{ .ProjectName }}"{% endraw %}
  ```

The next step is [adding custom annotations to Kubernetes YAMLs](/tutorials/customizing-the-output/custom-annotations).
