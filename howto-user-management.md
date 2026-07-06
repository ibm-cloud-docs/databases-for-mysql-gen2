---

copyright:
  years: 2026
lastupdated: "2026-06-22"

keywords: admin, superuser, roles, service credentials, mysql users, mysql roles, mysql privileges, mysql connection strings, mysql service credentials, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Managing users, roles, and privileges
{: #user-management}

[Gen 2]{: tag-purple}

As part of provisioning a new deployment in {{site.data.keyword.cloud}}, you can use the service credential console page to create a user with different roles (Manager and Writer).

{{site.data.keyword.databases-for-mysql}} deployments come with predefined roles that provide appropriate privilege sets for common use cases. Create users with the `Manager` or `Writer` role using the {{site.data.keyword.cloud}} service credential interface — via UI or CLI. These users come with necessary credentials to connect to and manage the deployment.

## The Manager user
{: #user-manager}

The `Manager` user functions as an admin-like user and is automatically granted the `ibm_manager_role`, which provides comprehensive database administration capabilities. Users with this role can manage schemas, manipulate data, create users, and monitor the database.

The `Manager` user (admin-like) comes with the following privileges:

```sql
SELECT, INSERT, UPDATE, DELETE
CREATE, DROP, ALTER, INDEX
CREATE VIEW, SHOW VIEW
CREATE ROUTINE, ALTER ROUTINE, EXECUTE
TRIGGER, EVENT
CREATE TEMPORARY TABLES, LOCK TABLES, REFERENCES
CREATE USER, CREATE ROLE
PROCESS, SHOW DATABASES
RELOAD
```
{: pre}

When the `Manager` user creates a resource in a database, such as a table, that user owns the object. Objects that are created by the `Manager` user are not accessible to other users unless you explicitly grant them access.

The biggest difference between the `Manager` user and any other users you add to your deployment is the `PROCESS` privilege and user management capabilities. The `PROCESS` privilege provides a set of permissions that makes the `Manager` user appropriate for monitoring the database server, allowing them to view information about running queries and connections. The `CREATE USER` and `CREATE ROLE` privileges allow the `Manager` user to create and manage other database users.

You can also use the `Manager` user to grant privileges to other users on your deployment.

To grant write access to another database user, use a command like:

```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON database_name.* TO 'joe'@'%';
```
{: pre}

To set up a specific user with the manager role, `mary`, use a command like:

```sql
GRANT ibm_manager_role TO 'mary'@'%';
```
{: pre}

## The Writer user
{: #user-writer}

The `Writer` user is granted the `ibm_writer_role`, which provides read and write access to data without schema modification capabilities. This role is suitable for standard application users who need to manipulate data but not alter database structure.

The `Writer` user comes with the following privileges:

```sql
SELECT, INSERT, UPDATE, DELETE
CREATE TEMPORARY TABLES
EXECUTE
SHOW VIEW
```
{: pre}

## Changing the user password in the UI
{: #user-management-set-manager-password-ui}
{: ui}

Changing a user password is not supported via the {{site.data.keyword.cloud_notm}} console on Gen 2. However, you can update a password using tools, such as `mysql` client by executing the following command:

```sql
ALTER USER 'username'@'host' IDENTIFIED BY 'new_password';
```
{: pre}

## Creating the Manager user in the CLI
{: #user-management-create-manager-user-cli}
{: cli}

Use one of the following commands from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to create the `Manager` user.

```sh
ibmcloud resource service-key-create <service_key_name> Manager --instance-name <instance_name>
```
{: pre}

```sh
ibmcloud resource service-key-create <service_key_name> Manager --instance-id <guid>
```
{: pre}

These commands can be used when creating a user with either the Writer or Manager role. The command creates a MySQL user with appropriate privileges based on the selected role.

Similarly, for creating a user with the `Writer` role, use the following command:

```sh
ibmcloud resource service-key-create <service_key_name> Writer --instance-name <instance_name>
```
{: pre}

## Deleting the user in the CLI
{: #user-management-delete-manager-user-cli}
{: cli}

Use the following command from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to delete the created user.

```sh
ibmcloud resource service-key-delete <service_key_name>
```
{: pre}

## Changing the user password in the CLI
{: #user-management-set-manager-pw-cli}
{: cli}

Changing the user password is not supported via the CLI on Gen 2. However, you can update the password using tools, such as `mysql` client by executing the following command:

```sql
ALTER USER 'username'@'host' IDENTIFIED BY 'new_password';
```
{: pre}

## Changing the user password through the API
{: #user-management-set-manager-password-api}
{: api}

Changing the user password is not supported via API on Gen 2. However, you can update the password using tools, such as `mysql` client by executing the following command:

```sql
ALTER USER 'username'@'host' IDENTIFIED BY 'new_password';
```
{: pre}

## Users created with `mysql` client
{: #user-management-mysql}

You can bypass creating users through {{site.data.keyword.cloud_notm}} entirely, and create users directly in MySQL with the `mysql` client. This allows you to use MySQL's native [user and privilege management](https://dev.mysql.com/doc/refman/8.0/en/access-control.html){: .external}. Users created in MySQL must have all of their privileges set manually, as well as privileges to the objects that they create.

Users that are created directly in MySQL do not appear in _Service credentials_, but you can [add them](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connection-strings&interface=ui).

These users are not integrated with IAM controls, even if added to _Service credentials_.
{: note}

## Additional users and connection strings
{: #creating_users}

Access to your {{site.data.keyword.databases-for-mysql}} deployment is not limited to the `Manager` user. Additional users can be created using the CLI, with the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference), or the [{{site.data.keyword.databases-for}} API](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-api).

All users on your deployment can use the connection strings, including connection strings for private endpoints.

When you create a user, it is assigned certain database roles and privileges based on the role you select (Manager or Writer). These privileges include the ability to log in and access databases according to the role's permissions.

## Creating users in the UI
{: #user-management-creating-users-service-cred}
{: ui}

1. Go to the service dashboard for your service.
2. Click **Service credentials**.
3. Click **New credential**.
4. Choose a descriptive name for your new credential.
7. Click **Add** to provision the new credential. A username and password, and an associated database user in the MySQL database are auto-generated.

The new credential appears in the table, and the connection strings are available as JSON in a click-to-copy field under _View credentials_.

## Creating users from the CLI
{: #user-management-creating-users-cli}
{: cli}

If you manage your service through the {{site.data.keyword.cloud_notm}} CLI and the [{{site.data.keyword.databases-for}} plug-in](/docs/cli?topic=cli-install-ibmcloud-cli), you can create a new user with `cdb user-create`. For example, to create a new user for an "example-deployment", use the following command:

```sh
ibmcloud cdb user-create example-deployment <NEW_USER_NAME> <NEW_PASSWORD>
```
{: pre}

Once the task has finished, you can retrieve the new user's connection strings with the `ibmcloud cdb deployment-connections` command.

## Creating users from the API
{: #user-management-creating-users-api}
{: api}

The _Foundation endpoint_ that is shown on the _Overview_ panel _Deployment details_ of your service provides the base URL to access this deployment through the API. To create and manage users, use the base URL with the `/users` endpoint.

```sh
curl -X POST 'https://api.{region}.databases.cloud.ibm.com/v4/ibm/deployments/{id}/users' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"username":"jane_smith", "password":"newsupersecurepassword"}'
```
{: pre}

After the task finishes, retrieve the new user's connection strings from the `/users/{userid}/connections` endpoint.

## Adding users to _Service credentials_
{: #user-management-adding-users-service-cred}

Creating a new user from the CLI or API doesn't automatically populate that user's connection strings into _Service credentials_. To add them, create a new credential with the existing user information.

Enter the username and password in the JSON field _Add inline configuration parameters_, or specify a file where the JSON information is stored. For example, putting `{"existing_credentials":{"username":"Robert","password":"supersecure"}}` in the field generates _Service credentials_ with the username "Robert" and password "supersecure" filled into connection strings.

Generating credentials from an existing user does not check for or create that user.
{: tip}
