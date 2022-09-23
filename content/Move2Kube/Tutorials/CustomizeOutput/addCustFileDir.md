---
title: "Add custom files and directories in custom locations"
date: 2022-08-04T19:22:55-06:00
draft: true
---
Move2Kube allows custom template files to be added to the directories of your choice. In this example, we illustrate this by adding a custom helm-chart.

1. Start by creating an empty workspace directory say `workspace` and make it the current working directory. We will assume all commands are executed within this directory.
```console
$ mkdir workspace && cd workspace
```

2. Use the [enterprise-app](https://github.com/konveyor/move2kube-demos/tree/main/samples/enterprise-app) as input for this flow.
```console
$ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/enterprise-app/src -r move2kube-demos  
$ ls src
README.md		config-utils		customers	docs			frontend		gateway			orders
```
In this project, all the apps have a pom.xml file. We will use a custom transformer to place a helm chart created from a template into each of those project directories.

3. Use the Starlark based custom transformer located [here](https://github.com/konveyor/move2kube-transformers/tree/main/add-custom-files-directories-in-custom-locations). We copy it into the `customizations` sub-directory.
```console
$ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d add-custom-files-directories-in-custom-locations -r move2kube-transformers -o customizations
```

4. Transform using this customization and specify the customization using the `-c` flag.
```console
$ move2kube transform -s src/ -c customizations/ --qa-skip
```

Once the output is generated, we can observe one helm-chart was generated for each service and placed within the service directory. Also, note that every helm-chart project is named after the service it is meant for. The contents are shown below for reference:
```
    {% raw %}
        $ tree myproject
            myproject/
            ├── config-utils
            │   ├── helm-chart
            │   │   └── config-utils
            │   │       ├── Chart.yaml
            │   │       ├── templates
            │   │       │   ├── config-utils-deployment.yaml
            │   │       │   ├── config-utils-ingress.yaml
            │   │       │   └── config-utils-service.yaml
            │   │       └── values.yaml
            │   ├── pom.xml
            │   └── src
            │       └── main
            │           └── java
            │               └── io
            │                   └── konveyor
            │                       └── demo
            │                           └── config
            │                               └── ApplicationConfiguration.java
            ├── customers
            │   ├── Makefile
            │   ├── helm-chart
            │   │   └── customers
            │   │       ├── Chart.yaml
            │   │       ├── templates
            │   │       │   ├── customers-deployment.yaml
            │   │       │   ├── customers-ingress.yaml
            │   │       │   └── customers-service.yaml
            │   │       └── values.yaml
            │   ├── pom.xml
            │   └── src
            │       └── main
            │           ├── java
            │           │   └── io
            │           │       └── konveyor
            │           │           └── demo
            │           │               └── ordermanagement
            │           │                   ├── OrderManagementAppInitializer.java
            │           │                   ├── config
            │           │                   │   ├── PersistenceConfig.java
            │           │                   │   └── WebConfig.java
            │           │                   ├── controller
            │           │                   │   └── CustomerController.java
            │           │                   ├── exception
            │           │                   │   ├── ResourceNotFoundException.java
            │           │                   │   └── handler
            │           │                   │       └── ExceptionHandlingController.java
            │           │                   ├── model
            │           │                   │   └── Customer.java
            │           │                   ├── repository
            │           │                   │   └── CustomerRepository.java
            │           │                   └── service
            │           │                       └── CustomerService.java
            │           └── resources
            │               ├── import.sql
            │               └── persistence.properties
            └── gateway
                ├── helm-chart
                │   └── snowdrop-dependencies
                │       ├── Chart.yaml
                │       ├── templates
                │       │   ├── snowdrop-dependencies-deployment.yaml
                │       │   ├── snowdrop-dependencies-ingress.yaml
                │       │   └── snowdrop-dependencies-service.yaml
                │       └── values.yaml
                ├── manifest.yml
                ├── pom.xml
                └── src
                    ├── main
                    │   ├── java
                    │   │   ├── META-INF
                    │   │   │   └── MANIFEST.MF
                    │   │   └── io
                    │   │       └── konveyor
                    │   │           └── demo
                    │   │               └── gateway
                    │   │                   ├── Application.java
                    │   │                   ├── command
                    │   │                   │   └── ProductCommand.java
                    │   │                   ├── controller
                    │   │                   │   ├── CustomersController.java
                    │   │                   │   ├── InventoryController.java
                    │   │                   │   └── OrdersController.java
                    │   │                   ├── exception
                    │   │                   │   ├── ResourceNotFoundException.java
                    │   │                   │   └── handler
                    │   │                   │       └── ExceptionHandlingController.java
                    │   │                   ├── model
                    │   │                   │   ├── Customer.java
                    │   │                   │   ├── Order.java
                    │   │                   │   ├── OrderItem.java
                    │   │                   │   ├── OrderSummary.java
                    │   │                   │   └── Product.java
                    │   │                   ├── repository
                    │   │                   │   ├── CustomerRepository.java
                    │   │                   │   ├── GenericRepository.java
                    │   │                   │   ├── InventoryRepository.java
                    │   │                   │   └── OrderRepository.java
                    │   │                   ├── serialization
                    │   │                   │   ├── CustomerDeserializer.java
                    │   │                   │   └── ProductDeserializer.java
                    │   │                   └── service
                    │   │                       ├── CustomersService.java
                    │   │                       ├── InventoryService.java
                    │   │                       └── OrdersService.java
                    │   └── resources
                    │       ├── application-local.properties
                    │       ├── application-openshift.properties
                    │       └── bootstrap.properties
                    └── test
                        ├── java
                        │   └── io
                        │       └── konveyor
                        │           └── demo
                        │               └── gateway
                        │                   ├── controller
                        │                   │   └── OrdersControllerTest.java
                        │                   ├── model
                        │                   │   └── OrderTest.java
                        │                   ├── repository
                        │                   │   ├── CustomerRepositoryTest.java
                        │                   │   ├── InventoryRepositoryTest.java
                        │                   │   └── OrderRepositoryTest.java
                        │                   └── service
                        │                       └── OrdersServiceTest.java
                        └── resources
                            ├── application-test.properties
                            └── bootstrap.properties
    {% endraw %}
```

## Anatomy of transformer in add-custom-files-directories-in-custom-locations

This custom transformer is more advanced compared to previous cases. It uses a Starlark script (`customhelmchartgen.star`) and several templatization features to achieve the per-service helm-chart requirement. Notice the `{% raw %}{{\ .ServiceName\ }}{% endraw %}` template in the file names of custom helm-chart template in the `templates` sub-directory. The contents of the `add-custom-files-directories-in-custom-locations` custom transformer are shown below:
```
{% raw %}customization/add-custom-files-directories-in-custom-locations/
├── customhelmchartgen.star
├── customhelmchartgen.yaml
└── templates
    └── helm-chart
        └── {{\ .ServiceName\ }}
            ├── Chart.yaml
            ├── templates
            │   ├── {{\ .ServiceName\ }}-deployment.yaml
            │   ├── {{\ .ServiceName\ }}-ingress.yaml
            │   └── {{\ .ServiceName\ }}-service.yaml
            └── values.yaml{% endraw %}
```
The code of the Starlark script (`cat customizations/add-custom-files-directories-in-custom-locations/customhelmchartgen.star`) is shown below. At a high level, the custom transformer detects a Java project if it finds `pom.xml` in the directory when the `directory_detect()` function is invoked in the detect phase. Once the Java project is detected, the corresponding project path and service name are passed to the transform phase through Move2Kube.

In the transform phase, the `transform()` function is invoked with the discovered service artifacts from the detect phase. These artifacts are used to fill the helm-chart templates shown above and produced as the output in a per-service directory structure.

```python
{% raw %}PomFile = "pom.xml"

# Performs the detection of pom file and extracts service name
def directory_detect(dir):
    dataFilePath = fs.pathjoin(dir, PomFile)
    if fs.exists(dataFilePath):
        serviceName = getServiceName(dataFilePath)
        return  {serviceName: [{
                    "paths": {"ProjectPath": [dir]} }] }

# Creates the customized helm chart for every service
def transform(new_artifacts, old_artifacts):
    pathMappings = []
    artifacts = []
    pathTemplate = "{{ SourceRel .ServiceFsPath }}"
    for v in new_artifacts:
        serviceName = v["configs"]["Service"]["serviceName"]
        dir = v['paths']['ProjectPath'][0]
        # Create a path template for the service
        pathTemplateName = serviceName.replace("-", "") + 'path'
        tplPathData = {'ServiceFsPath': dir, 'PathTemplateName': pathTemplateName}
        pathMappings.append({'type': 'PathTemplate', \
                            'sourcePath': pathTemplate, \
                            'templateConfig': tplPathData})
        # Since the helm chart uses the same templating character {{ }} as Golang templates,
        # we use `SpecialTemplate` type here where the templating character is <~ ~>.
        # The `Template` type can be used for all normal cases
        pathMappings.append({'type': 'SpecialTemplate', \
                    'destinationPath': "{{ ." + pathTemplateName + " }}", \
                    'templateConfig': {'ServiceFsPath': dir, 'ServiceName': serviceName}})
        pathMappings.append({'type': 'Source', \
                    'sourcePath': "{{ ." + pathTemplateName + " }}",
                    'destinationPath': "{{ ." + pathTemplateName + " }}"})

    return {'pathMappings': pathMappings, 'artifacts': artifacts}

# Extracts service name from pom file
def getServiceName(filePath):
    data = fs.read(filePath)
    lines = data.splitlines()
    for l in lines:
        if 'artifactId' in l:
            t = l.split('>')
            t2 = t[1].split('<')
            return t2[0]{% endraw %}
```
