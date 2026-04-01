---

copyright:
  years: 2026
lastupdated: "2026-04-01"

keywords: HA, DR, high availability, disaster recovery, disaster recovery plan, disaster event, mysql

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Understanding high availability and disaster recovery for {{site.data.keyword.databases-for-mysql-gen2}}
{: #mysql-ha-dr}

[High availability](#x2284708){: term} (HA) is the ability for a service to remain operational and accessible in the presence of unexpected failures. [Disaster recovery](#x2113280){: term} is the process of recovering the service instance to a working state.
{: shortdesc}

{{site.data.keyword.databases-for-mysql-gen2}} is a regional service that fulfills the defined [Service Level Objectives (SLO)](/docs/resiliency?topic=resiliency-slo) with the Standard plan. For more information, see the [Service Level Agreement (SLA)](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en). For more information about the available {{site.data.keyword.cloud_notm}} regions and data centers for {{site.data.keyword.databases-for-mysql-gen2}}, see [Service and infrastructure availability by location](/docs/overview?topic=overview-services_region).

## High availability architecture
{: #ha-architecture}

![Architecture](/images/MySQL_high_availability.svg){: caption="MySQL high availability architecture" caption-side="bottom"}

{{site.data.keyword.databases-for-mysql-gen2}} provides replication, failover, and high-availability features to protect your databases and data from infrastructure maintenance, upgrades, and some failures. Deployments contain a cluster with three data members – a leader and two replicas. Replicas are kept up to date using asynchronous replication. All members contain a copy of your data by using Orchestrator to handle failovers. The leader and replicas are always in different zones of a multi-zone region. If the leader becomes unreachable or encounters a critical issue, the service first attempts to auto-recover the existing leader. If the leader cannot be recovered, a failover is performed and a replica is promoted to leader, a new replica rejoins the cluster as a replica, and your cluster continues to operate normally. If the replica fails, a new replica is created. If a zone failure results in a member failing, the new replica is created in a surviving zone.

You can extend high availability further by provisioning [read-only replicas](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-read-replicas) for cross-regional failover or read offloading.

Review the MySQL documentation on [replication techniques](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication.html){: .external} to understand the constraints and tradeoffs associated with asynchronous replication.

Although version 8.0 utilizes semi-synchronous replication and version 8.4 introduces asynchronous technology, the core high availability and disaster recovery behaviors remain fundamentally unchanged.
{: note}

Workloads that programmatically access the cluster must follow the client availability retry logic to maintain availability.

{{site.data.keyword.databases-for-mysql-gen2}} sometimes performs controlled switchovers under normal operation. These switchovers are generally no-data-loss events that result in the reset of active connections. There is a period of up to 15 seconds where reconnections can fail. At times, unplanned failovers might occur due to unforeseen events in the operating environment. These can take up to 45 seconds, but generally less than 30 seconds. Service maintenance, for example, triggers a controlled failover.


### High availability features
{: #ha-features}

{{site.data.keyword.databases-for-mysql-gen2}} supports the following high availability features:

| Feature | Description | Consideration |
| -------------- | -------------- | -------------- |
| Failover behavior | Standard on all clusters and resilient against a zone or single member failure. If the leader becomes unhealthy or unreachable, the service attempts to auto-recover it. If the leader cannot be recovered, a failover is performed and a replica is promoted to restore write availability. | To minimize the replica lag between the leader and replicas, follow [best practices](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-best-practices&interface=cli), such as retry logic, connection management, and implement primary keys. |
| Member count | A three-member deployment. A three-member cluster can recover from a single failure of an instance or zone, with data lag possible during the recovery process. A replica is promoted to leader in the event of a failure, and the cluster continues to operate normally. | |
| Read-only replica | Read-only replicas can provide local access in remote regions, improving availability to potential network latency or connectivity issues. | All Write requests must be directed exclusively to the read-write cluster associated with the read-replica. |
{: caption="High availability features" caption-side="top"}

## Disaster recovery architecture
{: #disaster-recovery-intro}

The general strategy for disaster recovery is to create a new database, such as the `Restore` database. The contents of the new database can be a backup of the source database created before the disaster. A new database can be created using the point-in-time feature if the production database is available.

![Architecture](/images/MySQL_disaster_recovery.svg){: caption="MySQL disaster recovery architecture" caption-side="bottom"}

### Disaster recovery features
{: #dr-features}

{{site.data.keyword.databases-for-mysql-gen2}} supports the following disaster recovery features:

| Feature | Description | Consideration |
| -------------- | -------------- | -------------- |
| Backup restore | Create a database from previously created backup. For more information, see [Managing Cloud Databases backups](/docs/cloud-databases?topic=cloud-databases-dashboard-backups). | New connection strings for the restored database must be referenced throughout the workload. |
| Point-in-time restore | Create a database from the live production using [point-in-time recovery](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-pitr). | This is only possible if the active database is available and the RPO (disaster) falls within the supported window. It is not useful if the production cluster is unavailable. New connection strings for the restored database must be referenced throughout the workload. |
| Promote read replica | Create a [read-only replica](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-read-replicas) when planning for a disaster in the same or remote region. [Promote the read-only replica](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-read-replicas&interface=cli#read-replica-promoting) to recover from a disaster. | Previously created read replica must be available. New connection strings for the restored database must be referenced throughout the workload. |
{: caption="Disaster recovery features" caption-side="top"}

### Planning for disaster recovery
{: #features-for-disaster-recovery}

The disaster recovery steps must be practiced regularly. As you build your plan, consider the following failure scenarios and resolutions.

| Failure | Resolution |
| -------------- | -------------- |
| Hardware failure (single point) | IBM provides a database that is resilient from a single point of hardware failure within a zone - no configuration is required. |
| Zone failure | Failover (#mysql-high-availability). The database members are distributed between zones. |
| Data corruption | Backup restore. Use the restored database in production or for source data to correct the corruption in the restored database. \n \nPoint-in-time restore. Use the restored database in production or for source data to correct the corruption in the restored database. |
| Regional failure | Backup restore. Use the restored database in production. \n \nPromote read replica. Promote a read-only replica to a read/write database. Use the restored database in production |
{: caption="Failure scenarios and resolutions" caption-side="top"}

## Application-level high-availability
{: #application-level-ha}

Applications that communicate over networks and cloud services are subject to transient connection failures. You want to design your applications to retry connections when errors are caused by a temporary loss in connectivity to your deployment or to {{site.data.keyword.cloud_notm}}.

Because {{site.data.keyword.databases-for-mysql-gen2}} is a managed service, regular updates and database maintenance occur as part of normal operations. If both replicas are lost, writes to the leader hang, due to the semisynchronous replication process not having a follower. For more information, see [semisynchronous replication](https://dev.mysql.com/doc/mysql-replication-excerpt/8.0/en/replication-semisync.html). This scenario occasionally causes short intervals where your database is unavailable. It can also cause the database to trigger a graceful failover, retry, and reconnect. It takes a short time for the database to determine which member is a replica and which is the leader, so you might also see a short connection interruption. Failovers generally take less than 30 seconds. To minimize interruptions, updates are applied to replicas first, and the leader last.

Your applications must be designed to handle temporary interruptions to the database, implement error handling for failed database commands, and implement retry logic to recover from a temporary interruption.

Several minutes of database unavailability or connection interruption are not expected. Open a [support case](https://cloud.ibm.com/unifiedsupport/cases/add) with details if you have periods longer than a minute with no connectivity so we can investigate.

## Connection limits
{: #connection-limits-ha}

{{site.data.keyword.databases-for-mysql-gen2}} sets the maximum number of connections to your MySQL database to **200**. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. After the connection limit is reached, any attempts at starting a new connection result in an error. To prevent overwhelming your deployment with connections, use connection pooling, or scale your deployment and increase its connection limit. For more information, see [Managing MySQL connections](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-managing-mysql-connections).

## Your responsibilities for HA and DR
{: #feature-responsibilities}

The following information can help you create and continuously practice your plan for HA and DR.



When restoring a database from backups or using point-in-time restore, a new database is created with new connection strings. Existing workloads and processes must be adjusted to consume the new connection strings. Promoting a read replica to a cluster will have a similar impact, although existing read-only portions of the workload will not be impacted.

A recovered database may also need the same customer-created dependencies of the disaster database - make sure this and other services exist in the recovered region:

- {{site.data.keyword.keymanagementservicefull}}

Remember that deleting a database also deletes its associated backups. However, deleted databases may be recoverable within a limited timeframe. Refer to the [Backups FAQ](/docs/cloud-databases?topic=cloud-databases-faq-backups) for specific details on database recovery procedures.

It is not possible to copy backups off the {{site.data.keyword.cloud_notm}}, so consider using the database-specific tools for additional backups. It may be required to recover from malicious database deletion followed by a reclamation-delete of a database. Careful management of IAM access to databases can help reduce exposure to this problem.

The following checklist associated with each feature can help you create and practice your plan.

- Backup restore
   - Verify that backups are available at the desired frequency to meet RPO requirements. For more information, see [Managing Cloud Databases backups](/docs/cloud-databases?topic=cloud-databases-dashboard-backups). Consider a script using [IBM Cloud® Code Engine - Working with the Periodic timer (cron) event producer](/docs/codeengine?topic=codeengine-subscribe-cron) to create additional on-demand backups to improve RPO if the criticality and size of the database allow. However, given MySQL's PITR capabilities, carefully evaluate the need for additional backups.
   - There are some restrictions on database restore regions - verify that your restore goals can be achieved by reading [managing Cloud Databases backups](/docs/cloud-databases?topic=cloud-databases-dashboard-backups).
   - Verify that the retention period of the backups meet your requirements.
   - Schedule test restores regularly to verify that the actual restored times meet the defined RTO. Remember that database size significantly impacts restore time. Consider strategies to minimize restore times, such as breaking down large databases into smaller, more manageable units and purging unused data.
   - Verify the Key Protect service.
- Point-in-time restore
   - Verify the procedures covered earlier.
   - Verify that the desired backup is in the window.
- Promote read replica
   - Verify that a read replica exists in the recovery region.
   - Practice the promotion process - create a temporary read replica in the desired region. The temporary replica can be promoted to read/write and some testing performed with little impact to production.

To find out more about responsibility ownership between the customer and {{site.data.keyword.cloud_notm}} for using {{site.data.keyword.databases-for-mysql-gen2}}, see [Shared responsibilities for {{site.data.keyword.databases-for}}](/docs/cloud-databases?topic=cloud-databases-responsibilities-cloud-databases).

## Stay informed: {{site.data.keyword.IBM_notm}} notifications
{: #ibm-service-notifications}

Updates affecting customer workloads are communicated through {{site.data.keyword.cloud_notm}} notifications. To stay informed about planned maintenance, announcements, and release notes related to this service, refer to the [Monitoring notifications and status](/docs/account?topic=account-viewing-cloud-status) page. In addition, regularly review the [Version policy](/docs/cloud-databases?topic=cloud-databases-versioning-policy) page for the latest updates on End-of-Life versions and dates.

## Additional guidance
{: #ha_dr-guidance}

- [Understanding high availability for Cloud Databases](/docs/cloud-databases?topic=cloud-databases-ha-dr)
- [Understanding business continuity and disaster recovery for Cloud Databases](/docs/cloud-databases?topic=cloud-databases-bc-dr)
- [Managing connections - Databases for MySQL](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-managing-mysql-connections)
