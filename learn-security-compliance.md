---
copyright:
  years: 2026
lastupdated: "2026-06-25"

keywords: mysql, databases, soc, hipaa, gdpr, terms, mysql security compliance, mysql dedicated cores, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Security and compliance
{: #security-compliance}

[Gen 2]{: tag-purple}

## Protection against unauthorized access
{: #security-compliance-protection-access}

{{site.data.keyword.databases-for-mysql_full}} Gen 2 uses the following methods to protect data in transit or in storage:

### Authentication and Access Control
- **TLS Encryption:** All {{site.data.keyword.databases-for-mysql}} connections use mandatory TLS 1.2+ encryption for data in transit with automatic certificate rotation.
- **mTLS:** Database-to-database and job-to-database connections are secured with mutual TLS.
- **IBM Cloud IAM:** Access to the Account, Management Console UI, and API is secured through [Identity and Access Management (IAM)](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-iam&interface=ui) with federated identity and role-based access control.
- **MySQL Native Authentication:** Access to the database is secured through the standard MySQL user/password authentication with granular privilege system.

### Data Protection
- **At-Rest Encryption:** All {{site.data.keyword.databases-for-mysql}} storage is encrypted with AES-256. The default keys are managed by [{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about).
- **BYOK Support:** Bring-your-own-key (BYOK) for encryption is available through [Key Protect integration](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-key-protect&interface=ui) for both disk and backup encryption.
- **Automatic Secret Rotation:** TLS certificates and secrets are rotated automatically without downtime.

### Network Isolation
- **Private Endpoints Only:** Gen 2 supports private endpoints only for enhanced security and network isolation. All connections are made through the {{site.data.keyword.cloud_notm}} private network.
- **VPC Deployment:** Deployments are integrated with Virtual Private Cloud for network isolation.

### Isolated Compute
{: #compute-isolated}

- **Hypervisor-Level Isolation:** Gen 2 supports Isolated Compute only, providing dedicated resources with hypervisor-level isolation to ensure that your data processing remains separated from other customers.

### MySQL Security Best Practices
- Do not grant the [PROCESS](https://dev.mysql.com/doc/refman/8.4/en/privileges-provided.html#priv_process) or [SUPER](https://dev.mysql.com/doc/refman/8.4/en/privileges-provided.html#priv_super) privilege to nonadministrative users. `mysqld` reserves an extra connection for users who have the SUPER privilege so that a MySQL root user can log in and check server activity even if all normal connections are in use.
- The [SUPER](https://dev.mysql.com/doc/refman/8.4/en/privileges-provided.html#priv_super) privilege can be used to terminate client connections, change server operation by changing the value of system variables, and control replication servers. For more information, see MySQL's [Making MySQL secure against attackers](https://dev.mysql.com/doc/refman/8.4/en/security-against-attack.html) documentation.

## Data resilience
{: #security-compliance-data-resilience}

- **Snapshot-based Backups:** Backups are included in the service. Gen 2 uses daily automated VPC snapshots to IBM Cloud File Storage with 30-day retention at GA (programmable retention periods available in Q3 hardening). Backups are located in [{{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage) and are [encrypted](/docs/cloud-object-storage?topic=cloud-object-storage-security) with AES-256 encryption.
- **Cross-Region Replication:** Backups support cross-region replication with BYOK support via Key Protect.
- **Point-in-Time Recovery (PITR):** Available in Q3 hardening for granular recovery.
- **Storage-Based Replication:** {{site.data.keyword.databases-for-mysql}} Gen 2 deployments use a 2-node cluster with Regional File Storage (RFS). Both nodes share a single regional storage solution that provides synchronous storage-based replication across all availability zones, ensuring zero data loss (RPO = 0) and eliminating replication lag.
- **Multi-Zone Deployment:** If you deploy to an {{site.data.keyword.cloud_notm}} Multi-Zone Region (MZR), the primary and replica are spread over the region's availability zone locations for maximum resilience.

## Compliance Certifications
{: #security-compliance-certifications}

{{site.data.keyword.databases-for-mysql_full}} Gen 2 meets enterprise compliance requirements:

- **SOC 2:** Service Organization Control reports for security, availability, and confidentiality
- **ISO 27017:** Information security management for cloud services
- **ISO 27018:** Protection of personally identifiable information (PII) in public clouds
- **GDPR:** General Data Protection Regulation compliance for data privacy
- **HIPAA:** Health Insurance Portability and Accountability Act compliance for healthcare data
- **PCI DSS:** Payment Card Industry Data Security Standard for payment card data protection

## Terms
{: #security-compliance-terms}

- [The IBM privacy policy](https://www.ibm.com/privacy/us/en/)
- [The IBM Cloud notices and terms of use](/docs/overview/terms-of-use?topic=overview-terms)
