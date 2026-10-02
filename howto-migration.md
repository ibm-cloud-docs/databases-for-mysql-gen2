---
copyright:
  years: 2026
lastupdated: "2026-09-30"

keywords: mysql, databases, migrating, mysqldump, mydumper, mysql migration, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Migrating to {{site.data.keyword.databases-for-mysql}}
{: #migrating}

[Gen 2]{: tag-purple}

{{site.data.keyword.attribute-definition-list}}

Two options exist to migrate data from existing MySQL databases to {{site.data.keyword.databases-for-mysql_full}}. We recommend two options: `mysqldump` and `mydumper`. The best tool for you depends on certain conditions, including network connection, the size of your data set, and intermediate schema needs.

## Before you begin
{: #migrating-before-begin}

Before starting your data migration, you need MySQL installed locally so you have the `mysql` and `mysqldump` tools.

[MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html){: .external} also provides a graphical tool for working with MySQL servers and databases. While not strictly required, the {{site.data.keyword.databases-for}} [CLI](/docs/databases-cli-plugin) also makes it easy to connect and restore to a new {{site.data.keyword.databases-for-mysql}} deployment. 

## `mysqldump`
{: #migrating-mysqldump}

This native MySQL client utility installs by default and can perform logical backups, reproducing table structure and data, without copying the actual data files. mysqldump dumps one or more MySQL databases for backup or transfer to another MySQL server. For more information, see the [mysqldump documentation](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html){: .external}.

`mysqldump` is appropriate to use under the following conditions:

- The data set is smaller than 10 GB.
- Migration time is not critical, and the cost of retrying the migration is low.
- You don't need to do any intermediate schema or data transformations.

We don't recommend mysqldump if any of the following conditions are met:

- Your data set is larger than 10 GB. 
- The network connection between the source and target databases is unstable or slow.

Follow these steps by using the `mysqldump` tool:

Run `mysqldump` on your source database to create an SQL file, which can be used to re-create the database. At a minimum, migrating `mysql` using the CLI requires the following arguments:

- Hostname (`-h` flag)
- Port number (`-P` flag)
- Username (`-u` flag) 
- [--ssl-mode=VERIFY_IDENTITY](https://dev.mysql.com/doc/refman/5.7/en/connection-options.html#option_general_ssl-mode){: .external} (clients require an encrypted connection and perform verification against the server CA certificate and against the server hostname in its certificate)
- [--ssl-ca](https://dev.mysql.com/doc/refman/5.7/en/connection-options.html#option_general_ssl-ca){: .external} (the path name of the Certificate Authority (CA) file, which can be found within the Endpoints CLI tab of the *Overview* page in the UI.)
- database name
- result file (`-r` flag) 

Your CLI command looks like this:

```sh
mysqldump -h <host_name> -P <port_number> -u <user_name> --ssl-mode=VERIFY_IDENTITY --ssl-ca=mysql.crt --set-gtid-purged=OFF -p <database_name> -r dump.sql
```
{: pre}

To generate a log file of the mysqldump job that tracks errors while it's running, use a command like this:

```sh
mysqldump -h <host_name> -P <port_number> -u <user_name> --log-error=error.log --ssl-mode=VERIFY_IDENTITY --ssl-ca=mysql.crt --set-gtid-purged=OFF -p ibmclouddb -r dump.sql 
```
{: pre}


The same can be done while importing, for example 
```sh
mysql -h <host_name> -P <port_number> -u admin --ssl-mode=VERIFY_IDENTITY --ssl-ca=mysql.crt -p ibmclouddb < dump.sql > import_logfile.log
```
{: pre}

For more information on using MySQL Replication with Global Transaction Identifiers (GTIDs), see the [Using GTIDs for Failover and Scaleout](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-failover.html){: .external} in the MySQL Reference Manual.
{: .note} 

The `mysql` command has many options; [see the official documentation](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#mysqldump-syntax){: .external} and [command reference](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#mysqldump-option-summary){: .external} for a fuller view of its capabilities.

### Restoring mysqldump's output
{: #migrating-mysqldump-restore}

The resulting output of `mysqldump` can then be uploaded into a new {{site.data.keyword.databases-for-mysql}} deployment. As the output is SQL, it can simply be sent to the database through the `mysql` command. We recommend that imports be performed with the admin user. 

See the [Connecting with `mysql`](/docs/databases-for-mysql?topic=databases-for-mysql-connecting-mysql) documentation for details on connecting as admin by using `mysql`. To connect with the `mysql` command, you need the admin user's connection string and the TLS certificate, which can both be found in the UI. The certificate needs to be decoded from the base64 and stored as an arbitrary local file. To import the previously created `dump.sql` into a database deployment named `example-mysql`, the `mysql` command can be called with `-f dump.sql` as a parameter. The parameter tells `mysql` to read and run the SQL statements in the file. 

As noted in the [Connecting with `mysql`](/docs/databases-for-mysql?topic=databases-for-mysql-connecting-mysql) documentation, the {{site.data.keyword.databases-for}} CLI plug-in simplifies connecting. The previous `mysql` import can be run using a command like: 

```sh
mysql -h <host_name> -P <port_number> -u admin --ssl-mode=VERIFY_IDENTITY --ssl-ca=mysql.crt -p ibmclouddb < dump.sql
```
{: pre}

If no user is specified, the command automatically uses the admin user and interactively prompts for the password. The TLS certificate is automatically retrieved and used.

While the restore process is running, a number of messages are emitted regarding changes being made to the database deployment.

## mydumper
{: #migrating-mydumper}

mydumper, and its paired logical backup tool myloader, use multithreading capabilities to perform data migration similarly to mysqldump; however, mydumper provides many improvements such as parallel backups, consistent reads, and easier to manage output. Parallelism allows for better performance during both the import and export process, while output can be easier to manage because individual tables get dumped into separate files. 

mydumper is appropriate to use under the following conditions:
- The data set is larger than 10 GB.

- The network connection between source and target databases is fast and stable.
- You need to do intermediate schema or data transformations.

We don't recommend using mydumper if any of the following conditions are met:

- Your data set is smaller than 10 GB. 
- The network connection between the source and target databases is unstable or very slow.

Before you begin migrating your data with mydumper, first see the [mydumper project](https://github.com/maxbube/mydumper){: .external} for details and step-by-step instructions on installation and necessary developer environment, 

Next, refer to the [How to use mydumper](https://github.com/mydumper/mydumper#how-to-use-mydumper){: .external} page for information on using the mydumper and myloader tools to perform full data migration.

### Restoring with myloader
{: #migrating-mydumper-restore}

`myloader` is the paired restore tool for `mydumper` and is used to import the output directory that `mydumper` produces back into a {{site.data.keyword.databases-for-mysql}} deployment. Because `myloader` supports multithreaded restores, it can significantly reduce the time required to reload large data sets compared to single-threaded alternatives.

We recommend that restore operations be performed with the admin user.
{: .note}

At a minimum, restoring with `myloader` requires the following arguments:

- Hostname (`-h` flag)
- Port number (`-P` flag)
- Username (`-u` flag)
- Password prompt (`-p` flag)
- SSL CA certificate (`--ssl-ca` flag; the path to the Certificate Authority (CA) file, which can be found within the Endpoints CLI tab of the *Overview* page in the UI.)
- Input directory (`-d` flag; the output directory created by `mydumper`)
- Thread count (`--threads` flag)

Your `myloader` CLI command looks like this:

```sh
myloader -h <host_name> -P <port_number> -u admin -p --ssl-ca=mysql.crt -d <mydumper_output_dir> --threads=4
```
{: pre}

For full flag documentation and advanced options, see the [mydumper project](https://github.com/mydumper/mydumper){: .external}. After the database is restored, it's always recommended to validate the data consistency between the source and the target databases.
