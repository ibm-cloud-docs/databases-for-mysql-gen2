---

copyright:
  years: 2026
lastupdated: "2026-05-21"

keywords: admin, superuser, roles, service credentials, mysql users, mysql roles, mysql privileges, mysql connection strings, mysql service credentials, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Managing users, roles, and privileges
{: #user-management}

[Gen 2]{: tag-purple}

MySQL uses a system of roles to manage database permissions. Create users from both the UI and from [MySQL Shell](https://dev.mysql.com/doc/refman/8.4/en/privileges-provided.html). Users who are created from the UI have nearly identical privileges as `admin`, but cannot create other users. Since `admin` has both `CREATE USER` and `GRANT` options, it can create a user and give them all the privileges that it has, including the privilege to create new users.

```sh
mysql> SELECT DISTINCT GRANTEE FROM information_schema.user_privileges;
+-----------------------------+
| GRANTEE                     |
+-----------------------------+
| 'ibm'@'localhost'           |
| 'mysql.session'@'localhost' |
| 'mysql.sys'@'localhost'     |
| 'ibm-backup'@'localhost'    |
| 'admin'@'%'                 |
| 'ibm-replication'@'%'       |
| 'ibm-monitor'@'%'           |
+-----------------------------+
+-----------------+
| user            |
+-----------------+
| admin           |
| ibm-monitor     |
| ibm-replication |
| ibm             |
| ibm-backup      |
| mysql.session   |
| mysql.sys       |
+-----------------+
```

The users below are maintained by {{site.data.keyword.cloud_notm}} and shouldn't be altered or deleted by you:

```sh

| ibm-monitor     |
| ibm-replication |
| ibm             |
| ibm-backup      |
```

When you provision a new deployment in {{site.data.keyword.cloud_notm}}, you are automatically given an `admin` user to access and manage MySQL.

Add users in the UI in _Service credentials_, with the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin), or the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#introduction).

## User management commands
{: #user-management-commands}

For security reasons, we recommend that you do not run DML (Data Manipulation Language) queries on the `mysql.user` table. To protect against altering the `mysql.user` table, you should use DML queries to manage users.

Manage users by using commands such as `CREATE USER`, `ALTER USER`, `RENAME USER`, and `DROP USER`.

A list of users with their hosts information, but without auth information and password hashes, can be extracted by running the following command:

```sh
mysql> SELECT DISTINCT GRANTEE FROM information_schema.user_privileges;
```
{: pre}

## The `admin` user
{: #user-management-admin-user}

When you provision a new deployment in {{site.data.keyword.cloud_notm}}, you are automatically given an admin user to access and manage MySQL. Once you [set the admin password](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-user-management&interface=ui#user-management-set-admin-password-ui), use it to connect to your deployment.

When `admin` creates a resource in a database, like a table, `admin` owns that object. Users who are created from the UI have permissions to `*.*`, which means that any newly created user is able to see any database automatically. Use MySQL sh, or modify permissions of a UI-created user to restrict access. To limit permissions, remove global privileges, if enabled, and grant privileges to a database, or database set, to which a given user is expected to have access.

Set the admin password before using it to connect.
{: important}

### Setting the admin password in the UI
{: #user-management-set-admin-password-ui}
{: ui}

Set your admin password through the UI by selecting your instance from the Resource list in the [{{site.data.keyword.cloud_notm}} Dashboard](https://cloud.ibm.com/){: external}. Then, select **Settings**. Next, select **Change Database Admin Password**.

### Setting the admin password in the CLI
{: #user-management-set-admin-password-cli}
{: cli}

Use the `cdb user-password` command from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to set the admin password.

For example, to set the admin password for a deployment named `example-deployment`, use the following command:

```sh
ibmcloud cdb user-password example-deployment admin <newpassword>
```
{: pre}

### Setting the admin password in the API
{: #user-management-set-admin-password-api}
{: api}

The Foundation Endpoint that is shown on the Overview panel Deployment Details section of your service provides the base URL to access this deployment through the API. Use it with the [Set specified user's password](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#changeuserpassword){: external} endpoint to set the admin password.

```sh
curl -X PATCH `https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/users/admin` \
-H `Authorization: Bearer <>` \
-H `Content-Type: application/json` \
-d `{"password":"newrootpasswordsupersecure21"}` \
```
{: pre}

## Other `ibm` users
{: #user-management-ibm-users}

The `ibm` and the `ibm-replication` accounts are the only superusers on your deployment. A superuser account is not available for you to use. These users are internal administrative accounts that manage replication, metrics, and other functions that ensure the stability of your deployment.

## Users created with `mysql`
{: #user-management-mysql-users}

You can bypass creating users through {{site.data.keyword.cloud_notm}} entirely, and create users directly in MySQL with `mysql`. This allows you to make use of MySQL's native [role and user management](https://dev.mysql.com/doc/refman/8.4/en/privileges-provided.html). Users/roles created in `mysql` must have all of their privileges set manually, as well as privileges to the objects that they create.

Users that are created directly in MySQL do not appear in _Service credentials_, but you can [add them](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connection-strings#adding-users-to-_service-credentials_) if you choose.

Note that these users are not integrated with IAM controls, even if added to _Service Credentials_.
{: .tip}

## User access to tables
{: #user-management-user-tables}

While you cannot delete `mysql database`, users can drop tables, including the `mysql.users` table that contains internal users. Clients shouldn't delete any table belonging to `mysql database` as this action can result in a broken formation, which can only be resolved with a Point-in-time recovery (PITR).

{{site.data.keyword.cloud_notm}} does not alert on formation breaking because of a system table dropped by a client.
{: .important}

## More users and connection strings
{: #creating_users}

Access to your {{site.data.keyword.databases-for-mysql}} deployment is not limited to the admin user. You can create users by using the _Service credentials_ panel, the {{site.data.keyword.IBM_notm}} CLI, or through the {{site.data.keyword.IBM_notm}} {{site.data.keyword.databases-for}} API.

All users on your deployment can use the connection strings, including connection strings for either public or private endpoints.

When you create a user, it is assigned certain database roles and privileges. These privileges include the ability to log in, create databases, and create other users. For more information, see the [Managing users, roles, and privileges](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-user-management) page.

### Creating users in *Service credentials*
{: #user-management-create-users}
{: ui}

1. Navigate to the service dashboard for your service.
2. Click _Service credentials_ to open the _Service credentials_ panel.
3. Click **New credential**.
4. Choose a descriptive name for your new credential.
5. (Optional) Specify whether the new credentials should use a public or private endpoint. Use either `{ "service-endpoints": "public" }` / `{ "service-endpoints": "private" }` in the _Add inline configuration parameters_ field to generate connection strings using the specified endpoint. Use of the endpoint is not enforced. It just controls which hostnames are in the connection strings. Public endpoints are generated by default.
6. Click **Add** to provision the new credentials. A username and password, and an associated database user in the MySQL database are auto-generated.

The new credentials appear in the table, and the connection strings are available as JSON in a click-to-copy field under _View credentials_.

### Creating users from the command line
{: #user-management-cli}
{: cli}

If you manage your service through the {{site.data.keyword.cloud_notm}} CLI and the [cloud databases plug-in](/docs/cli?topic=cli-install-ibmcloud-cli), you can create a new user with `cdb user-create`. For example, to create a new user for an "example-deployment", use the following command.

```sh
ibmcloud cdb user-create example-deployment <newusername> <newpassword>
```
{: pre}

Once the task finishes, you can retrieve the new user's connection strings with the `ibmcloud cdb deployment-connections` command.

### Creating Users from the API
{: #user-management-api}
{: api}

The _Foundation endpoint_ that is shown on the _Overview_ panel _Deployment details_ of your service provides the base URL to access this deployment through the API. To create and manage users, use the base URL with the `/users` endpoint.

```sh
curl -X POST 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"username":"jane_smith", "password":"newsupersecurepassword"}'
```
{: pre}

Once the task has finished, you can retrieve the new user's connection strings, from the `/users/{userid}/connections` endpoint.

### Adding users to _Service credentials_
{: #user-management-add-users}

Creating a new user from the CLI doesn't automatically populate that user's connection strings into _Service credentials_. If you want to add them there, you can create a new credential with the existing user information.

Enter the username and password in the JSON field _Add inline configuration parameters_, or specify a file where the JSON information is stored. For example, putting `{"existing_credentials":{"username":"Robert","password":"supersecure"}}` in the field generates _Service credentials_ with the username "Robert" and password "supersecure" filled into connection strings.

Generating credentials from an existing user does not check for or create that user.
{: tip}
