---
title: "Administrator view tasks"
date: 2022-06-28T15:16:01-06:00
draft: false
---
The administrator view is intended to be used by administrators to set up the Tackle instance environment.
![](/Tackle2/Views/AdminMenu.png)

## Credentials
This management module enables administrators to create and manage credentials for access to private repositories. It also allows for the architects to assign the credentials to applications without knowing their contents. Key credential attributes to have before starting the procedure include:
* Name
* Description (optional)
* Type (Souce control, Maven settings file, Proxy)
* Created by

> **Important** There are three types of credentials. Follow the procedure below for the type being configured.

![](/Tackle2/Views/CredMain.png)

### Configuring source control credentials
Follow the steps below to create new credentials for a source control repository.

**Procedure**
1. Click **Credentials** in the left menu of the **Administrator** view.
2. Click the **Create new** button.
![](/Tackle2/Views/NewCredSourceCUserP.png)
3. Enter the following information.
    * Name
    * Description (Optional)
4. Select the credential type in the **Type** drop-down list.
    * Souce Control
    * Maven Settings File
    * Proxy
5. Select the **credential type** in the **User Credentials** drop-down list and enter the requested information.
    * Username/Password
        * Username
        * Password (Hidden)
        * Create credentials
    * SCM Private Key/Passphrase
        * SCM Private Key
        * Private Key Passphrase (Hidden)
    * Maven
        * Settings File

**Note:** Type specific credential information such as keys and passphrases will be hidden or shown as [Encrypted].

6. Click **Save**.

Tackle validates the input and creates a new credential. SCM keys must be parsed and checked for validity. If the validation fails, an error message displaying “not a valid key/XML file” is displayed.

### Configuring Maven credentials
Follow the steps below to create new credentials for a Maven repository.

**Procedure**
1. Click **Credentials** in the left menu of the **Administrator** view.
2. Click the **Create new** button.
![](/Tackle2/Views/NewCredMaven.png)
3. Enter the following information.
    * Name
    * Description (Optional)
4. Select **Maven Settings File** in the **Type** drop-down list.
5. Upload the settings file.
6. Click **Save**.

Tackle validates the input and creates a new credential. Maven settings.xml files must be parsed and checked for validity. If the validation fails, an error message displaying “not a valid key/XML file” is displayed.

### Configuring proxy credentials
Follow the steps below to create new credentials for a proxy repository.

**Procedure**
1. Click **Credentials** in the left menu of the **Administrator** view.
2. Click the **Create new** button.
![](/Tackle2/Views/NewCredProxy.png)
3. Enter the following information.
    * Name
    * Description (Optional)
4. Select **Proxy** in the **Type** drop-down list.
5. Enter the following information.
    * Username
    * Password

**Note:** Type specific credential information such as keys and passphrases will be hidden or shown as [Encrypted].

6. Click **Save**.

## Repositories
This management module configures the repositories used by Tackle with the following options.

### Configuring Git repositories
Follow the steps below to configure a Git repository.
1. Click **Repositories** and then **Git** in the left menu of the **Administrator** view.
![](/Tackle2/Views/GitConfig.png)
2. Click the **Consume insecure Git repositories** toggle switch to enable.

### Configuring Subversion repositories
Follow the steps below to configure a Subversion repository.
1. Click **Repositories** and then **Subversion** in the left menu of the **Administrator** view.
![](/Tackle2/Views/SubvConfig.png)
2. Click the **Consume insecure Subversion repositories** toggle switch to enable.
Subversion

### Configuring Maven repositories
Follow the steps below to configure a Maven repository or clear the local artifact repository.

#### Configuring the repository
1. Click **Repositories** and then **Maven** in the left menu of the **Administrator** view.
![](/Tackle2/Views/MavenConfig.png)
2. Click the **Force update of dependencies** toggle switch to enable.
3. Click the **Consume insecure artifact repositories** toggle switch to enable.

#### Managing repository size
1. Click **Repositories** and then **Maven** in the left menu of the **Administrator** view.
![](/Tackle2/Views/MavenConfig.png)
2. Click the **Clear repository** link.

**Note** Due to the size of the repository, the size change may not be evident despite the function working properly.

## Proxy
This management module configures HTTP & HTTPS proxy settings.  To configure the proxies click the radio button and enter the following information.

1. Click **Proxy** in the left menu of the **Administrator** view.
![](/Tackle2/Views/ProxyConfig.png)
2. Click the **HTTP proxy** or **HTTPS proxy** toggle switch to enable the proxy connection.
3. Enter the following information
    * Proxy host
    * Proxy port
4. Click the **Authentincation** toggle switch to enable authentication (optional).
5. Select the credential from the drop-down list.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/adminTasks.md)
