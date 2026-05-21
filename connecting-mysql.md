---
copyright:
  years: 2026
lastupdated: "2026-05-21"

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

| Field Name | Index | Description |
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

## Connecting
{: #mysql-connecting}

The `ibmcloud cdb deployment-connections` command handles everything that is involved in creating a command-line client connection. For example, to connect to a deployment named "example-mysql", use the following command.

```sh
ibmcloud cdb deployment-connections example-mysql --start
```
{: pre}

Or
```sh
ibmcloud cdb cxn example-mysql -s
```
{: pre}

The command prompts for the admin password and then runs the `mysql` command-line client to connect to the database.

If you have not installed the cloud databases plug-in, connect to your MySQL databases by using `mysql` by giving it the "composed" connection string. It provides environment variables `MYSQL_PWD` and `--ssl-ca=<cert_name>`. Set `MYSQL_PWD` to the admin's password and `--ssl-ca=<cert_name>` to the path or file name for the service proprietary certificate.

```sh
MYSQL_PWD=$PASSWORD mysql --host=e4ad919f-59b6-4300-97c9-e099a5b6cf31.c5kmhkid0ujpmrucb800.databases.appdomain.cloud --port=32195 --user=$USERNAME --ssl-mode=VERIFY_IDENTITY --ssl-ca=52b78cf7-b17e-42aa-9e07-1fe4f741b286 ibmclouddb
```

## Using the service proprietary certificate
{: #mysql-using-ssc}

1. Copy the certificate information from the _Endpoints_ panel or the Base64 field of the connection information.
2. If needed, decode the Base64 string into text.
3. Save the certificate to a file. (You can use the Name that is provided or your own file name).
4. Provide the path to the certificate to the `--ssl-ca=<cert_name>` environment variable.

You can display the decoded certificate for your deployment with the CLI plug-in with the command:
```sh
ibmcloud cdb deployment-cacert "your-service-name"
```
{: pre}

It decodes the base64 into text. Copy and save the command's output to a file and provide the file's path to the `--ssl-ca=<cert_name>` environment variable.
