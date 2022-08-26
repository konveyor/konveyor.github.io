---
title: "Commands"
date: 2022-08-05T11:13:15-06:00
draft: true
---
Move2Kube creates all the resources required for deploying applications into Kubernetes including containerization and Kubernetes resources. It supports translating from Docker swarm/Docker-compose, Cloud Foundry, and other non-containerized applications. If the application does not use any of those or is not containerized, it can still be transformed by Move2Kube.

For more documentation and support, visit https://move2kube.konveyor.io/.

> **Note:** Command options
Use these commands to view the options available in Move2Kube.
>```
  -h, --help               help for move2kube
      --log-file string    File to store the logs in. By default it only prints to console.
      --log-level string   Set logging levels. (default "info")
>```

## Move2Kube commands
There are four phases of the Move2Kube processes that are created, configured, and run using four commands and their options:
* [Move2Kube collect](/commands/collect) - Collects and processes metadata from multiple sources.
* [Move2Kube plan](/commands/plan) - Plans the deployment of an application into Kubernetes.
* [Move2Kube transform](/commands/transform) - Transforms the application using the results of Move2Kube plan stage.
* [Move2Kube version](/commands/version) - Assigns the application version information.

# Move2Kube collect command
Move2Kube collects metadata from multiple sources (cluster, image repo etc.), then filters and summarizes them into a YAML.
```
move2kube collect [flags]
```

## Collect options
Move2Kube provides the following options for collecting.
```
  -a, --annotations string   Specify annotations to select collector subset.
  -h, --help                 help for collect
  -o, --output string        Specify output directory for collect. (default ".")
  -s, --source string        Specify source directory for the artifacts to be considered while collecting.
```

### Options inherited from parent commands
```
      --log-file string    File to store the logs in. By default it only prints to console.
      --log-level string   Set logging levels. (default "info")
```

# Move2Kube plan
Move2Kube discovers and creates a plan-file based on an input directory built after running the collect command.
```
move2kube plan [flags]
```

## Plan options
Move2Kube provides the following options for planning.
```
  -f, --config strings                Specify config file locations.
  -c, --customizations string         Specify directory where customizations are stored.
      --disable-local-execution       Allow files to be executed locally.
  -h, --help                          help for plan
  -n, --name string                   Specify the project name. (default "myproject")
  -p, --plan string                   Specify a file path to save plan to. (default "m2k.plan")
      --preset strings                Specify preset config to use.
      --set-config stringArray        Specify config key-value pairs.
  -s, --source string                 Specify source directory. (default ".")
  -t, --transformer-selector string   Specify the transformer selector.
```

### Options inherited from parent commands
```
      --log-file string    File to store the logs in. By default it only prints to console.
      --log-level string   Set logging levels. (default "info")
```

# Move2Kube transform
Transform functionality modifies artifacts using the Move2Kube plan-file.
```
move2kube transform [flags]
```

## Transform options
Move2Kube provides the following options for transform.
```
  -f, --config strings                Specify config file locations.
      --config-out string             Specify config file output location. (default ".")
  -c, --customizations string         Specify directory where customizations are stored.
      --disable-local-execution       Allow files to be executed locally.
  -h, --help                          help for transform
      --ignore-env                    Ignore data from local machine.
  -n, --name string                   Specify the project name. (default "myproject")
  -o, --output string                 Path for output. Default will be directory with the project name. (default ".")
      --overwrite                     Overwrite the output directory if it exists. By default we don't overwrite.
  -p, --plan string                   Specify a plan file to execute. (default "m2k.plan")
      --preset strings                Specify preset config to use.
      --qa-cache-out string           Specify cache file output location. (default ".")
      --qa-persist-passwords          Stores passwords too in the config.
      --qa-skip                       Enable/disable the default answers to questions posed in QA Cli sub-system. If disabled, you will have to answer the questions posed by QA during interaction.
      --set-config stringArray        Specify config key-value pairs.
  -s, --source string                 Specify source directory to transform. If you already have a m2k.plan then this will override the sourceDir value specified in that plan.
  -t, --transformer-selector string   Specify the transformer selector.
```

### Options inherited from parent commands
```
      --log-file string    File to store the logs in. By default it only prints to console.
      --log-level string   Set logging levels. (default "info")
```

# Move2Kube version
Version functionality prints the application version information.
```
move2kube version [flags]
```

## Version options
Move2Kube provides the following options for versioning.
```
  -h, --help   help for version
  -l, --long   Print the version details.
```

### Options inherited from parent commands
```
      --log-file string    File to store the logs in. By default it only prints to console.
      --log-level string   Set logging levels. (default "info")
```
