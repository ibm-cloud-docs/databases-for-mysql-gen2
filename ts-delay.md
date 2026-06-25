---

copyright:
  years: 2026
lastupdated: "2026-06-25"

keywords: troubleshooting MySQL, delay mysql, mysql configurable variables, gen2

subcollection: databases-for-mysql-gen2

content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}

# Why is there a delay when restoring my {{site.data.keyword.databases-for-mysql_full}} deployment?
{: #troubleshoot-delay}
{: troubleshoot}
{: support}

[Gen 2]{: tag-purple}

You're experiencing a long delay when restoring your {{site.data.keyword.databases-for-mysql_full}} deployment.
{: tsSymptoms}

Restoring a backup can be delayed if your configurable variables aren't optimized, or if you aren't using the appropriate tools.
{: tsCauses}

- Check your configurable variables at [Migrating to Databases for MySQL](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-migrate-data-center).
- You can restore a backup using `mysqldump` or `mydumper`. `mysqldump`, the native MySQL backup client, is effective for databases smaller than 10 GB, whereas `mydumper` is effective with larger databases. Use the tool that best suits your needs.
{: tsResolve}
