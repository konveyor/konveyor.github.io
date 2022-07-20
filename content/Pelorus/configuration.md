---
title: "Configuration the Pelorus stack"
date: 2022-07-07T17:20:01-06:00
draft: false
---
The Pelorus stack (Prometheus, Grafana, Thanos, etc.) is configured by changing the `values.yaml` file that is passed to Helm. The recommended practice is to make a copy of the one [values.yaml](https://github.com/konveyor/pelorus/blob/master/charts/pelorus/values.yaml) file and [charts/pelorus/configmaps/](https://github.com/konveyor/pelorus/tree/master/charts/pelorus/configmaps) directory, and store in in your own configuration repo for safe keeping, and updating. Once established, you can make configuration changes by updating your `charts/pelorus/configmaps` files with `values.yaml` and applying the changes like so:
```
oc apply -f `myclusterconfigs/pelorus/configmaps
helm upgrade pelorus charts/pelorus --namespace pelorus --values myclusterconfigs/pelorus/values.yaml
```

The following configurations may be made through the `values.yaml` file:

|Variable|Required|Explanation|Default Value|
---|---|---|---|
|<code>openshift_prometheus_htpasswd_auth</code>|yes|The contents for the htpasswd file that Prometheus will use for basic authentication user.|User: <code>internal</code>, Password: <code>changeme</code>|
|<code>openshift_prometheus_basic_auth_pass</code>|yes|The password that grafana will use for its Prometheus datasource. Must match the above.|<code>changme</code>|
|<code>custom_ca</code>|no|Whether or not the cluster serves custom signed certificates for ingress (e.g. router certs). If <code>true</code> we will load the custom via the <a href="https://docs.openshift.com/container-platform/4.4/networking/configuring-a-custom-pki.html#certificate-injection-using-operators_configuring-a-custom-pki">certificate injection method</a>|<code>false</code>|
|<code>exporters</code>|no|Specified which exporters to install. See <a href="https://pelorus.readthedocs.io/en/latest/Configuration/#configuring-exporters">Configuring Exporters</a>.|Installs deploytime exporter only.|

**Procedure**
1. Copy the [values.yaml](https://github.com/konveyor/pelorus/blob/master/charts/pelorus/values.yaml) file and [charts/pelorus/configmaps/](https://github.com/konveyor/pelorus/blob/master/charts/pelorus/configmaps) directory, and save in local configuration repo.
2. Update the `charts/pelorus/configmaps` files with `values.yaml` to change the configuration.
3. Apply the changes.
```
oc apply -f `myclusterconfigs/pelorus/configmaps
helm upgrade pelorus charts/pelorus --namespace pelorus --values myclusterconfigs/pelorus/values.yaml
```

## **Configuring exporters**
The _exporter_ data collection application pulls data from various tools and platforms so it can be consumed by Pelorus dashboards. Each exporter gets deployed individually alongside the core Pelorus stack.

There are currently three _exporter_ types which need to be specified using  `exporters.instances.exporter_type` value:
* `deploytime`
* `failure`
* `comittime`

Exporters are deployed using a list of `exporters.instances` inside the `values.yaml` file that correspond to the OpenShift ConfigMap configurations in the `charts/pelorus/configmaps/` directory. Some exporters also require secrets be created when integrating with external tools and platforms. A sample exporter configuration may look like this:

```
exporters:
  instances:
  - app_name: deploytime-exporter
    exporter_type: deploytime
    env_from_configmaps:
    - pelorus-config
    - deploytime-config
```
A single exporter can be deployed multiple times to gather data from different sources. For example, deploying two instances of the Commit Time Exporter to pull commit data from both GitHub and a private GitHub Enterprise instance.

Each exporter has a unique set of environment variables to configure its integrations and behavior. These variables are set by using example ConfigMap object configurations similar to the Kubernetes secrets and listing them under `env_from_configmaps` or under `env_from_secrets` accordingly.
```
exporters:
  instances:
  - app_name: committime-github
    exporter_type: comittime
    env_from_secrets:
    - github-credentials
    env_from_configmaps:
    - pelorus-config
    - committime-config

  - app_name: committime-gh-enterprise
    exporter_type: comittime
    env_from_secrets:
    - github-enterprise-credentials
    env_from_configmaps:
    - pelorus-config
    - comittime-enterprise-config
```

### Exporter ConfigMap configuration values
Each exporter is configured using ConfigMap objects. Each ConfigMap must be in a separate file and must be applied to the cluster before deploying the Pelorus Helm chart.

> **Important:**  Store the ConfigMap folder outside of local Pelorus Git repository and modify accordingly.

ConfigMap can be applied individually or all together:
```
# Only deploytime ConfigMap
oc apply -f charts/pelorus/configmaps/deploytime.yaml

# All at once
oc apply -f charts/pelorus/configmaps/
```
Example ConfigMap for the `deploytime-exporter` with the unique name `deploytime-config`:
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: deploytime-config
  namespace: pelorus
data:
  PROD_LABEL: "default"    # "" / PROD_LABEL is ignored if NAMESPACES are provided
  NAMESPACES: "default"    # ""
```

### Authentication to remote services
Pelorus exporters make use of `personal access tokens` when authentication is required. It is recommended to configure the Pelorus exporters with authentication using the `TOKEN` key to avoid connection rate limiting and access restrictions.

See [Github Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) for more information about personal access tokens.

* [Jira / Bitbucket Personal Access Tokens](https://confluence.atlassian.com/bitbucketserver/personal-access-tokens-939515499.html)
* [Gitlab Personal Access Tokens](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html)
* [Microsoft Azure DevOps Tokens](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=Windows)
* [Gitea Tokens](https://docs.gitea.io/en-us/api-usage/#generating-and-listing-api-tokens)

Use Openshift secrets to store a personal access token securely and make it available to Pelorus to use for all of the exporters. The following is an example for the committime exporter in Github.
```
oc create secret generic github-secret --from-literal=TOKEN=<personal access token> -n pelorus
```
A Pelorus exporter can require additional information to collect data such as the remote `GIT_API` or `USER` information. It is recommended to consult the requirements for each Pelorus exporter in this guide and include the additional key/value information in the Openshift secret. An API example is `github.mycompany.com/api/v3`

CLI commands can also be substituted with Secret templates. Example files can be found [here](https://github.com/konveyor/pelorus/tree/master/charts/pelorus/secrets)

Below is an example of creating a secret containing the Git username, token, and sample API.
```
oc create secret generic github-secret --from-literal=USER=<username> --from-literal=TOKEN=<personal access token> --from-literal=GIT_API=<api> -n pelorus
```
This is an example of creating a secret in Jira.
```
oc create secret generic jira-secret \
--from-literal=SERVER=<Jira Server> \
--from-literal=USER=<username/e-mail> \
--from-literal=TOKEN=<personal access token> \
-n pelorus
```
This is an example of creating a secret in ServiceNow.
```
oc create secret generic snow-secret \
--from-literal=SERVER=<ServiceNow Server> \
--from-literal=USER=<username> \
--from-literal=TOKEN=<personal access token> \
--from-literal=TRACKER_PROVICER=servicenow \
--from-literal=APP_FIELD=<Custom app label field> \
-n pelorus
```
### Committing the Time Exporter
The Commit Time Exporter finds relevant builds in OpenShift and associates a commit from the build's source code repository with a container image built from that commit. It captures a timestamp for the commit and the resulting image hash so the Deploy Time Exporter can associate that image with a production deployment.

> **Important:** All builds associated with a particular application must be labelled with the same `app.kubernetes.io/name=&lt;app_name>` label.

#### Annotated binary (local) source build support
The Commit Time Exporter may be used in conjunction with builds where values required to gather commit time from the source repository are missing. In this case, each build is required to be annotated with two values allowing the Commit Time Exporter to calculate metric from the Build.

Annotate build with the following commands:
```
oc annotate build <build-name> -n <namespace> --overwrite io.openshift.build.commit.id=<commit_hash>

oc annotate build <build-name> -n <namespace> --overwrite io.openshift.build.source-location=<repo_uri>
```
Custom annotation names may also be configured using ConfigMap Data Values.

> **Note:** The requirement to label the build with `app.kubernetes.io/name=&lt;app_name>` for the annotated Builds applies.

#### Instance configuration
```
exporters:
  instances:
  - app_name: committime-exporter
    exporter_type: committime
    env_from_secrets:
    - github-secret
    env_from_configmaps:
    - pelorus-config
    - committime-config
```
#### ConfigMap data values
This exporter provides several configuration options, passed via `pelorus-config` and `committime-config` variables. User may define own ConfigMaps and pass to the committime exporter in a similar way.

|Variable|Required|Explanation|Default Value|
|---|---|---|---|
|<code>USER</code>|yes|User's github username|unset|
|<code>TOKEN</code>|yes|User's Github API Token|unset|
|<code>GIT_API</code>|no|Github API FQDN. This allows the override for Github Enterprise users. Currently only applicable to <code>github</code> provider type.|<code>api.github.com</code>|
|<code>LOG_LEVEL</code>|no|Set the log level. One of <code>DEBUG</code>, <code>INFO</code>, <code>WARNING</code>, <code>ERROR</code>|<code>INFO</code>|
|<code>APP_LABEL</code>|no|Changes the label key used to identify applications|<code>app.kubernetes.io/name</code>|
|<code>NAMESPACES</code>|no|Restricts the set of namespaces from which metrics will be collected. ex: <code>myapp-ns-dev,otherapp-ci</code>|unset; scans all namespaces|
|<code>PELORUS_DEFAULT_KEYWORD</code>|no|ConfigMap default keyword. If specified it's used in other data values to indicate "Default Value" should be used|<code>default</code>|
|<code>COMMIT_HASH_ANNOTATION</code>|no|Annotation name associated with the Build from which hash is used to calculate commit time|<code>io.openshift.build.commit.id</code>|
|<code>COMMIT_REPO_URL_ANNOTATION</code>|no|Annotation name associated with the Build from which GIT repository URL is used to calculate commit time|<code>io.openshift.build.source-location</code>|

### Deploying the Time Exporter
The Deploy Time Exporter captures the timestamp of a deployment in a production environment.

> **Important:** All deployments associated with a particular application must be labelled with the same `app.kubernetes.io/name=&lt;app_name>` label.

#### Instance configuration

```
exporters:
  instances:
  - app_name: deploytime-exporter
    exporter_type: deploytime
    env_from_configmaps:
    - pelorus-config
    - deploytime-config
```

#### ConfigMap Data Values Exporter
The ConfigMap Data Values Exporter provides several configuration options that are passed using `pelorus-config` and `deploytime-config` variables. Users can define custom ConfigMaps and pass them to the committime exporter in a similar way.

|Variable|Required|Explanation|Default Value|
|---|---|---|---|
|code>LOG_LEVEL</code>|no|Set the log level. One of <code>DEBUG</code>, <code>INFO</code>, <code>WARNING</code>, <code>ERROR</code>|<code>INFO</code>|
|<code>APP_LABEL</code>|no|Changes the label key used to identify applications|<code>app.kubernetes.io/name</code>|
|<code>PROD_LABEL</code>|no|Changes the label key used to identify namespaces that are considered production environments.|unset; matches all namespaces|
|<code>NAMESPACES</code>|no|Restricts the set of namespaces from which metrics will be collected. ex: <code>myapp-ns-dev,otherapp-ci</code>|unset; scans all namespaces|
|<code>PELORUS_DEFAULT_KEYWORD</code>|no|ConfigMap default keyword. If specified it's used in other data values to indicate "Default Value" should be used|<code>default</code>|

### Failure Time Exporter
The Failure Time Exporter captures the timestamp of a failure in a production environment and when it is resolved.

Failure Time Exporter may be deployed with one of three backends: JIRA, GithHub Issues, and ServiceNow. One clusters' namespace can have multiple instances of the Failure Time Exporter for each backend and/or watched projects.

Each of the backend requires specific [configuration](https://pelorus.readthedocs.io/en/latest/Configuration/#failureconfigmap), using the ConfigMap associated with the exporter instance.

> **Important:** All GitHub Issues and JIRA backends issues associated with a particular application must be labelled with the same `app.kubernetes.io/name=&lt;app_name>` label, or custom label if it was configured via `APP_LABEL`.

#### Instance Config Jira Exporter
The Instance Config JIRA Exporter expects a specific workflow be used when the issue needs to be `Resolved` with `resolutiondate` and all the relevant issues to be a `Bug` with the `Highest` priority with the `app.kubernetes.io/name=&lt;app_name>` label.

This exporter can be customized to orgaizational needs by configuring `JIRA_JQL_SEARCH_QUERY`, `JIRA_RESOLVED_STATUS` and `APP_LABEL` options. Please refer to the [Failure Exporter ConfigMap Data Values](https://pelorus.readthedocs.io/en/latest/Configuration/#failureconfigmap).
```
exporters:
  instances:
  - app_name: failuretime-exporter
    exporter_type: failure
    env_from_secrets:
    - jira-secret
    env_from_configmaps:
    - pelorus-config
    - failuretime-config
```
#### Instance Config Github
```
exporters:
  instances:
  - app_name: failuretime-exporter
    exporter_type: failure
    env_from_secrets:
    - github-issue-secret
    env_from_configmaps:
    - pelorus-config
    - failuretime-github-config
```
#### ConfigMap data values
This exporter provides several configuration options, passed via `pelorus-config` and `failuretime-config` variables. User may define own ConfigMaps and pass to the committime exporter in a similar way.

|Variable|Required|Explanation|Default Value|
|---|---|---|---|
|<code>PROVIDER</code>|no|Set the type of failure provider. One of <code>jira</code>, <code>servicenow</code>|<code>jira</code>|
|<code>LOG_LEVEL</code>|no|Set the log level. One of <code>DEBUG</code>, <code>INFO</code>, <code>WARNING</code>, <code>ERROR</code>|<code>INFO</code>|
|<code>SERVER</code>|yes|URL to the Jira or ServiceNowServer|unset|
|<code>USER</code>|yes|Tracker Username|unset|
|<code>TOKEN</code>|yes|User's API Token|unset|
|<code>APP_LABEL</code>|no|Used in GitHub and JIRA only. Changes the label key used to identify applications|<code>app.kubernetes.io/name</code>|
|<code>APP_FIELD</code>|no|Required for ServiceNow, field used for the Application label. ex: "u_appName"|'u_application'|
|<code>PROJECTS</code>|no|Used for Jira Exporter to query issues from a list of project keys. Comma separated string. ex: <code>PROJECTKEY1,PROJECTKEY2</code>. Value is ignored if <code>JIRA_JQL_SEARCH_QUERY</code> is defined.|unset|
|<code>PELORUS_DEFAULT_KEYWORD</code>|no|ConfigMap default keyword. If specified it's used in other data values to indicate "Default Value" should be used|<code>default</code>|
|<code>JIRA_RESOLVED_STATUS</code>|no|Used for Jira Exporter to define list Issue states that indicates whether issue is considered resolved. Comma separated string. ex: <code>Done,Closed,Resolved,Fixed</code>|unset|

### Github Failure Exporter details
Use the Github failure exporter is to define the Github token and Github projects. The `TOKEN` should be defined in an OpenShift secret as described above, and the `PROJECTS` are defined in the failure exporter ConfigMap. This is an example of the Github Failure Exporter.
```
kind: ConfigMap
metadata:
  name: failuretime-config
  namespace: pelorus
data:
  PROVIDER: "github"     # jira  |  jira, github, servicenow
  SERVER:                #       |  URL to the Jira or ServiceNowServer, can be overriden by env_from_secrets
  USER:                  #       |  Tracker Username, can be overriden by env_from_secrets
  TOKEN:                 #       |  User's API Token, can be overriden by env_from_secrets
  PROJECTS: "konveyor/todolist-mongo-go,konveyor/todolist-mariadb-go"
  APP_FIELD: "todolist"  #       |  This is optional for the Github failure exporter
```
The `PROJECTS` key is comma deliniated and formated at the `Github_organization/Github_repository`. The `APP_FIELD` key can be used to associate the Github repository with a particular application

Any Github issue must be labeled as a bug. Issues can optionally have a label associated with a particular application. If an application is not found, it will default the app to the Github repository name.

This is an example of the output with the `APP_FIELD` for the [todolist-mongo-go repository](https://github.com/konveyor/todolist-mongo-go).
```
05-25-2022 13:09:40 INFO     Collected failure_creation_timestamp{ app=todolist, issue_number=3 } 1652305808.0
05-25-2022 13:09:40 INFO     Collected failure_creation_timestamp{ app=todolist-mariadb-go, issue_number=4 } 1652462194.0
05-25-2022 13:09:40 INFO     Collected failure_creation_timestamp{ app=todolist, issue_number=3 } 1652394664.0
```
This is an example of not setting the `APP_FIELD`.
```
05-25-2022 13:16:14 INFO     Collected failure_creation_timestamp{ app=todolist-mongo-go, issue_number=3 } 1652305808.0
05-25-2022 13:16:14 INFO     Collected failure_creation_timestamp{ app=todolist-mariadb-go, issue_number=4 } 1652462194.0
05-25-2022 13:16:14 INFO     Collected failure_creation_timestamp{ app=todolist-mariadb-go, issue_number=3 } 1652394664.0
```
### ServiceNow failure exporter details
ServiceNow integration is configured to process resolved (stage=6) Incident objects. Because ServiceNow does not have tags in all versions, there may be a need to configure a custom field on the Incident object to provide an application name to match Openshift Labels. The exporter uses the `opened_at` field for a created timestamp and the `resolved_at` field for the resolution timestamp. The exporter monitors all incidents and will create a resolution record when a `resolved_at` field is populated.

A custom field can be configured with the following steps:
1. Navigate to an existing Incident.
2. In the upper left menu, click **Configure**, then **Form Layout**.
3. Create a new field (String, Table or reference a List).
4. Verify the APP_FIELD name using the API Explorer.

For more information see the [Pelorus documentation site](https://pelorus.readthedocs.io/en/latest/).

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Pelorus/configuration.md)
