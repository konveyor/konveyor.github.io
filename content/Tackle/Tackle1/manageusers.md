---
title: "Managing users and credentials"
date: 2022-05-05T16:40:47-06:00
draft: false
---

Follow the procedures in this section to  manage Tackle users and passwords using the Keycloak admin console.

## Accessing the Keycloak admin console
A Keycloak admin user is created when Tackle is installed. The admin username and password are needed to log in to the Keycloak admin console. The credentials are stored in the tackle-keycloak secret.

**Prerequisites**

Cluster-admin privileges.

**Procedure**
1. Run the following command to obtain the admin credentials:
```
$ kubectl get secret tackle-keycloak -o go-template='{{range $k,$v := .data}}{{printf "%s: " $k}}{{if not $v}}{{$v}}{{else}}{{$v | base64decode}}{{end}}{{"\n"}}{{end}}'
Example output
ADMIN_PASSWORD: <password>
ADMIN_USERNAME: admin
```
2. Launch a browser and navigate to **https://<www.example.com>/auth** and specify the **Tackle cluster URL**.
3. Log in to the Keycloak admin console with the admin user name and password.

## Changing the default password
Follow the steps below to change the default password of the tackle user.

**Prerequisites**

* Cluster-admin privileges
* Keycloak admin user name and password.

**Procedure**
1. Log in to the Keycloak admin console at **https://<www.example.com>/auth** and specify the **Tackle cluster URL**.
2. Locate the tackle user in the Tackle realm. See [Searching for users](https://www.keycloak.org/docs/latest/server_admin/index.html#searching-for-users) in the Keycloak documentation.
3. Update the tackle user’s password. See [User Credentials](https://www.keycloak.org/docs/latest/server_admin/index.html#_user-credentials).
4. Log out of the Keycloak admin console.
5. Log in to the Tackle web console as the tackle user to verify the new password.

## Adding users
Follow the steps below to add users by using the Keycloak admin console.

**Prerequisites**

* Cluster-admin privileges
* Keycloak admin user name and password.

**Procedure**
1. Log in to the Keycloak admin console at **https://<www.example.com>/auth** and specify the **Tackle cluster URL**.
Create a new user in the Tackle realm. See [Creating a new user](https://www.keycloak.org/docs/latest/server_admin/index.html#_create-new-user) in the Keycloak documentation.
2. Create a password for the new user. See [Creating a password](https://www.keycloak.org/docs/latest/server_admin/index.html#creating-a-password-for-the-user) for the user.

**Optional:** Set attributes and permissions for the new user. See [User profile](https://www.keycloak.org/docs/latest/server_admin/index.html#user-profile).

### Additional resources for Keycloak

* [Admin console](https://www.keycloak.org/docs/latest/server_admin/#admin-console) in the Keycloak documentation.
* [User management](https://www.keycloak.org/docs/latest/server_admin/#user-management) in the Keycloak documentation.
* [Keycloak Operator on Kubernetes](https://www.keycloak.org/getting-started/getting-started-operator-kubernetes) in the Keycloak documentation.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/manageusers.md)
