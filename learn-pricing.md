---
copyright:
  years: 2026
lastupdated: "2026-06-26"

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

Due to the shared storage architecture, disk storage billing differs from other {{site.data.keyword.databases-for}} offerings.
{: important}

* **Disk storage:** You are charged for the provisioned disk size once, not per member. For example, if you provision 100 GB of disk for your 2-member deployment, you are billed for 100 GB total (not 200 GB).
* **RAM and CPU:** You are charged for the total resources across both data members. For example, a 4 vCPU x 20 GB RAM profile means you pay for 2 members worth of compute resources.

Example calculation:

* 100 GB disk provisioned = 100 GB billed (shared storage)
* 4 vCPU x 20 GB RAM profile = 2 members x (4 vCPU x 20 GB RAM) billed


## Gen 2 backups pricing
{: #pricing-backup}

Gen 2 {{site.data.keyword.databases-for-mysql}} backup storage usage is currently not billed due to a known issue. This issue does not affect backup creation, retention, or recovery operations. Billing will resume after the issue is resolved, and applicable backup charges will be reflected in future invoices.
{: warning}

 
## Scaling per member
{: #scaling-member}

{{site.data.keyword.databases-for-mysql}} instances have minimum and maximum allocation for disk and RAM as shown. Scaling instances through the API and CLI provides more granularity and also allows you to scale a database instance up to 9600 GB of disk per member. Minimum and maximum CPU and RAM combinations vary per region and as per the host flavor, see [Isolated Compute](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-isolated-compute&interface=cli).

| Resource | Minimum | Maximum | Scaling granularity (API/CLI) |
| ---------- | ----- | ----- | ------- |
| Disk | 10 GB per member | 9600 GB per member | 1024 MB per member |
| RAM | 16 GB | 240 GB | Isolated Compute – Resource scaling via T-shirt sizes |
| CPU | 4 vCPU | 48 vCPU| Isolated Compute – Resource scaling via T-shirt sizes |
{: caption="Scaling limits" caption-side="top"}
