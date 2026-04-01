---

copyright:
  years: 2024, 2026
lastupdated: "2026-04-01"

keywords: best practices

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Best practices for {{site.data.keyword.databases-for-mysql-gen2}}
{: #best-practices}

| Best practice | Notes |
| --- | --- |
| Set the correct value for `max_allowed_packet`. | In replication setups where large transactions or binlogs exceed the limit, breaching of `max_allowed_packet` size can cause replica entering a failed copy state and cause risk of locking the primary. Using BLOB or VARTEXT columns to store file content in MySQL columns can cause issues with replication and restores of backups. It is recommended that you configure the max_allowed_packet to the maximum value, if you load data of varying lengths into any column. In addition, don't use multiple wide LONGBLOB columns in a specific table, since this can lead to situations where row size exceeds the maximum allowable, and restores using point-in-time may not work.|
| Implement primary keys in tables with large row counts. | Adding a primary key to any table > 5K rows will greatly optimize replication and prevent excessive lags. Defining a primary key for any table for which DELETE or UPDATE operations of over 5K rows are performed in single statements is recommended. If you cannot add a primary key, DELETE in batches of < 5K rows, with commits per batch.|
| Favour DROP TABLE and TRUNCATE TABLE over whole table DELETE when possible. | Replication logs every row deletion, which can significantly slow down the process. Chunking deletes and updates is recommended. Be aware that DROP, CREATE, and TRUNCATE are DDL statements and they do not complete during a backup but wait until the backup operation is complete. The log entries show warning messages for the time during the backup where these statements will block. You can estimate your backup times by inspecting the disk used panel of the monitoring dashboard. As a rule of thumb, 500 GB of data takes approximately 90 minutes to backup.|
| Minimize single statement multi-row operations such as UPDATE JOIN or DELETE, using WHERE range clauses to minimize the rows touched count. | This practice can improve query performance and reduce lock contention. |
| Use connection pooling in the application end. | Pooling reuses connections, avoiding maxing out of connections. Ensure that your pool size is well below the database's `max_connections`. Implement retry logic and catch exceptions to handle pool exhaustion or connection failures.|
| Reduce blast radius by using a dedicated ICD-MySQL instance per production application. | Separation of applications lowers the volume per service instance, minimizing issues caused by aggregation of demand from many applications. |
| Deploy a MySQL read replica per MySQL instance. | Read replicas offer the ability to support read traffic off the main instance, reducing the overall workload, and enhancing reliability.|
| Hosting MySQL on a dedicated instance | For production environments, particularly those anticipating growth, increased traffic, or requiring high reliability and performance, we strongly recommend hosting MySQL on a dedicated instance. This ensures better resource isolation, scalability, and overall system stability.|
{: caption="Best practices" caption-side="top"}
