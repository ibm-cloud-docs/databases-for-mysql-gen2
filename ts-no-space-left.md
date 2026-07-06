---

copyright:
  years: 2026
lastupdated: "2026-06-26"

keywords: troubleshooting for MySQL, mysql max_connections, mysql max connections, mysql connection pooling, mysql connection pooling, disk space, scaling considerations

subcollection: databases-for-mysql-gen2

content-type: troubleshoot

---

 {{site.data.keyword.attribute-definition-list}}

# How do I scale disk to get my {{site.data.keyword.databases-for-mysql}} deployment back online?
{: #troubleshoot-no-space-left}
{: troubleshoot}
{: support}

[Gen 2]{: tag-purple}

If you encounter a `No space left on device` error for your {{site.data.keyword.databases-for-mysql_full}} deployment, review these solutions.
{: shortdesc}

Once disk utilization reaches the 90% threshold, the database enters a read-only state and produces a `[Errno 28] No space left on device` error message.
{: tsSymptoms}

Review the following information to troubleshoot and resolve your `No space left on device` issues:
{: tsResolve}

* [Scale up the disk](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-resources-scaling&interface=ui) to get your instance back online.
   Scaling is a potentially long-running operation. For more information, see [Scaling considerations](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-resources-scaling&interface=ui#resources-scaling-consider).

* To prevent an issue with disk space, set up [monitoring](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-monitoring).
