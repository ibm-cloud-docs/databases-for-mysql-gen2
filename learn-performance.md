---

copyright:
  years: 2021, 2026
lastupdated: "2026-04-01"

keywords: mysql, databases, monitoring, scaling, autoscaling, resources, mysql connection limits

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Performance
{: #performance}

{{site.data.keyword.databases-for-mysql-gen2_full}} deployments can be manually [scaled to your usage](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-resources-scaling#resources-scaling-consider) or configured to [autoscale](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-autoscaling-mysql&interface=cli) under certain resource conditions. There are a few factors to consider when you are tuning the performance of your deployment.

## Monitoring your deployment
{: #monitor-deployment}

{{site.data.keyword.databases-for-mysql-gen2}} deployments offer an integration with the [{{site.data.keyword.monitoringfull}} service](/docs/cloud-databases?topic=cloud-databases-monitoring) for basic monitoring of resource usage on your deployment. Many of the available metrics, like disk usage and IOPS, are presented to help you configure [autoscaling](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-autoscaling-mysql&interface=cli) on your deployment. Observing trends in your usage and configuring the autoscaling to respond to them can help alleviate performance problems before your databases become unstable due to resource exhaustion.

## Disk IOPS
{: #disk-iops}

The number of input/output operations per second (IOPS) is limited by the type of storage volume. Storage volumes for {{site.data.keyword.databases-for-mysql-gen2}} deployments are provisioned on [Block Storage Endurance Volumes in the 10 IOPS per GB tier](/docs/BlockStorage?topic=BlockStorage-orderingBlockStorage&interface=ui). If your operational load saturates or exceeds the IOPS limit, database requests and operations are delayed until the disk can catch up. Extended periods of heavy-load can cause your deployment to be unable to process queries and become effectively unavailable. If you experience delayed responses and failing operations, you might be exceeding the disk's IOPS limit. You can increase the number IOPS available to your deployment by increasing disk space.

## Connection Limits
{: #connection-limits-performance}

{{site.data.keyword.databases-for-mysql-gen2}} sets the maximum number of connections to your MySQL database to **200**. Leave some connections available, as a number of them are reserved internally to maintain the state and integrity of your database. After the connection limit is reached, any attempts at starting a new connection results in an error. To prevent overwhelming your deployment with connections, use connection pooling, or scale your deployment and increase its connection limit. For more information, see the [Managing MySQL Connections](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-managing-mysql-connections) page.
