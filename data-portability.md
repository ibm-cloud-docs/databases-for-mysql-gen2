---

copyright:
  years: 2026
lastupdated: "2026-05-21"

keywords: data export, portability, mysqldump, mydumper, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}



# Understanding data portability for {{site.data.keyword.databases-for-mysql}}
{: #data-portability}

[Gen 2]{: tag-purple}

[Data Portability](#x2113280){: term} involves a set of tools, and procedures that enable customers to export the digital artifacts that would be needed to implement similar workload and data processing on different service providers or on-prem software. It includes procedures for copying and storing the service customer's content, including the related configuration used by the service to store and process the data, on customer's own location.
{: shortdesc}

## Responsibilities
{: #data-portability-responsibilities}

IBM Cloud services provide interfaces and instructions to guide the customer to copy and store the service customer content, including the related configuration, on their own selected location.

You are responsible for the use of the exported data and configuration for the purpose of data portability to other infrastructures. This can involve the following:

- Planning and execution for setting up alternate infrastructure on on different cloud providers or on-prem software that provide similar capabilities to the IBM services.
- Planning and execution for the porting of the required application code on the alternate infrastructure, including the adaptation of customer's application code, and deployment automation.
- Conversion of the exported data and configuration to format required by the alternate infrastructure and adapted applications.

For more information about your responsibilities when using {{site.data.keyword.databases-for-mysql_full}}, see [Shared responsibilities for {{site.data.keyword.databases-for-mysql}}](/docs/cloud-databases?topic=cloud-databases-responsibilities-cloud-databases).

## Data export procedures
{: #data-portability-procedures}

{{site.data.keyword.databases-for-mysql}} provides mechanisms to export your content that has been uploaded, stored, and processed using the service.

### Exporting data from {{site.data.keyword.databases-for-mysql}}
{: #data-portability-exporting-data}

Two options exist to migrate data from {{site.data.keyword.databases-for-mysql_full}}. We recommend  `mysqldump` and `mydumper`. The best tool for you depends on certain conditions, including network connection, the size of your data set, and intermediate schema needs.

#### Before you begin
{: #data-portability-exporting-before-begin}

Before you start your data migration, install MySQL locally, so you have the `mysql` and `mysqldump` tools.

[MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html){: .external} also provides a graphical tool for working with MySQL servers and databases.

#### `mysqldump`
{: #data-portability-exporting-mysqldump}

This native MySQL client utility installs by default and can perform logical backups, reproducing table structure and data, without copying the actual data files. `mysqldump` dumps one or more MySQL databases for backup or transfer to another MySQL server. For more information, see the [mysqldump documentation](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html){: .external}.

Use `mysqldump` under the following conditions:

- The data set is smaller than 10 GB.
- Migration time is not critical, and the cost of retrying the migration is low.
- You donâ€™t need to do any intermediate schema or data transformations.

Don't use `mysqldump` if any of the following conditions are met:

- Your data set is larger than 10 GB.
- The network connection between the source and target databases is unstable or slow.

Follow these steps::

Run `mysqldump` on your source database to create an SQL file, which can be used to re-create the database. At a minimum, migrating `mysql` using the CLI requires the following arguments:

- Hostname (`-h` flag)
- Port number (`-P` flag)
- Username (`-u` flag)
- [--ssl-mode=VERIFY_IDENTITY](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-mode){: .external} (clients require an encrypted connection and perform verification against the server CA certificate and against the server hostname in its certificate).
- [--ssl-ca](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-ca){: .external} (the path name of the Certificate Authority (CA) file, which can be found within the Endpoints CLI tab of the *Overview* page in the UI).
- database name
- result file (`-r` flag)

Your CLI command looks like this:

```sh
mysqldump -h <host_name> -P <port_number> -u <user_name> --ssl-mode=VERIFY_IDENTITY --ssl-ca=mysql.crt --set-gtid-purged=OFF -p <database_name> -r dump.sql
```
{: pre}

To generate a log file of the `mysqldump` job that tracks errors while it's running, use a command like this:

```sh
mysqldump -h <host_name> -P <port_number> -u <user_name> --log-error=error.log --ssl-mode=VERIFY_IDENTITY --ssl-ca=mysql.crt --set-gtid-purged=OFF -p ibmclouddb -r dump.sql
```
{: pre}

You can do the same while importing, for example:

```sh
mysql -h <host_name> -P <port_number> -u admin --ssl-mode=VERIFY_IDENTITY --ssl-ca=mysql.crt -p ibmclouddb < dump.sql > import_logfile.log
```
{: pre}

The `mysql` command has many options. For more information, see [the mysqldump documentation](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html#mysqldump-syntax){: .external} and [command reference](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html#mysqldump-option-summary){: .external}.

#### mydumper
{: #data-portability-exporting--mydumper}

`mydumper` and its paired logical backup tool `myloader` use multithreading capabilities to perform data migration similarly to `mysqldump`. However, `mydumper` provides many improvements, such as parallel backups, consistent reads, and easier to manage output. Parallelism allows for better performance during both the import and export process, while output can be easier to manage because individual tables get dumped into separate files.

Use `mydumper` under the following conditions:

- The data set is larger than 10 GB.
- The network connection between source and target databases is fast and stable.
- You need to do intermediate schema or data transformations.

Don't use `mydumper` if any of the following conditions are met:

- Your data set is smaller than 10 GB.
- The network connection between the source and target databases is unstable or very slow.

Before you begin exporting your data with `mydumper`, see the [mydumper project](https://github.com/maxbube/mydumper){: .external} for details and step-by-step instructions on installation and necessary developer environment.

Next, refer to [How to use mydumper](https://github.com/mydumper/mydumper#how-to-use-mydumper){: .external} page for information about using the `mydumper` and `myloader` tools to perform full data migration.

## Exported data formats
{: #data-portability-data-formats}

The exported data is in SQL format and can be imported into any other MySQL instance using the `mysql` command. Perform imports with the admin user. For more information, see the [mysqldump documentation](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html){: .external}.

## Data ownership
{: #data-ownership}

All exported data are classified as Customer content and therefore apply to them the full customer ownership and licensing rights, as stated in [IBM Cloud Service Agreement](https://www.ibm.com/terms/?id=Z126-6304_WS).
