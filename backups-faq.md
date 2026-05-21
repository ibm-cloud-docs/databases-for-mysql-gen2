---

copyright:
  years: 2026
lastupdated: "2026-05-21"

keywords: mysql backup, backups, xtrabackup, corrupted_table, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Backing up {{site.data.keyword.databases-for-mysql}} FAQ
{: #faq-mysql-backups}
{: faq}
{: support}

[Gen 2]{: tag-purple}

Backups for {{site.data.keyword.databases-for-mysql}} Gen 2 deployments are accessible from the Backups tab of your deployment's dashboard.

## Gen 2 Backup Features
{: #gen2-backup-features}
{: faq}

{{site.data.keyword.databases-for-mysql}} Gen 2 uses snapshot-based backups with the following features:

- **Snapshot-based backups:** Daily automated VPC snapshots to IBM Cloud File Storage
- **Retention:** 30-day retention at GA (programmable retention periods available in Q3 hardening)
- **Backup windows:** Programmable backup windows to minimize performance impact (available in Q3 hardening)
- **Cross-region replication:** Backups support cross-region replication with AES-256 encryption
- **BYOK support:** Bring-your-own-key (BYOK) encryption via Key Protect for backup encryption
- **Point-in-Time Recovery (PITR):** Available in Q3 hardening for granular recovery

All backups are encrypted with AES-256 encryption and stored in {{site.data.keyword.cos_full_notm}}.

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
