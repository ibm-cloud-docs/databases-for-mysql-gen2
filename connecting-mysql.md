---
copyright:
  years: 2026
lastupdated: "2026-06-22"

keywords: mysql, databases, mysql connection strings, connecting mysql, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Connecting with `mysql`
{: #connecting-mysql}

[Gen 2]{: tag-purple}

You can access your MySQL database directly from its command-line client, `mysql`. You can use `mysql` for direct interaction and monitoring of the data structures that are created within the database. It is also useful for testing and monitoring the queries and performance, installing and modifying scripts, and other management activities.

Set the admin password before you use it to connect to the database. For more information, see [Setting the Admin Password](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-user-management&interface=ui#user-management-set-admin-password-ui).
{: .tip}

## Installing `mysql`
{: #installing-mysql}

Install the command-line client for MySQL, `mysql`. To use `mysql`, the MySQL client tools need to be installed on the local system. They can be installed with the full MySQL package that is provided from [mysql.com](https://www.mysql.com/downloads/), or as a [package from your operating system's package manager](https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/){: .external}.

For more information about `mysql`, see the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/){: .external}.

## `mysql` Connection Strings
{: #mysql-conn-strings}

Connection strings are displayed in the _Endpoints_ panel of your deployment's _Overview_, and can also be retrieved from the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-connections), and the [API](https://{DomainName}/apidocs/cloud-databases-api#discover-connection-information-for-a-deployment-f-e81026).

The information that you need to make a connection with `mysql` is in the "cli" section of your connection strings. The table contains a breakdown for reference.

| Field name | Index | Description |
| ---------- | ----- | ----------- |
| `Bin` | | The recommended binary to create a connection; in this case, it is `mysql`. |
| `Composed` | | A formatted command to establish a connection to your deployment. The command combines the `Bin` executable file, `Environment` variable settings, and uses `Arguments` as command-line parameters. |
| `Environment`| | A list of key/values you set as environment variables. |
| `Arguments`| 0... | The information that is passed as arguments to the command shown in the Bin field. |
| `Certificate`| Base64 | A service proprietary certificate that is used to confirm that an application is connecting to the appropriate server. It is base64 encoded. |
| `Certificate`| Name | The allocated name for the service proprietary certificate. |
| `Type` | | The type of package that uses this connection information; in this case `cli`. |
{: caption="mysql/cli connection information" caption-side="top"}

* `0...` indicates that there might be one or more of these entries in an array.

## Creating a command-line client connection
{: #create-cli-connection}

Before creating a command-line client connection, ensure that you have the username and password for your instance. You need the username and password that were returned when you created a Manager or Writer user by using service credentials.

Run the following command:

```
export MYSQLUSER=<username>
export MYSQLPASSWORD=<password>
```
{: pre}

Then, execute the following command:

```
ibmcloud resource service-instance <instance name or instance id> -o json
```
{: pre}

Look for the mysql connection string in the output. Alternatively, if you have `jq` installed, run the following command:

```
ibmcloud resource service-instance <instance name or instance id> -o json | jq '.[0].extensions.dataservices.connection.cli.composed[0]'

```
{: pre}

The connection string has the following format:

```sh
"mysql --tls-sni-servername=<instance>.<subdomain>.appdomain.cloud -h <instance>.<subdomain>.appdomain.cloud -P 3306 -u $MYSQLUSER -p$MYSQLPASSWORD --ssl-mode=REQUIRED"
```
{: pre}

You can only connect from within a VPE that is configured for your MySQL instance.
{: note}
