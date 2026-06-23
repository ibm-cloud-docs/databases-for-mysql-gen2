---

copyright:
  years: 2026
lastupdated: "2026-06-23"

keywords: mysql workbench, mysql gui, mysql, gen2, cloud databases, mysql getting started, Gen 2,

subcollection: databases-for-mysql-gen2

content-type: tutorial
account-plan: paid
completion-time: 30m

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with {{site.data.keyword.databases-for-mysql}}
{: #getting-started}
{: toc-content-type="tutorial"}
{: toc-services=""}
{: toc-completion-time="30m"}

[Gen 2]{: tag-purple}

This tutorial guides you through the steps to quickly start using {{site.data.keyword.databases-for-mysql}} on the Gen 2 platform by provisioning an instance, setting up a secure connection through a VSI and VPE, and enabling logging and monitoring.
{: shortdesc}

Follow these steps to complete the tutorial:
{: ui}

* [Before you begin](#prereqs)
* [Step 1: Provision through the console](#provision_instance_ui)
* [Step 2: Create the \`Manager\` user and generate credentials](#admin_pw)

* [Step 3: Create a connection](#private_connect_setup)
* [Step 4: Connect {{site.data.keyword.mon_full_notm}}](#connect_monitoring_ui)
* [Step 5: Connect {{site.data.keyword.atracker_full}}](#mysql_logs)
* [Next Steps](#next_steps)
{: ui}

Follow these steps to complete the tutorial:
{: cli}

* [Before you begin](#prereqs)
* [Step 1: Choose your plan](#choose_plan)
* [Step 2: Create the \`Manager\` user and generate credentials](#admin_pw)

* [Step 3: Create a connection](#private_connect_setup)
* [Step 4: Connect {{site.data.keyword.mon_full_notm}}](#connect_monitoring_ui)
* [Step 5: Connect {{site.data.keyword.atracker_full}}](#mysql_logs)
* [Next Steps](#next_steps)
{: cli}

Follow these steps to complete the tutorial:
{: api}

* [Before you begin](#prereqs)
* [Step 1: Choose your plan](#choose_plan)
* [Step 2: Create the \`Manager\` user and generate credentials](#admin_pw)

* [Step 3: Create a connection](#private_connect_setup)
* [Step 4: Connect {{site.data.keyword.mon_full_notm}}](#connect_monitoring_ui)
* [Step 5: Connect {{site.data.keyword.atracker_full}}](#mysql_logs)
* [Next Steps](#next_steps)
{: api}

Follow these steps to complete the tutorial:
{: terraform}

* [Before you begin](#prereqs)
* [Step 1: Choose your plan](#choose_plan)
* [Step 2: Create the \`Manager\` user and generate credentials](#admin_pw)

* [Step 3: Create a connection](#private_connect_setup)
* [Step 4: Connect {{site.data.keyword.mon_full_notm}}](#connect_monitoring_ui)
* [Step 5: Connect {{site.data.keyword.atracker_full}}](#mysql_logs)
* [Next Steps](#next_steps)
{: terraform}


## Before you begin
{: #prereqs}

- You need to have an [{{site.data.keyword.cloud_notm}} account](https://cloud.ibm.com/registration){: external}.


## Step 1: Provision through the console
{: #provision_instance_ui}
{: ui}

1. Log in to the {{site.data.keyword.cloud_notm}} console.
2. Click the [{{site.data.keyword.databases-for-mysql}} service](https://cloud.ibm.com/databases/databases-for-mysql/create){: external} in the **catalog**.
3. In **Service details**, configure the following:
   - **Location** - Select a location that supports Gen 2.
   - **Service name** - The name can be any string and is the name that is used on the web and in the CLI to identify the new deployment.
   - **Resource group** - If you are organizing your services into [resource groups](/docs/account?topic=account-account_setup){: external}, specify the resource group in this field. Otherwise, you can leave it at default. For more information, see [Managing resource groups](/docs/account?topic=account-rgs&interface=ui){: external}.
4. **Resource allocation** - Select an isolated compute instance with a certain amount of RAM and CPU cores. Changing resource allocation requires selecting a different host size. Once provisioned, disk cannot be scaled down.
5. In **Service configuration**, configure the following:
   - **Database version** [Set only at deployment]{: tag-red} - The deployment version of your database. To ensure optimal performance, run the preferred version. The latest minor version is used automatically and currently the only option for Gen 2 databases.
   - **Encryption** - If you use [Key Protect](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-key-protect&interface=ui), an instance and key can be selected to encrypt the deployment's disk. If you do not use your own key, the deployment automatically creates and manages its own disk encryption key.
6. Click **Create**. The {{site.data.keyword.databases-for}} **Resource list** page opens.
7. When your instance has been provisioned, click the instance name to view more information.

## Step 1: Provision through the CLI
{: #provision_instance_cli}
{: cli}

You can provision a {{site.data.keyword.databases-for-mysql}} instance by using the CLI. If you don't already have it, you need to install the [{{site.data.keyword.cloud_notm}} CLI](https://www.ibm.com/cloud/cli){: external}.

1. Log in to {{site.data.keyword.cloud_notm}} with the following command:

   ```sh
   ibmcloud login
   ```
   {: pre}

   If you use a federated user ID, it's important that you switch to a one-time passcode (`ibmcloud login --sso`), or use an API key (`ibmcloud --apikey key` or `@key_file`) to authenticate. For more information about how to log in by using the CLI, see [General CLI (ibmcloud) commands](/docs/cli?topic=cli-ibmcloud_cli#ibmcloud_login) under `ibmcloud login`.

2. Create a {{site.data.keyword.databases-for-mysql}} instance.

   To create an instance from the CLI, run the following command:

   ```sh
   ibmcloud resource service-instance-create <INSTANCE_NAME> databases-for-mysql standard-gen2 <LOCATION> -g <RESOURCE_GROUP>
   ```
   {: pre}

   The fields in the command are described in the following table.

   | Field | Description | Flag |
   |-------|------------|------------|
   | `INSTANCE_NAME` [Required]{: tag-red} | The instance name can be any string and is the name that is used on the web and in the CLI to identify the new deployment. | |
   | `SERVICE_NAME` [Required]{: tag-red} | Name or ID of the service. For {{site.data.keyword.databases-for-mysql}}, use `databases-for-mysql`. | |
   | `SERVICE_PLAN_NAME` [Required]{: tag-red} | Standard plan (`standard`) | |
   | `LOCATION` [Required]{: tag-red} | The location where you want to deploy. To retrieve a list of regions, use the `ibmcloud regions` command. | |
   | `RESOURCE_GROUP` | The Resource group name. The default value is `default`. | -g |
   | `--parameters` | JSON file or JSON string of parameters to create service instance | -p |
   {: caption="Basic command format fields" caption-side="top"}

## Step 1: Provision through the resource controller API
{: #provision_instance_api}
{: api}

### Using APIs
{: #using_apis}
{: api}

Use the [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#introduction){: external} to work with your {{site.data.keyword.databases-for-mysql}} instance. The resource controller API is used to [provision an instance](#provision_instance_api).

You will need an API key to perform actions via the API. Follow [these steps](/docs/account?topic=account-userapikey#create_user_key){: external} to create an IBM Cloud API key that enables you to use the API to provision infrastructure into your account. You can create up to 20 API keys.

For security reasons, the API key is only available to be copied or downloaded at the time of creation. If the API key is lost, you must create a new API key.
{: note}

Follow [these steps](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-provisioning&interface=api) to provision a {{site.data.keyword.databases-for-mysql}} instance using the Resource Controller API. Obtain an IAM token from your API token.

1. You need to know the ID of the resource group that you would like to deploy to. This information is available through the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-ibmcloud_commands_resource#ibmcloud_resource_groups){: external}.

   Use a command like:

   ```sh
   ibmcloud resource groups
   ```
   {: pre}

2. After you have all the information, [provision a new resource instance](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-api) with the {{site.data.keyword.cloud_notm}} Resource Controller.

   ```sh
   curl -X POST \
     https://resource-controller.cloud.ibm.com/v2/resource_instances \
     -H 'Authorization: Bearer <>' \
     -H 'Content-Type: application/json' \
       -d '{
        "name":"my-instance",
        "target":"ca-mon",
        "resource_group":"5c49eabc-f5e8-5881-a37e-2d100a33b3df",
        "resource_plan_id":"databases-for-mysql-gen2-standard",
        "dataservices":{
           "mysql":{
              "storage_gb":10,
              "host_flavor":"b3c.8x32.encrypted"
           },
           "encryption":{
              "disk":"crn:v1..."
           },
           "version":"8.4"
        }
     }'
   ```
   {: pre}

   The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required.
   {: required}

### List of additional parameters:
{: #provisioning-parameters-api}
{: api}

* `backup_id` - A CRN of a backup resource to restore from. The backup must be created by a database deployment with the same service ID. The backup is loaded after provisioning and the new deployment starts up that uses that data. A backup CRN is in the format `crn:v1:<...>:backup:<uuid>`. If omitted, the database is provisioned empty.
* `version` - The version of the database to be provisioned. If omitted, the database is created with the most recent major and minor version.
* `disk_encryption_key_crn` - The CRN of a KMS key ([{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about)), which is then used for disk encryption. A KMS key CRN is in the format `crn:v1:<...>:key:<id>`.
* `backup_encryption_key_crn` - The CRN of a KMS key ([{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about)), which is then used for backup encryption. A KMS key CRN is in the format `crn:v1:<...>:key:<id>`.

   To use a key for your backups, you must first [enable the service-to-service delegation](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-key-protect&interface=api#key-byok).
   {: note}

* `storage_gb` - Total amount of disk in Gigabytes. If omitted, the default value for the database type is used.

## Step 1: Provision through Terraform
{: #provision_instance_tf}
{: terraform}

Use Terraform to manage your infrastructure through the [\`ibm_database\` Resource for Terraform](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database){: external}.

## Step 2: Create the \`Manager\` user and generate credentials
{: #admin_pw}

### The `Manager` user
{: #admin_like_manager_user}

As part of provisioning a new instance in {{site.data.keyword.cloud}}, you can use the service credential console page to create a user with different roles (Manager and Writer).

{{site.data.keyword.databases-for-mysql}} instances no longer include a default `admin` user. Instead, you create a user with the `Manager` or `Writer` role by using the {{site.data.keyword.cloud}} service credential interface - through the UI or CLI. These users come with necessary credentials to connect to and manage the instance.

The Manager user functions as an admin-like user and is automatically granted necessary privileges to manage the database.

### Change the user password in the UI
{: #user-management-set-manager-password-ui}
{: ui}

Changing the user password is not supported via the {{site.data.keyword.cloud_notm}} console on Gen 2.

### Create the manager user in the CLI
{: #manager_user_set_cli}
{: cli}

Use one of the following commands from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to create the `Manager` user.

```sh
ibmcloud resource service-key-create <service_key_name> Manager --instance-name <instance_name>
```
{: pre}

```sh
ibmcloud resource service-key-create <service_key_name> Manager --instance-id <guid>
```
{: pre}

### Delete the user in the CLI
{: #manager_user_del_cli}
{: cli}

Use the following command from the {{site.data.keyword.cloud_notm}} CLI {{site.data.keyword.databases-for}} plug-in to delete the created user.

```sh
ibmcloud resource service-key-delete <service_key_name>
```
{: pre}

### Change the manager password in the CLI
{: #manager_pw_set_cli}
{: cli}

Changing a user password is not supported via the CLI on Gen 2.




## Step 3: Create a connection
{: #private_connect_setup}


The **Connect** tab in Gen 2 provides guided instructions for creating a secure connection to your {{site.data.keyword.databases-for-mysql}} deployment.

Because Gen 2 supports **private endpoints only**, all connections are established through the {{site.data.keyword.cloud}} private network. The _Create a connection_ view walks you through the required setup to connect securely from your infrastructure, such as a Virtual Server Instance (VSI), by using Virtual Private Endpoint (VPE) gateway.

This guided experience is designed to help you configure a production-ready, secure connection without exposing your database to the public internet.

Also, the sections below provide a clear overview of how a connection is established within the VPC environment.

* [Create a VPC](https://cloud.ibm.com/infrastructure/network/vpcs/) (Virtual Private Cloud): A VPC is your own isolated network within {{site.data.keyword.cloud}} where you can securely run resources.
* [Generate an SSH key](https://cloud.ibm.com/infrastructure/compute/sshKeys/): SSH keys allow you to securely connect to your virtual servers.
* [Provision a Virtual Server Instance (VSI)](https://cloud.ibm.com/infrastructure/compute/vs/): A VSI is your cloud-based server where applications and workloads will run.
* [Reserve a floating IP for your VSI](https://cloud.ibm.com/infrastructure/network/floatingIPs/): A floating IP is a public IP address that lets you access your VSI from the internet.
* [Create a Virtual Private Endpoint (VPE)](https://cloud.ibm.com/infrastructure/network/endpointGateways/): A VPE provides secure, private connectivity to {{site.data.keyword.cloud_notm}} services.

## Step 4: Connect {{site.data.keyword.monitoringlong_notm}} through the console
{: #connect_monitoring_ui}

You can use {{site.data.keyword.monitoringlong}} to get operational visibility into the performance and health of your applications, services, and platforms. {{site.data.keyword.monitoringlong_notm}} provides administrators, DevOps teams, and developers full stack telemetry with advanced features to monitor and troubleshoot, define alerts, and design custom dashboards.

For more information about how to use Monitoring with {{site.data.keyword.databases-for-mysql}}, see [Monitoring integration](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-monitoring&interface=ui).

You cannot connect {{site.data.keyword.monitoringlong_notm}} by using the CLI. Use the console to complete this task.
{: note}

## Step 5: Connect IBM Cloud Logs Activity Tracker
{: #mysql_logs}

{{site.data.keyword.atracker_full}} allows you to view, and audit service activity to comply with corporate policies and industry regulations. {{site.data.keyword.atracker_short}} records user-initiated activities that change the state of a service in IBM Cloud. Use {{site.data.keyword.atracker_short}} to track how users and applications interact with the {{site.data.keyword.databases-for-mysql}} service.

To get up and running with Activity Tracker Event Routing, see [Getting started with Activity Tracker Event Routing](/docs/atracker?topic=atracker-getting-started){: external}.

{{site.data.keyword.atracker_short}} can have only one instance per location. To view events, you must access the web UI of the {{site.data.keyword.atracker_short}} service in the same location where your service instance is available. For more information, see [Launching the web UI](/docs/cloud-logs?topic=cloud-logs-getting-started){: external}.

Events are formatted according to the Cloud Auditing Data Federation (CADF) standard. For more information, see [CADF standard](/docs/atracker?topic=atracker-event){: external}.

You cannot connect {{site.data.keyword.atracker_short}} by using the CLI. Use the console to complete this task.
{: note}

## Next steps
{: #next_steps}

- If you are using MySQL for the first time, see the official [MySQL documentation](https://dev.mysql.com/doc/refman/8.4/en/){: external}.

- For guidance on best practices, see [Best practices for MySQL on the IBM Cloud](https://www.ibm.com/blog/best-practices-for-mysql-on-the-ibm-cloud/){: external}.

- Connect your deployment to [{{site.data.keyword.logs_full}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-logging) and [{{site.data.keyword.monitoringfull}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-monitoring) for observability and alerting.

- Looking for more tools on managing your databases? Connect to your instance with the following tools:

  - [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli){: external}

  - [{{site.data.keyword.databases-for}} CLI plug-in](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cdb-reference){: external}

  - [{{site.data.keyword.databases-for}} API](https://cloud.ibm.com/apidocs/cloud-databases-api){: external}

- To ensure the stability of your applications and databases, see the following topics:

  - [High-availability](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-ha-dr)

  - [Performance](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-performance)
