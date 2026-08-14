---

copyright:
  years: 2026
lastupdated: "2026-08-14"

keywords: mysql, databases, monitoring, scaling, autoscaling, resources, mysql connection limits, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Performance
{: #performance}

[Gen 2]{: tag-purple}

{{site.data.keyword.databases-for-mysql_full}} deployments can be manually [scaled to your usage](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-resources-scaling#resources-scaling-consider). There are several factors to consider when you are tuning the performance of your deployment.


## Monitoring your deployment
{: #monitor-deployment}

{{site.data.keyword.databases-for-mysql}} deployments offer an integration with the[{{site.data.keyword.monitoringfull}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-monitoring) service for basic monitoring of resource usage on your deployment. Many of the available metrics are presented to help you monitor your deployment. Observing trends in your usage can help alleviate performance problems before your databases become unstable due to resource exhaustion.

 


## Connection Limits
{: #connection-limits-performance}

{{site.data.keyword.databases-for-mysql}} sets the maximum number of connections to your MySQL database to **151**. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. After the connection limit is reached, any attempts at starting a new connection results in an error. To prevent overwhelming your deployment with connections, use connection pooling, or scale your deployment and increase its connection limit. For more information, see the [Managing MySQL Connections](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-managing-mysql-connections) page.

## Bandwidth
{: #bandwidth}

{{site.data.keyword.databases-for-mysql}} deployments are provisioned with a default network bandwidth of **100 MB/s**. This bandwidth allocation is designed to handle typical workload requirements.

You can request a bandwidth increase by [opening a support ticket](https://cloud.ibm.com/login?redirect=%2Funifiedsupport%2Fsupportcenter) if your application requires higher network throughput due to:

- High-volume data transfers
- Large number of concurrent connections
- Intensive read/write operations
- Data replication requirements

The IBM Cloud support team will evaluate your requirements and adjust the bandwidth allocation accordingly.
