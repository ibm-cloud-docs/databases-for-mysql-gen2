---

copyright:
  years: 2026
lastupdated: "2026-05-21"

keywords: mysql workbench, mysql gui, mysql, gen2

subcollection: databases-for-mysql-gen2

content-type: tutorial
account-plan: paid
completion-time: 30m

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with {{site.data.keyword.databases-for-mysql}}
{: #getting-started}
{: toc-content-type="tutorial"}
[Gen 2]{: tag-purple}
{: toc-completion-time="30m"}

This tutorial will guide you through deploying and managing {{site.data.keyword.databases-for-mysql_full}} on IBM Cloud. With MySQL Workbench, an open-source tool, you can easily manage your data and databases.

MySQL Workbench provides many tools to help you manage your database effortlessly so you can focus on building and scaling your applications.
{: tip}

## Before you begin
{: #mysql-prereqs}

- Create an [IBM Cloud account](https://cloud.ibm.com/registration).
- Provision {{site.data.keyword.databases-for-mysql}} from the [IBM Cloud catalog](https://cloud.ibm.com/catalog/services/databases-for-mysql-gen2).
- Configure the [admin password](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-user-management&interface=ui#user-management-set-admin-password-ui) for secure access.
- Download and install [MySQL Workbench](https://dev.mysql.com/downloads/workbench/){: .external}.
- Review the [Getting to production guide](/docs/cloud-databases?topic=cloud-databases-getting-to-production&interface=ui) for optimal configuration.

## Connect to your database
{: #connect-to-database}
{: step}

Set up your connection to {{site.data.keyword.databases-for-mysql_full}} by performing the following steps:

1. Open MySQL Workbench.
2. Add a new connection with your database details.
3. Save the connection settings and connect to your database.

For detailed instructions, see the [MySQL Workbench documentation](https://dev.mysql.com/doc/workbench/en/wb-mysql-connections.html){: .external}.

## Provision a MySQL deployment
{: #mysql-deployment}
{: step}

To create your MySQL deployment:

1. Log in to the IBM Cloud Console.
2. Go to [Databases for MySQL Service](https://cloud.ibm.com/catalog/services/databases-for-mysql-gen2) in the catalog.
3. Configure the following:
   - **Service name**: Choose a memorable name for your deployment.
   - **Resource group**: Select the appropriate resource group.
   - **Location:** The deployment's public cloud region.
   - **Resource allocation**: Define initial RAM, disk, and CPU resources.
4. Click **Create** to finalize the deployment.

Once created, your deployment automatically scales and manages availability across zones for resilience.
{: tip}

## Connect to your database with the CLI
{: #mysql-connect-db-cli}
{: cli}
{: step}

To connect to your database from the CLI, see the [Cloud Databases CLI reference](https://cloud.ibm.com/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference) and [Connecting with mysql](https://dev.mysql.com/doc/workbench/en/wb-mysql-connections.html){: external}.

The `ibmcloud cdb deployment-connections` command handles everything involved in creating a command-line client connection. For example, to connect to a deployment named `example-mysql`, use the following command:

```sh
ibmcloud cdb deployment-connections example-mysql --start
```
{: pre}

The command prompts for the admin password and then runs the `mysql` command-line client to connect to the database. For more information, see [Connecting with mysql](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connecting-mysql).

## Connect with MySQL Workbench
{: #mysql-connect-db-workbench}
{: step}

Use MySQL Workbench to manage and interact with your MySQL database visually.

1. Open MySQL Workbench.
2. Create a new connection:
   - Go to **Database > Manage connections** and add a new connection.
   - Fill in your connection details (hostname, username, and password).
3. Test the connection:
   - Click **Test connection** to ensure everything is set up correctly.
4. Save and connect:
   - Save your connection settings and proceed to connect to your database.

For more information, see [Connections in MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-mysql-connections.html){: .external}.

## Product overview
{: #mysql-product-overview}

{{site.data.keyword.databases-for-mysql_full}} Gen 2 is a fully managed cloud database service built on MySQL 8.4 with long-term support until April 2029. It offers the following main benefits:

- **MySQL 8.4 Support:** Full protocol compatibility with MySQL 8.4, providing the latest features and performance improvements with extended support.
- **Automated maintenance:** No manual software, infrastructure, network or OS administration is required.
- **High availability:** 2-node cluster with Regional File Storage (RFS) for automatic failover with zero data loss (RPO = 0).
- **Scalability:** Independently scale disk, RAM, and vCPU with hourly billing. Isolated Compute only at launch.
- **Storage-based replication:** Synchronous replication at the storage layer ensures data consistency and eliminates replication lag.
- **Security:** Isolated Compute environment with private endpoints only for enhanced security and network isolation.

## Key features
{: #mysql-key-features}

- **Regional File Storage (RFS):** Single regional storage solution providing data resiliency across all availability zones with synchronous replication.
- **Horizontal scaling:** Easily scale instances with read replicas for read-heavy workloads, both regionally and cross-regionally.
- **Disaster recovery:** Built-in snapshot-based backups with 30-day retention and cross-regional disaster recovery options.
- **Isolated Compute:** Dedicated resources with hypervisor-level isolation for enhanced security and performance.
- **Native IBM Cloud integration:** Seamless integration with IAM, Key Protect, Activity Tracker, and Cloud Logs.

For more information on isolation settings, see the [Security and Compliance section](/docs/cloud-databases?topic=cloud-databases-manage-security-compliance).
{: note}

## Next steps
{: #mysql-next-steps}

- If you are using MySQL for the first time, see the [MySQL 8.4 reference manual](https://dev.mysql.com/doc/refman/8.4/en/){: .external}.
- You can connect, manage your databases, and manage data with MySQL's command-line interface (CLI) tool [`mysql`](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connecting-mysql).
- To manage your deployment, connect to your deployment with the [IBM Cloud CLI](/docs/cli?topic=cli-install-ibmcloud-cli), the [Cloud Databases CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference), or the [Cloud Databases API](https://cloud.ibm.com/apidocs/cloud-databases-api).
- If you plan to use {{site.data.keyword.databases-for-mysql}} for your applications, check out [Connecting an external application](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-external-app) and [Connecting an IBM Cloud application](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-ibmcloud-app).
- To ensure the stability of your applications and your database, check out [High availability](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-ha-dr) and [Performance](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-performance).
