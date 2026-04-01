---

copyright:
  years: 2021, 2023
lastupdated: "2023-08-02"

keywords: mysql, databases, connection limits, terminating connections, connection pooling, mysql connections, mysql connection pooling, managing connections

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Managing MySQL Connections
{: #managing-mysql-connections}

Connections to your {{site.data.keyword.databases-for-mysql-gen2_full}} deployment use resources, so consider how many connections you need when tuning your deployment's performance. MySQL uses a `max_connections` setting to limit the number of connections (and resources that are used by connections) to prevent runaway connection behavior from overwhelming your deployment's resources.

You can check the value of `max_connections` with your [admin user](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-user-management#the-admin-user) and [`mysql`](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connecting-mysql). Check the `max_connections` with your `admin` user using a command like:

```sh
ibmclouddb=> SHOW max_connections;
 max_connections
-----------------
 200
(1 row)
```
{: .codeblock}

## How to calculate the MySQL `max_connections` Variable
{: #managing-mysql-connections-calculate-max-connections}

`max_connections` is a configuration parameter in MySQL that determines the maximum number of concurrent connections that can be established with the database server.

### MySQL `max_connections` basic formula
{: #managing-mysql-connections-calculate-max-connections-formula}

The basic formula for calculating `max_connections` is: 

```text
Available RAM = Global Buffers + (Thread Buffers x `max_connections`)
```

To retrieve a list of buffers and their values, use a command like:

```sh
SHOW VARIABLES LIKE '%buffer%';
```
{: pre}

## MySQL Connection Limits 
{: #managing-mysql-connection-limits}

At provision, {{site.data.keyword.databases-for-mysql-gen2}} sets the maximum number of connections to your MySQL database to **200**. Raise this value by [Changing the MySQL Configuration](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-changing-configuration).

Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. 

Limit the number of simultaneous connections for any nonadmin account. For example, setting `max_user_connections=3` restricts the user account to a maximum of three simultaneous connections. 
{: .tip}

Exceeding the connection limit for your deployment will negatively affect the health of your database and cause it to be unreachable by your applications. After the connection limit is reached, any attempts at starting a new connection result in an error.
{: .important}

```sh
FATAL: remaining connection slots are reserved for
non-replication superuser connections
```

Access information about connections to your deployment with the `admin` user, `mysql`, and `SHOW GLOBAL STATUS`.

```sh
mysql> SHOW GLOBAL STATUS;
+-----------------------------------+------------+
| Variable_name                     | Value      |
+-----------------------------------+------------+
| Aborted_clients                   | 0          |
| Aborted_connects                  | 0          |
| Bytes_received                    | 155372598  |
| Bytes_sent                        | 1176560426 |
...
| Connections                       | 30023      |
| Created_tmp_disk_tables           | 0          |
| Created_tmp_files                 | 3          |
| Created_tmp_tables                | 2          |
...
| Threads_created                   | 217        |
| Threads_running                   | 88         |
| Uptime                            | 1389872    |
+-----------------------------------+------------+
```

To determine where the connections are going, break down the connections by database with the help of the `threads_connected` variable, using a command like:

``` sh
mysql> show status where `variable_name` = 'Threads_connected';
```
{: pre}

To further investigate your connections, use the `SHOW PROCESSLIST` command:

```sh
SHOW [FULL] PROCESSLIST;
```
{: pre}

## Ending MySQL Connections
{: #managing-mysql-connections-terminating}

Each connection to [mysqld](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html){: .external}, the MySQL Server, runs in a separate thread and can be stopped with a `processlist_id` statement, using a command like:

```sh
KILL [CONNECTION | QUERY] processlist_id
```
{: pre}

- `KILL CONNECTION` ends the connection that is associated with the `processlist_id`, after stopping any statement that the connection is running. 
- `KILL QUERY` ends the statement the connection is running, but leaves the connection itself intact.

For more information, see the [MySQL Reference Manual KILL Statement](https://dev.mysql.com/doc/refman/8.0/en/kill.html){: .external}.


### End MySQL Connections
{: #managing-mysql-connections-end}

If your deployment reaches the connection limit or you are having trouble connecting to your deployment and suspect that a high number of connections is a problem, disconnect (or end) all of the connections to your deployment. 

In the UI, on the _Settings_ tab, there is a button to `End Connections` to your deployment. Use caution, as it disrupts anything that is connected to your deployment.

In the CLI, end connections to the deployment using a command like:

```sh
ibmcloud cdb deployment-kill-connections <deployment name or CRN>
```

You can also use the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api#kill-connections-to-a-MySql-deployment) to perform the end all connections operation.

## MySQL Connection Pooling
{: #managing-mysql-connection-pooling}

One way to prevent exceeding the connection limit and ensure that connections from your applications are being handled efficiently is through connection pooling.

[MySQL Connectors](https://dev.mysql.com/doc/refman/8.0/en/connectors-apis.html){: .external} enable you to connect and run MySQL statements from another language or environment, including ODBC, Java (JDBC), C++, Python, PHP, Perl, Ruby, and native C and embedded MySQL instances.

For example, connection pooling with [MySQL Connector/J](https://dev.mysql.com/doc/refman/8.0/en/connector-j-info.html){: .external} can increase performance while reducing overall usage. [MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/connector-python-connection-pooling.html){: .external} also allows for optimization by using the `mysql.connector.pooling` module.
