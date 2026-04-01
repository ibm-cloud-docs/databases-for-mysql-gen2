---
copyright:
  years: 2021, 2026
lastupdated: "2026-04-01"

keywords: mysql, databases, pricing, resources, scaling, mysql pricing

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Pricing
{: #pricing}

A {{site.data.keyword.databases-for-mysql-gen2}} Standard plan deploys as one highly available MySQL cluster with three data members. Your data is replicated on all members. The Standard plan is priced based on the total amount of disk storage, RAM, dedicated cores, and backup storage that is allocated to deployments, prorated hourly. {{site.data.keyword.databases-for-mysql-gen2}} deployments have a minimum of 10 GB of disk and 4 GB of RAM per data member.

## Using the Pricing Calculator
{: #pricing-calc}

Templates are provided for ease of use and provide balanced resource allocations appropriate for general-purpose workloads. The **Custom** tab can be used to configure Disk, RAM, and vCPU, as wanted.

For pricing estimation, use the **Add to Estimate** button on the [{{site.data.keyword.databases-for-mysql-gen2}} catalog page](https://cloud.ibm.com/databases/databases-for-mysql-gen2/create?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2cjc2VydmljZXM%3D). Input your total consumption across three data members into the calculator. This is roughly double the size of your data because your data is replicated to all members. For example, 20 GB of disk and 1 GB of RAM across three data members would be priced at 60 GB of disk and 3 GB of RAM respectively. 

## Backups Pricing
{: #pricing-backup}

You receive your total disk space purchased, per database, in free backup storage. For example, in a given month, if you have a {{site.data.keyword.databases-for-mysql-gen2}} deployment that has 20 GB of disk per member, and has three data members, you receive 60 GB of backup storage free for that month. If your backup storage utilization is greater than 60 GB for the month (in this scenario), you are charged an overage of $0.03/month per gigabyte. 

By default, {{site.data.keyword.databases-for}} provides a daily backup that is stored for 30 days. These backups, and any on-demand backups you make, all count toward the above allocation.

In the above example, if your database contains 2 GB of data and you have not taken any on-demand backups, then your total backup size is 2 GB x 30 = 60 GB. Your backup costs are nil.

If your database contains 15 GB of data and you have not taken any on-demand backups, then your total backup size is 15 GB x 30 = 450 GB. In this scenario, your backup costs are (450 GB - 60 GB) * 0.03 = $11.7 per month.

Most deployments will not ever go over the allotted credit.

## Scaling per Member
{: #scaling-member}

{{site.data.keyword.databases-for-mysql-gen2}} deployments have minimum and maximum allocation for disk and RAM as shown. Scaling deployments through the API/CLI provides more granularity and also allows a user to scale a database instance up to 4 TB of disk per member.

| Resource | Minimum | Maximum | Scaling Granularity (API/CLI) |
| ---------- | ----- | ----- | ------- |
| Disk | 10 GB per member | 4 TB per member | 1024 MB per member |
| RAM | 1 GB per member | 112 GB per member | 128 MB per member |
| CPU (if enabled) | 3 CPUs per member | 28 CPUs per member| 1 CPU per member |
{: caption="Per Member Scaling Limits" caption-side="top"}
