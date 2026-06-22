---

copyright:
  years: 2026
lastupdated: "2026-06-22"

keywords: HA, DR, high availability, disaster recovery, disaster recovery plan, disaster event, mysql, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Understanding high availability and disaster recovery
{: #mysql-ha-dr}

[Gen 2]{: tag-purple}

[High availability](#x2284708){: term} (HA) is the ability for a service to remain operational and accessible in the presence of unexpected failures. [Disaster recovery](#x2113280){: term} is the process of recovering the service instance to a working state.
{: shortdesc}

{{site.data.keyword.databases-for-mysql}} is a regional service that fulfills the defined [Service Level Objectives (SLO)](/docs/resiliency?topic=resiliency-slo) with the Standard plan. For more information, see the [Service Level Agreement (SLA)](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en). For more information about the available {{site.data.keyword.cloud_notm}} regions and data centers for {{site.data.keyword.databases-for-mysql}}, see [Service and infrastructure availability by location](/docs/overview?topic=overview-services_region).

## High availability architecture
{: #ha-architecture}

Image to be uploaded by Aman

{{site.data.keyword.databases-for-mysql}} provides replication, failover, and high-availability features to protect your databases and data from infrastructure maintenance, upgrades, and failures. Deployments contain a cluster with two data members: primary and replica. The replica is kept up to date using synchronous replication at the storage layer. A distributed consensus mechanism is used to maintain cluster state and handle failovers. If the primary becomes unreachable, the cluster initiates a failover, and the replica is promoted to primary. The primary and replica will always be in different zones of an MZR. If the replica fails, a new replica is created. If a zone failure results in a member failing, the new replica will be created in a surviving zone.

MySQL Gen 2 uses regional storage with synchronous replication at the storage infrastructure level. Through synchronous replication to each zone's persistent disk, all writes made to the primary instance are replicated to disks in both zones before a transaction is reported as committed. This ensures data consistency across zones. In the event of an instance or zone failure, the replica instance becomes the new primary instance. Users are then rerouted to the new primary instance through a shared static IP address. This process is called a failover.

There are cases where MySQL issues a restart instead of a failover. When this happens, you see Restart as an operation on the instance. For example, the database can restart when a resource is exhausted, such as when an instance runs out of memory.
{: note}

In scenarios where a database becomes critically unhealthy, such as a server crash on the leader, {{site.data.keyword.databases-for-mysql}} attempts a failover.

Workloads that programmatically access the cluster must follow the client availability retry logic to maintain availability.

The service will, at times, do controlled failovers under normal operation. These failovers are no-data-loss events but result in resets of active connections. There is a period of up to 15 seconds where reconnections can fail. At times, unplanned failovers might occur due to unforeseen events in the operating environment. These can take up to 45 seconds, but generally less than 30. Service maintenance, for example, triggers a controlled failover.

### High availability features
{: #ha-features}

{{site.data.keyword.databases-for-mysql}} supports the following high availability features:

| Feature | Description | Consideration |
| -------------- | -------------- | -------------- |
| Automatic failover | Standard on all clusters and resilient against a zone or single member failure | |
| Member count | 2-member deployment (primary + replica) | |
| Synchronous replication | Data replicated synchronously at storage layer across zones | |
{: caption="Table 1. High availability features" caption-side="top"}

### Synchronous replication {{site.data.keyword.databases-for-mysql}}
{: #synchronous-replication}

MySQL Gen 2 uses synchronous replication at the storage infrastructure level to ensure data consistency. The HA configuration provides data redundancy through regional storage. A MySQL instance configured for HA is also called a regional instance and has a primary and replica instance within the configured region. Within a regional instance, the configuration is made up of a primary instance and a replica instance.

Through synchronous replication to each zone's persistent disk, all writes made to the primary instance are replicated to disks in both zones before a transaction is reported as committed. This approach ensures that data is consistent across zones and provides protection against zone failures. The synchronous replication occurs at the storage infrastructure layer, which means:

- Data written to the primary instance is automatically replicated across zones at the storage level
- Replication happens synchronously, ensuring data consistency
- No database-level replication configuration is required
- The replica instance accesses the same replicated storage

This storage-based synchronous replication provides data consistency while simplifying operations compared to traditional database-level replication. The architecture ensures that your data is protected even in the event of a complete zone failure, as the data is already present in multiple zones through the storage layer replication.

Regional storage with synchronous replication has full Service Level Agreement (SLA) coverage. An HA-configured instance costs twice as much as a standalone instance. This price includes CPU, RAM, and storage. For more information, see the pricing page.

## Disaster recovery architecture
{: #disaster-recovery-intro}

The general strategy for disaster recovery is to create a new database from a backup, like the Restore database below. The contents of the new database can be a backup of the source database created before the disaster.

Image to be uploaded by Aman

### Disaster recovery features
{: #dr-features}

{{site.data.keyword.databases-for-mysql}} supports the following disaster recovery features:

| Feature | Description | Consideration |
| -------------- | -------------- | -------------- |
| Backup restore | Create database from previously created backup; see [Managing Cloud Databases backups](/docs/cloud-databases?topic=cloud-databases-dashboard-backups) | New connection strings for the restored database must be referenced throughout the workload |
{: caption="Table 2. Disaster recovery features" caption-side="top"}

### Planning for disaster recovery
{: #features-for-disaster-recovery}

The disaster recovery steps must be practiced regularly. As you build your plan, consider the following failure scenarios and resolutions.

| Failure | Resolution |
| -------------- | -------------- |
| Hardware failure (single point) | IBM provides a database that is resilient from a single point of hardware failure within a zone - no configuration is required |
| Zone failure | Automatic failover. The database members are distributed between zones. Synchronous replication at storage layer ensures data consistency across zones |
| Data corruption | Backup restore: Use the restored database in production or for source data to correct the corruption in the production database |
| Regional failure | Backup restore: Use the restored database in production |
| Accidental deletion | Backup restore: Restore from backup if within retention period |
{: caption="Table 3. Failure scenarios and resolutions" caption-side="top"}

## Application-level high-availability
{: #application-level-ha}

Applications that communicate over networks and cloud services are subject to transient connection failures. You want to design your applications to retry connections when errors are caused by a temporary loss in connectivity to your deployment or to {{site.data.keyword.cloud_notm}}.

Because {{site.data.keyword.databases-for-mysql}} is a managed service, regular updates and database maintenance occur as part of normal operations. This can occasionally cause short intervals where your database is unavailable. It can also cause the database to trigger a graceful fail-over, retry, and reconnect. It takes a short time for the database to determine which member is a replica and which is the primary, so you might also see a short connection interruption. Failovers generally take less than 30 seconds.

Your applications must be designed to handle temporary interruptions to the database, implement error handling for failed database commands, and implement retry logic to recover from a temporary interruption.

Several minutes of database unavailability or connection interruption are not expected. Open a [support case](https://cloud.ibm.com/unifiedsupport/cases/add) with details if you have periods longer than a minute with no connectivity so we can investigate.

## Connection limits
{: #connection-limits-ha}

{{site.data.keyword.databases-for-mysql}} sets the maximum number of connections to your MySQL database to **200**. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. After the connection limit is reached, any attempts at starting a new connection result in an error. To prevent overwhelming your deployment with connections, use connection pooling, or scale your deployment and increase its connection limit. For more information, see [Managing MySQL connections](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-managing-mysql-connections).

## Your responsibilities for HA and DR
{: #feature-responsibilities}

The following information can help you create and continuously practice your plan for HA and DR.

When restoring a database from backups, a new database is created with new connection strings. Existing workloads and processes must be adjusted to consume the new connection strings.

A recovered database may also need the same customer-created dependencies of the disaster database - make sure these and other services exist in the recovered region:

- {{site.data.keyword.keymanagementservicefull}}

Remember that deleting a database also deletes its associated backups. However, deleted databases may be recoverable within a limited timeframe. Refer to the [Backups FAQ](/docs/cloud-databases?topic=cloud-databases-faq-backups) for specific details on database recovery procedures.

It is not possible to copy backups off the {{site.data.keyword.cloud_notm}}, so consider using the database-specific tools for additional backups. It may be required to recover from malicious database deletion followed by a reclamation-delete of a database. Careful management of IAM access to databases can help reduce exposure to this problem.

The following checklist associated with each feature can help you create and practice your plan.

- Backup restore
   - Verify backups are available at the desired frequency to meet RPO requirements. [Managing Cloud Databases backups](/docs/cloud-databases?topic=cloud-databases-dashboard-backups) documents backup frequency. Consider a script by using [IBM Cloud® Code Engine - Working with the Periodic timer (cron) event producer](/docs/codeengine?topic=codeengine-subscribe-cron) to create additional on-demand backups to improve RPO if the criticality and size of the database allow.
   - There are some restrictions on database restore regions - verify your restore goals can be achieved by reading [Managing Cloud Databases backups](/docs/cloud-databases?topic=cloud-databases-dashboard-backups).
   - Verify the retention period of the backups meet your requirements.
   - Schedule test restores regularly to verify that the actual restored times meet the defined RTO. Remember that database size significantly impacts restore time. Consider strategies to minimize restore times, such as breaking down large databases into smaller, more manageable units and purging unused data.
   - Verify the {{site.data.keyword.keymanagementserviceshort}} service.

To find out more about responsibility ownership between the customer and {{site.data.keyword.cloud_notm}} for using {{site.data.keyword.databases-for-mysql}}, see [Shared responsibilities for {{site.data.keyword.databases-for}}](/docs/cloud-databases?topic=cloud-databases-responsibilities-cloud-databases).

## Stay informed: {{site.data.keyword.IBM_notm}} notifications
{: #ibm-service-notifications}

Updates affecting customer workloads are communicated through {{site.data.keyword.cloud_notm}} notifications. To stay informed about planned maintenance, announcements, and release notes related to this service, refer to the [Monitoring notifications and status](/docs/account?topic=account-viewing-cloud-status){: external} page. In addition, regularly review the [Version policy](/docs/cloud-databases?topic=cloud-databases-versioning-policy) page for the latest updates on End-of-Life versions and dates.

## Additional guidance
{: #ha_dr-guidance}

- [Understanding high availability for Cloud Databases](/docs/cloud-databases?topic=cloud-databases-ha-dr)
- [Understanding business continuity and disaster recovery for Cloud Databases](/docs/cloud-databases?topic=cloud-databases-bc-dr)
- [Managing connections - Databases for MySQL](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-managing-mysql-connections)
