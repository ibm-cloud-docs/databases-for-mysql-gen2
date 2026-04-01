---
copyright:
  years: 2021, 2026
lastupdated: "2026-04-01"

keywords: mysql, databases, soc, hipaa, gdpr, terms, mysql security compliance, mysql dedicated cores

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Security and compliance
{: #security-compliance}

## Protection against unauthorized access
{: #security-compliance-protection-access}

{{site.data.keyword.databases-for-mysql-gen2_full}} use the following methods to protect data in transit or in storage.
- All {{site.data.keyword.databases-for-mysql-gen2}} connections use TLS/SSL encryption for data in transit. The current supported version of this encryption is TLS 1.2.
- Access to the Account, Management Console UI, and API is secured through [Identity and Access Management (IAM)](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-iam&interface=ui).
- Access to the database is secured through the standard access controls provided by the database. These access controls are configured to require valid database-level credentials that are obtainable only through prior access to the database or through our Management Console UI or API.
- All {{site.data.keyword.databases-for-mysql-gen2}} storage is provided on storage encrypted with LUKS using AES-256. The default keys are managed by [{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about). Bring-your-own-key (BYOK) for encryption is also available through [Key Protect integration](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-key-protect&interface=ui).
- IP allowlisting - All deployments support [allowlisting IP addresses](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-allowlisting&interface=ui) to restrict access to the service.
- Public and private networking - {{site.data.keyword.databases-for-mysql-gen2}} is integrated with [Service endpoints](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-service-endpoints&interface=ui). You can select whether to use connections over the public network, the {{site.data.keyword.cloud_notm}} internal network, or both.
- Dedicated Cores - Allocating dedicated cores to your deployment introduces hypervisor-level isolation to your database instance, by using isolated virtual machines to ensure that your data processing remains separated from other customers. It also provides a minimum number of CPUs to your deployment. Deployments with dedicated cores in the same Resource Group and {{site.data.keyword.cloud_notm}} Region might share a virtual machine.
- Do not grant the [PROCESS](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_process) or [SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) privilege to nonadministrative users. `mysqld` reserves an extra connection for users who have the SUPER privilege so that a MySQL root user can log in and check server activity even if all normal connections are in use.
- The [SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) privilege can be used to terminate client connections, change server operation by changing the value of system variables, and control replication servers. For more information, see MySQL's [Making MySQL secure against attackers](https://dev.mysql.com/doc/refman/5.7/en/security-against-attack.html) documentation.

## Data resilience
{: #security-compliance-data-resilience}

- [Backups](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-dashboard-backups&interface=ui) are included in the service. {{site.data.keyword.databases-for-mysql-gen2}} backups are located in [{{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage&cloud-object-storage-about-cloud-object-storage) and are also [encrypted](/docs/cloud-object-storage?topic=cloud-object-storage-security).
- {{site.data.keyword.databases-for-mysql-gen2}} deployments are configured with replication. Deployments contain a cluster with three data members. All members contain a copy of your data through [semisynchronous replication](https://dev.mysql.com/doc/mysql-replication-excerpt/8.0/en/replication-semisync.html){: .external}, with a distributed consensus mechanism to maintain cluster state and handle failovers. 
- If you deploy to an {{site.data.keyword.cloud_notm}} Single-Zone Region (SZR), each database member is on a different host in the data center. 
- If you deploy to an {{site.data.keyword.cloud_notm}} Multi-Zone Region (MZR), the members are spread over the region's availability zone locations. 

## Terms
{: #security-compliance-terms}

- [The IBM privacy policy](https://www.ibm.com/privacy/us/en/)
- [The IBM Cloud notices and terms of use](/docs/overview/terms-of-use?topic=overview-terms)
