---

copyright:
  years: 2026
lastupdated: "2026-06-22"

keywords: deployment, crn, task, gui, api endpoint, mysql connection strings, mysql, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# The Dashboard overview
{: #dashboard-overview}

[Gen 2]{: tag-purple}

The **Overview** page shows you information about your {{site.data.keyword.databases-for-mysql_full}} deployment. The overview includes essential identifying information.

## Overview
{: #dashboard-overview-overview}

### Deployment details
{: #console-overview-deployment-details}

- **Type:** The type of database that is offered by the service and the database version that your service uses.
- **Version:** Displays the current version of your deployed database.
- **Encryption details:** Shows the encryption configuration for your backup and disk. These settings are fixed and cannot be changed after deployment.
- **Location:** Specifies the region where your database is deployed.
- **Platform:** Displays the deployment generation. For more information, see [Overview of Gen 1 and Gen 2](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-overview-gen1-gen2&interface=ui).
- **Hosting model:** Displays the hosting model used for your database (for example, [Isolated compute on Gen 2](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute&interface=ui)).
- **Resource group:** Shows the resource group selected during deployment.
- **CRN (deployment ID):** The ID is a [CRN (Cloud Resource Name)](/docs/account?topic=account-crn){: external} that uniquely identifies the database deployment. The CRN is used to refer to the database in the API and can be used with the CLI. The **Overview** panel shows details of your service.

## Resources
{: #dashboard-overview-resources}

The resources tab contains information and configuration options on the size and resource usage of your deployment. You can [scale disk, memory, and CPU](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-resources-scaling).

### Most recent tasks
{: #console-overview-recent-tasks}

Every time you make administrative changes to your service (such as scaling, or taking a manual backup), a task starts up. The _Most recent tasks_ panel shows only the **latest task**, including its name and progress bar. After completion, the most recent task remains visible for a short period of time.

To view more than the latest task, you can configure an [{{site.data.keyword.logs_full}}](/docs/cloud-logs?topic=cloud-logs-getting-started){: external} instance to capture and retain logs from your deployment.

A historical record of tasks from any time period is available through [{{site.data.keyword.atracker_full}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-at_events). Tasks can also be retrieved programmatically from the [{{site.data.keyword.databases-for}} API](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-api){: external} and [CLI plug-in](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference){: external}.

### Observability
{: #console-overview-observability}

The _Observability_ tab provides access to the {{site.data.keyword.monitoringlong}}, logging, and event tracking integrations available for your deployment.

- [{{site.data.keyword.logs_full}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-logging)
- [{{site.data.keyword.monitoringfull}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-monitoring)

### Service endpoints
{: #console-overview-endpoints}

The **Service endpoints** pane within the **Overview** pane contains connection strings for your deployment.

For Gen 2 deployments, endpoints are **private only**, ensuring that your database is accessible exclusively within your {{site.data.keyword.cloud}} private network.

The information displayed includes:

- Endpoint string for connection.
- The _hostname_ and _port_ values for direct connections to the database members.
- The default database for your service.
- SSL mode parameters. Gen 2 uses certificates from *Let's Encrypt* instead of proprietary TLS certificates. The recommended setting is `verify-full` for secure connections.

Gen 2 deployments do not include a pre-provisioned database admin password. To begin working with your instance, you must [create a service credential](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-user-management), which provides the authentication details and connection strings required for your applications.
For more information on reference tables for the different connection types, see [Getting credentials and connection strings](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connection-strings).
{: note}

You can manage your {{site.data.keyword.databases-for-mysql}} service through the [{{site.data.keyword.databases-for}} API](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-api).

## Connect page
{: #console-connect}

In Gen 2 deployments, only private endpoints are used to enhance security. This page provides multiple connection options, accessible via tabs:

- **How-to tab**: Learn how to set up a secure connection to your database from scratch. This guide walks you through connecting through an {{site.data.keyword.cloud}} Virtual Private Cloud (VPC) by using a Virtual Server Instance (VSI) with a `mysqlsh` install, and accessing the database through an {{site.data.keyword.cloud}} VPN (Virtual Private Network gateway). You can use any VSI in your account and need to create or use a service credential. You use the service endpoint details from the **Overview** page.

- **Manage connections tab**: Find links to documentation on how to manage or delete existing connections from your {{site.data.keyword.cloud}} database instance.

For detailed ways of connecting, go to the connect docs or open the console's (UI) Connect page.

## Backups and restore
{: #console-overview-backups-and-restore}

The _Backups and restore_ tab is the UI for managing your deployments backups. All of the available backups are listed with their timestamps. Click a backup to copy its ID or to restore it into a new deployment. For more information, see [Managing backups](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-independent-backups).

## Settings
{: #console-overview-settings}

The _Settings_ tab contains the UI for many of the tunable settings for your deployment.

- **View encryption details:** All {{site.data.keyword.databases-for-mysql}} deployments are automatically encrypted at rest. Disks and backups are encrypted, and the encryption keys are managed automatically by {{site.data.keyword.cloud}}. If you brought your own encryption key from [{{site.data.keyword.keymanagementserviceshort}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-key-protect&interface=ui), the panel provides a link to your {{site.data.keyword.keymanagementserviceshort}} instance and displays the key name in the _Encryption key_ field.
- **Service endpoints:** View and manage network access to your deployment. By default, only private endpoints are enabled to restrict access. For more information, see [Private endpoints on Gen 2](/docs/databases-for-mysql?topic=databases-for-mysql-service-endpoints&interface=ui).
- **Context-based restrictions (CBR):** You can create context-based restriction rules to control access to your deployment.
- **Disconnect all active connections:** You can disconnect all client connections to your database. This action immediately ends all active sessions and can cause service interruptions. Use this option with caution when you need to revoke access or apply configuration changes.

## Service credentials
{: #console-overview-service-cred}

Service credentials provide the authentication details that you need to connect your applications to a {{site.data.keyword.databases-for-mysql}} deployment.

For Gen 2 deployments, you must create a service credential before you can begin working with your instance. Unlike Gen 1, there is no pre-provisioned database admin password. On first use (or if a credential has been deleted), the **Overview** page prompts you to create a credential in order to connect.

### Creating a service credential
{: #create-service-cred}

1. Go to the **Service credentials** tab.
2. Click **Create credential**.
3. Optional: Enable **Control by Secrets Manager** to integrate the credential with your {{site.data.keyword.secrets-manager_full}} instance.
4. Provide a name for the credential.
5. Optional: Expand **Advanced options** to upload a JSON file or add inline configuration parameters.
6. Click **Create**.

The new credential appears in the list and can be copied into your applications. Credentials are generated as **one-time view only**, so be sure to store them securely after creation.

### Using service credentials
{: #using-service-cred}

In Gen 2 deployments, **service credentials are required as the first step to connect**. Unlike Gen 1, an admin password is not provided by default. If no credential exists, you are prompted to create one on the **Overview** page before you can establish any database connection.
{: note}

- A service credential contains the username, password, and connection strings needed to connect to your database.
- The credentials can be used directly in your application, or integrated with {{site.data.keyword.secrets-manager_full}} for centralized key management.
- If you delete a credential, you must create a new one to continue connecting.

For more information, see [Managing service credentials](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-user-management&interface=ui).
