---

copyright:
  years: 2023, 2026
lastupdated: "2026-04-01"

keywords: mysql backup, backups, xtrabackup, corrupted_table

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Backing up {{site.data.keyword.databases-for-mysql-gen2}} FAQ
{: #faq-mysql-backups}
{: faq}
{: support}

Backups for {{site.data.keyword.databases-for-mysql-gen2}} deployments are accessible from the Backups tab of your deployment's dashboard.

## Optimizing `xtrabackup` table
{: #optimize-xtrabackup-table}
{: faq}

MySQL version 8.0.29 contained a design flaw that can cause data corruption for tables with `INSTANT ADD/DROP COLUMNS`. The issues in MySQL 8.0.29 make this version unsafe to take backups. If Xtrabackup detects tables with instant add/drop columns, you see an error message like this:

```text
[ERROR] [MY-011825] [Xtrabackup] Tables found:
2023-03-03T08:09:34.643290-00:00 0 [ERROR] [MY-011825] [Xtrabackup] corrupted_table
2023-03-03T08:09:34.643300-00:00 0 [ERROR] [MY-011825] [Xtrabackup]
Please run OPTIMIZE TABLE or ALTER TABLE ALGORITHM=COPY on all listed tables to fix this issue.
```

This error can be seen using [Activity Tracker](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-at_events&interface=ui) or [Log Analysis](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-logging&interface=ui).
{: tip}

### Resolving `corrupted_table` error
{: #resolve-corrupted-table-error}
{: faq}

To resolve the `corrupted_table` error, query and optimize the `Xtrabackup` table using a command like:

```sh
SELECT NAME FROM INFORMATION_SCHEMA.INNODB_TABLES WHERE TOTAL_ROW_VERSIONS > 0;
```
{: pre}

If the results are a list of tables, run `OPTIMIZE TABLE` on the list before taking a backup.

For more infortmation, see [Error Message: Found tables with row versions due to INSTANT ADD/DROP columns](https://docs.percona.com/percona-xtrabackup/8.0/error-message-instant.html){: external}.
