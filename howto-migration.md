---
copyright:
  years: 2021, 2022
lastupdated: "2022-07-19"

keywords: mysql, databases, migrating, mysqldump, mydumper, mysql migration

subcollection: databases-for-mysql-gen2

---

{:external: .external target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Migrating to {{site.data.keyword.databases-for-mysql-gen2}}
{: #migrating}

Two options exist to migrate data from existing MySQL databases to {{site.data.keyword.databases-for-mysql-gen2_full}}. We recommend two options: `mysqldump` and `mydumper`. The best tool for you depends on certain conditions, including network connection, the size of your data set, and intermediate schema needs. 

## Before you begin
{: #migrating-before-begin}

Before starting your data migration, you need MySQL installed locally so you have the `mysql` and `mysqldump` tools. 

[MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html){: .external} also provides a graphical tool for working with MySQL servers and databases. While not strictly required, the {{site.data.keyword.databases-for}} [CLI](/docs/databases-cli-plugin) also makes it easy to connect and restore to a new {{site.data.keyword.databases-for-mysql-gen2}} deployment. 

## `mysqldump`
{: #migrating-mysqldump}

This native MySQL client utility installs by default and can perform logical backups, reproducing table structure and data, without copying the actual data files. mysqldump dumps one or more MySQL databases for backup or transfer to another MySQL server. For more information, see the [mysqldump documentation](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html){: .external}.

`mysqldump` is appropriate to use under the following conditions:
- The data set is smaller than 10 GB. 
- Migration time is not critical, and the cost of retrying the migration is low.
- You don’t need to do any intermediate schema or data transformations.

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

Your CLI command looks like this
```sh
mysqldump -h <host_name> -P <port_number> -u <user_name> --ssl-mode=VERIFY_IDENTITY --ssl-ca=mysql.crt --set-gtid-purged=OFF -p <database_name> -r dump.sql
```
{: pre}

To generate a log file of the mysqldump job that tracks errors while it's running, use a command like this
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

The resulting output of `mysqldump` can then be uploaded into a new {{site.data.keyword.databases-for-mysql-gen2}} deployment. As the output is SQL, it can simply be sent to the database through the `mysql` command. We recommend that imports be performed with the admin user. 

See the [Connecting with `mysql`](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connecting-mysql) documentation for details on connecting as admin by using `mysql`. To connect with the `mysql` command, you need the admin user's connection string and the TLS certificate, which can both be found in the UI. The certificate needs to be decoded from the base64 and stored as an arbitrary local file. To import the previously created `dump.sql` into a database deployment named `example-mysql`, the `mysql` command can be called with `-f dump.sql` as a parameter. The parameter tells `mysql` to read and run the SQL statements in the file. 

As noted in the [Connecting with `mysql`](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connecting-mysql) documentation, the {{site.data.keyword.databases-for}} CLI plug-in simplifies connecting. The previous `mysql` import can be run using a command like: 

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

## Tuning InnoDB Configurable Variables
{: #migrating-config-variables}

You can configure the following MySQL InnoDB options to tune performance, based on machine capacity and database workload. 

- [innodb_buffer_pool_size_percentage](#migrating-config-variables_buffer_pool)
- [innodb_flush_log_at_trx_commit](#migrating-config-variables-flush-log)
- [innodb_log_buffer_size](#migrating-config-variables-log-buffer-size)
- [innodb_log_file_size](#migrating-config-variables-log-file-size)
- [innodb_lru_scan_depth](#migrating-config-variables-lru-scan-depth)
- [innodb_read_io_threads](#migrating-config-variables-read-io-threads)
- [innodb_write_io_threads](#migrating-config-variables-write-io-threads)
- [net_read_timeout](#migrating-config-variables-net-read-timeout)
- [net_write_timeout](#migrating-config-variables-net-write-timeout)


### innodb_buffer_pool_size_percentage
{: #migrating-config-variables_buffer_pool}

- Description: The `innodb_buffer_pool_size_percentage value` parameter defines your database container's dedicated memory amount.
- Default setting: 50
- Max: 100
- Min: 10
- Requires restart: True

 As your database itself uses a given amount of memory, if the `innodb_buffer_pool_size_percentage value` parameter is configured too high, then your database memory requirements + `innodb_buffer_pool_size_percentage` can become higher than available memory limits, resulting in an out of memory state (OOM).

The `innodb_buffer_pool_size_percentage` parameter value differs based on the size of your database. The default value is `50%`, which is safe for databases of all sizes. Configure the value as needed; if you encounter OOM then the value is set too high and you should lower it. 

### innodb_flush_log_at_trx_commit
{: #migrating-config-variables-flush-log}

- Description: Controls the balance between strict ACID compliance for commit operations and higher performance

- Default setting: 2

The default setting of 2 is not fully ACID-compliant (Default setting of 1 is required for full ACID compliance), but it is more performant and still is safe.
{: .note}

- Max: 2
- Min: 0
- Requires restart: False

For more information, see [MySQL innodb_flush_log_at_trx_commit documentation](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit){: .external}.

### innodb_log_buffer_size
{: #migrating-config-variables-log-buffer-size}

- Description: The size in bytes of the buffer that InnoDB uses to write to the log files on disk.
- Default setting: 32 MiB
- Max: 4294967295
- Min: 1048576
- Requires restart: True

For more information, see [MySQL innodb_log_buffer_size documentation](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit){: .external}.

### innodb_log_file_size
{: #migrating-config-variables-log-file-size}

- Description: InnoDB log file size in bytes.
- Default setting: 64 MB
- Max: 274877906900
- Min: 4194304
- Requires restart: True

For more information, see [MySQL innodb_log_file_size documentation](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_log_file_size){: .external}.

### innodb_lru_scan_depth
{: #migrating-config-variables-lru-scan-depth}

- Description: A parameter that influences the algorithms and heuristics for the flush operation for the InnoDB buffer pool.
- Default setting: 1024
- Max: no max value
- Min: 100
- Requires restart: True

For more information, see [MySQL innodb_lru_scan_depth documentation](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_lru_scan_depth){: .external}.

### innodb_read_io_threads
{: #migrating-config-variables-read-io-threads}

- Description: The number of I/O threads for read operations in InnoDB.
- Default setting: 4
- Max: 64
- Min: 1
- Requires restart: True

For more information, see [MySQL innodb_read_io_threads documentation](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_read_io_threads){: .external}.

### innodb_write_io_threads
{: #migrating-config-variables-write-io-threads}

- Description: The number of I/O threads for read operations in InnoDB.
- Default setting: 4
- Max: 64
- Min: 1
- Requires restart: True

For more information, see [MySQL innodb_write_io_threads documentation](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_write_io_threads){: .external}.

### net_read_timeout
{: #migrating-config-variables-net-read-timeout}

- Description: The number of seconds to wait for more data from a connection before aborting the read.
- Default setting: 30 
- Max: 
- Min: 1
- Requires restart: True

For more information, see [MySQL net_read_timeout documentation](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_net_read_timeout){: .external}.

### net_write_timeout
{: #migrating-config-variables-net-write-timeout}

- Description: The number of seconds to wait for a block to be written to a connection before aborting the write.
- Default setting: 60
- Max: 
- Min: 1
- Requires restart: True

For more information, see [`net_write_timeout` documentation](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_net_write_timeout){: .external}.
