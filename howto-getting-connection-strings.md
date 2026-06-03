---

copyright:
  years: 2026
lastupdated: "2026-06-03"

keywords: mysql, databases, connection strings, gen 2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}


# Getting connection strings
{: #connection-strings}

[Gen 2]{: tag-purple}

Connection strings allow you to establish a connection between your application and your {{site.data.keyword.databases-for-mysql}} instance.

## Getting connection strings in the UI
{: #connection-strings-ui}
{: ui}

Follow these steps to retrieve your {{site.data.keyword.databases-for-mysql}} instance connection strings:

1. In your deployment's **Overview** page, scroll down to the **Service endpoints** section.
1. The **Service endpoints** section displays tabs for available connection methods:
   - **MySQL** – Shows the connection string, hostname, port, database name, and SSL certificate information for your deployment.
   - **CLI** – Provides details for connecting by using the [{{site.data.keyword.IBM_notm}} CLI](https://www.ibm.com/cloud/cli){: external}.

## Getting connection strings in the CLI
{: #connection-strings-cli}
{: cli}

You can also retrieve connection strings by using the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference) Connection command.

The command looks like this:

```sh
ibmcloud resource service-instance <INSTANCE_NAME_OR_CRN>
```
{: pre}

For more information, see [Connections command options](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference).

{{site.data.keyword.databases-for-mysql}} deployments no longer include a default admin user. Instead, customers create a user with the 'Manager', 'Writer', or 'Reader' role by using the {{site.data.keyword.cloud}} service credential interface - through the UI or CLI. This provides a username (ibm_...), password, and connection string that can be used to connect to your deployment. If your deployment has only a private endpoint, you must specify `--endpoint-type private` or the commands return an error. The user and endpoint type is not enforced. You can use any user on your deployment with the private endpoint, which is the designated connection option.

To use the `ibmcloud cdb` CLI commands, you must [install the {{site.data.keyword.databases-for}} plug-in](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference).
{: .tip}


## Getting connection strings in the API
{: #connection-strings-api}
{: api}

To retrieve user connection strings from the [{{site.data.keyword.databases-for}} API](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-api){: external}, use the [Connections endpoint](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-api){: external}. To create the connection strings, ensure that the path includes the specific user and endpoint type that should be used. The `user` is not restricted or enforced. You have the flexibility to utilize any user available in your deployment.

The API command looks like:

```sh
curl -X GET 'https://resource-controller.cloud.ibm.com/v2/resource_instances/{id}' -H "Authorization: Bearer <IAM token>"
```
{: pre}

Remember to replace `{id}` with the appropriate value.
{: note}

## Connection string breakdown
{: #connection-string-breakdown}

### The MySQL section
{: #mysql-section}

The **MySQL** tab contains information that is suited to applications that make connections to MySQL.

| Field name | Index | Description |
| ---------- | ----- | ----------- |
| `Type` | | Type of connection - for MySQL, it is "URI". |
| `Scheme` | | Scheme for a URI - for MySQL, it is "MySQL". |
| `Path` | | Path for a URI - for MySQL, it is the database name. The default is `ibmclouddb`. |
| `Authentication` | `Username` | The username that you use to connect. |
| `Authentication` | `Password` | A password for the user - might be shown as `$PASSWORD`. |
| `Authentication` | `Method` | How authentication takes place; "direct" authentication is handled by the driver. |
| `Hosts` | `0...` | A hostname and port to connect to. |
| `Composed` | `0...` | A URI combining Scheme, Authentication, Host, and Path. |
{: caption="MySQL/URI connection information" caption-side="top"}

* `0...` indicates one or more of these entries in an array.
