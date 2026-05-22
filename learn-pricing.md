---
copyright:
  years: 2026
lastupdated: "2026-05-22"

keywords: mysql, gen 2, pricing

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Pricing
{: #pricing}

[Gen 2]{: tag-purple}

A {{site.data.keyword.databases-for-mysql}} deployment consists of a highly available MySQL cluster with two data members, ensuring your data is replicated across both. Pricing is based on the total resources allocated to the deployment, such as disk storage, RAM, virtual CPU cores, and backup storage, calculated on an hourly prorated basis. Gen 2 instances require at least 10 GB of disk space, and the smallest configuration profile offers 4 vCPU cores.

## Storage architecture
{: #storage-architecture}

{{site.data.keyword.databases-for-mysql}} Gen 2 uses a shared storage layer across both data members. Unlike traditional per-member storage billing, this architecture means you are charged for a single disk that is accessible by both members, rather than separate storage for each member.

## Using the pricing calculator
{: #pricing-calc}

For pricing estimation, use the **Add to estimate** button on the [{{site.data.keyword.databases-for-mysql}} catalog page](https://cloud.ibm.com/catalog). 

Due to the shared storage architecture, disk storage billing differs from other {{site.data.keyword.databases-for-}} offerings.
{: important}

* **Disk storage:** You are charged for the provisioned disk size once, not per member. For example, if you provision 100 GB of disk for your 2-member deployment, you are billed for 100 GB total (not 200 GB).
* **RAM and CPU:** You are charged for the total resources across both data members. For example, a 4 vCPU x 20 GB RAM profile means you pay for 2 members worth of compute resources.

Example calculation:

* 100 GB disk provisioned = 100 GB billed (shared storage)
* 4 vCPU x 20 GB RAM profile = 2 members x (4 vCPU x 20 GB RAM) billed

## Gen 2 backups pricing
{: #pricing-backup}

Gen 2 {{site.data.keyword.databases-for-}} uses a snapshot based backup model, with pricing aligned to the size of your provisioned database storage. Snapshots differ from traditional backups in that they are block-level incremental copies, therefore you are billed based on how much data has changed since the last snapshot, not just the total size of your database. Snapshots have a minimum size of 1 GB and are rounded up to the next full Gigabyte.

By default, {{site.data.keyword.databases-for-mysql}} provides a daily backup that is stored for 30 days. These backups, and any on-demand backups you make, all count toward the above allocation.

Backup storage included:

* You receive free backup storage equal to the total provisioned disk size of your deployment.
* This includes both automated daily backups and manual (on-demand) snapshots.
* Example: If your 2-member {{site.data.keyword.databases-for-mysql}} deployment is provisioned with 100 GB of disk, you get 100 GB of backup storage included at no cost.

Overage charges:

* The overage is billed monthly.
* Total snapshot storage = Day 1 full + (daily change × 29 days)
* Overage is charged at $0.18 per GB per month.

Worked example, for a 2-member MySQL deployment with 100 GB of disk storage:

* Day 1: A full snapshot is taken from the current primary. This consumes 100 GB of snapshot storage.

This models the worst case scenario where the full snapshot is equal to the file system size. In practice, especially for new databases that grow over time, snapshot sizes are typically smaller, which helps reduce backup costs.
{: note}

* Day 2-16: You write 10 GB of new data per day. Snapshots are incremental and only store changes. Over 15 days, this adds 150 GB, bringing total snapshot usage to 100 GB + 150 GB = 250 GB.

* Your backup storage utilization is now greater than the free allocation of 100 GB for the month (in this scenario). Billing incurs for an overage at a rate of $0.18/month per Gigabyte.

* Day 17: A failover occurs, and one secondary member becomes the new primary. A full snapshot is taken from this new primary, consuming another 100 GB.

* Day 18-30: You continue writing 10 GB per day, adding 130 GB over 13 days.
  
Total snapshot = 100 GB (initial) + 150 GB (incremental) + 100 GB (failover snapshot) + 130 GB (post-failover incremental) = 480 GB
Free allocation = 100 GB x 2 members
Overage = 480 GB - 200 GB = 280 GB
Monthly charge = (480 GB - 200 GB) x $0.18 = $50.4

With large deployments and frequent writes, you may exceed the free tier after the first snapshot.

* Cross-region copies: If you choose to copy snapshots to another region, {{site.data.keyword.cloud}} charges for the full size of the snapshot in the destination region (not incremental) and continued incremental growth in the original region as new snapshots are taken.

## Scaling per member
{: #scaling-member}

{{site.data.keyword.databases-for-mysql}} instances have minimum and maximum allocation for disk and RAM as shown. Scaling instances through the API and CLI provides more granularity and also allows you to scale a database instance up to 9600 GB of disk per member. Minimum and maximum CPU and RAM combinations vary per region and as per the host flavor, see [Isolated Compute](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-isolated-compute&interface=cli).

| Resource | Minimum | Maximum | Scaling granularity (API/CLI) |
| ---------- | ----- | ----- | ------- |
| Disk | 10 GB per member | 9600 GB per member | 1024 MB per member |
| RAM | 16 GB | 240 GB | Isolated Compute – Resource scaling via T-shirt sizes |
| CPU | 4 vCPU | 48 vCPU| Isolated Compute – Resource scaling via T-shirt sizes |
{: caption="Scaling limits" caption-side="top"}
