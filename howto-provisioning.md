---

copyright:
  years: 2026
lastupdated: "2026-08-24"

keywords: provision cloud databases, terraform, provisioning parameters, cli, resource controller api, provision mysql, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Provisioning
{: #provisioning}

[Gen 2]{: tag-purple}

Provision a {{site.data.keyword.databases-for-mysql_full}} deployment through the [catalog](https://cloud.ibm.com/databases/databases-for-mysql-gen2/create){: external}, the [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-cdb-reference), the [{{site.data.keyword.databases-for}} API](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-api), or through [Terraform](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database){: external}.



## Provisioning through the {{site.data.keyword.cloud_notm}} console
{: #catalog}
{: ui}

Deploy from the console by specifying the following parameters:

### Location and platform
{: #location_and_platform}
{: ui}

- **Location** - Choose the region where you want to deploy your database. Each region in the list shows which platform it supports, [Gen 1 or Gen 2](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-overview-gen1-gen2) to help guide your selection.
- **Platform** - Select the platform you want to deploy your database on. Available options depend on the region you choose. For more information on the differences between Gen 1 and Gen 2 , see [Overview of Gen 1 and Gen 2](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-overview-gen1-gen2).

### Service details
{: #service_details}
{: ui}

- **Service name:** The name can be any string and is the name that is used on the web and in the CLI to identify the new deployment.
- **Resource group:** If you are organizing your services into [resource groups](/docs/account?topic=account-account_setup){: external}, specify the resource group in this field. Otherwise, you can leave it at default. For more information, see [Managing resource groups](/docs/account?topic=account-rgs){: external}.
- **Location:** The deployment's public cloud region.

Select the platform for your deployment.

- **Gen 1:** {{site.data.keyword.cloud}}’s original platform, available across all IBM Cloud data centers.
- **Gen 2:** {{site.data.keyword.cloud}}'s latest platform, based on a highly secure software-defined networking architecture. Gen 2 is optimized for cloud-native applications.

For more details, see [Overview of Gen 1 (Classic) and Gen 2 (VPC)](/docs-draft/cloud-databases-gen2?topic=cloud-databases-gen2-overview-gen1-gen2&interface=ui).

### Hosting model
{: #hosting_model}
{: ui}

Gen 2 supports **Isolated Compute only**. Isolated Compute provides a secure single-tenant offering with dedicated resources and hypervisor-level isolation for complex, highly-performant enterprise workloads. For more information, see [Isolated Compute](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute&interface=ui).

### Resource allocation
{: #resource_allocation}
{: ui}

Choose the initial host size and disk configuration for your deployment. Host size and disk allocation apply per instance member.
Use the table to choose the available configurations of vCPU and RAM to match your workload needs.

#### Fixed host flavors
{: #fixed-host-flavors-ui}

| Member Host flavor | vCPU x RAM  | host_flavor value          |
|-----------|----------------------|------------------------ ---|
| 4x20      | 4 vCPU x 20 GB RAM   | bx3d.4x20.encrypted        |
| 8x40      | 8 vCPU x 40 GB RAM   | bx3d.8x40.encrypted        |
| 16x80     | 16 vCPU x 80 GB RAM  | bx3d.16x80.encrypted       |
| 32x160    | 32 vCPU x 160 GB RAM | bx3d.32x160.encrypted      |
| 48x240    | 48 vCPU x 240 GB RAM | bx3d.48x240.encrypted      |
{: caption="Fixed host flavor sizing parameter" caption-side="bottom"}

#### Flex host flavors
{: #flex-host-flavors-ui}

| Member Host flavor | vCPU x RAM  | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x16      | 4 vCPU x 16 GB RAM   | bxf.4x16.encrypted        |
| 8x32      | 8 vCPU x 32 GB RAM   | bxf.8x32.encrypted        |
| 16x64     | 16 vCPU x 64 GB RAM  | bxf.16x64.encrypted       |
| 32x128    | 32 vCPU x 128 GB RAM | bxf.32x128.encrypted      |
{: caption="Flex host flavor sizing parameter" caption-side="bottom"}

**Disk:** Configure the initial disk size for your deployment. Use the slider to set disk capacity.

Disk size can be increased after provisioning but cannot be decreased to prevent data loss.
{: note}

### Service configuration
{: #service_configuration}
{: ui}

- **Database version:** [Set only at deployment]{: tag-red} The deployment version of your database. The console highlights the preferred version to ensure optimal performance. Gen 2 supports MySQL 8.4. The latest minor version is used automatically. For more information, see [Versioning policy](/docs/cloud-databases?topic=cloud-databases-versioning-policy){: external}.
- **Encryption:** [Set only at deployment]{: tag-red} If you use [Key Protect](/docs/cloud-databases?topic=cloud-databases-key-protect&interface=ui), an instance and key can be selected to encrypt the deployment's disk. If you do not use your own key, the deployment automatically creates and manages its own disk encryption key.
- **Endpoints:** [Set only at deployment]{: tag-red} - Gen 2 supports **private endpoints only**.

After you select the appropriate settings, click **Create** to start the provisioning process.

## Provisioning through the CLI
{: #use-cli}
{: cli}

### Create a service instance through the CLI
{: #create-service-instance-cli}
{: cli}

Before provisioning, follow the instructions provided in the documentation to install the [{{site.data.keyword.cloud_notm}} CLI tool](/docs/cli?topic=cli-install-ibmcloud-cli){: external}.

1. Log in to {{site.data.keyword.cloud_notm}}. If you use a federated user ID, it's important that you switch to a one-time passcode (`ibmcloud login --sso`), or use an API key (`ibmcloud --apikey key or @key_file`) to authenticate. For more information about how to log in by using the CLI, see [General CLI (ibmcloud) commands](/docs/cli?topic=cli-ibmcloud_cli#ibmcloud_login){: external} under `ibmcloud login`.

    ```sh
    ibmcloud login
    ```
    {: pre}

2. Gen 2 supports **Isolated Compute only**. Select the desired Isolated Compute size for your deployment.
3. Provision your database with the following command:

   ```sh
   ibmcloud resource service-instance-create <INSTANCE_NAME> <SERVICE_NAME> <SERVICE_PLAN_NAME> <LOCATION> <RESOURCE_GROUP> -p '{"host_flavor": "<host_flavor value>"}' --service-endpoints="private"
   ```
   {: pre}

   For example, to provision a {{site.data.keyword.databases-for-mysql}} Gen 2 Isolated Compute instance, use a command like:

   ```sh
   ibmcloud resource service-instance-create test-instance databases-for-mysql standard-gen2 in-che -g Default -p '{"dataservices": {"mysql": {"storage_gb": 10, "members": 2, "host_flavor": "bxf.4x16"}}}'
   ```
   {: pre}

   Available hosting sizes and their `host_flavor value` parameters are listed in [Table 2](#host-flavor-parameter-cli). For example, `{"host_flavor": "b3c.4x16.encrypted"}` provisions 4 CPU and 16 GB RAM. Note that since the host flavor selection includes CPU and RAM sizes, this request does not accept both an Isolated size selection and separate CPU and RAM allocation selections.

   Gen 2 supports private endpoints only. The `--service-endpoints` parameter must be set to `"private"`.
   {: important}

   The fields in the command are described in the table that follows.

   | Field | Description | Flag |
   |-------|------------|------------|
   | `INSTANCE_NAME` [Required]{: tag-red} | The instance name can be any string and is the name that is used on the web and in the CLI to identify the new deployment. |  |
   | `SERVICE_NAME` [Required]{: tag-red} | Name or ID of the service. For {{site.data.keyword.databases-for-mysql}}, use `databases-for-mysql`. |  |
   | `SERVICE_PLAN_NAME` [Required]{: tag-red} | Standard plan (`standard-gen2`) |  |
   | `LOCATION` [Required]{: tag-red} | The location where you want to deploy. To retrieve a list of regions, use the `ibmcloud regions` command. |  |
   | `RESOURCE_GROUP` | The Resource group name. The default value is `default`. | -g |
   | `--parameters` | JSON file or JSON string of parameters to create service instance | -p |
   | `host_flavor` | To provision an Isolated Compute instance, use `{"host_flavor": "<host_flavor value>"}`. For Isolated Compute, select the desired CPU and RAM configuration. For more information, see the following [Table 2](#host-flavor-parameter-cli).| |
   {: caption="Basic command format fields" caption-side="top"}

      You will see a response like:

   ```text
   Creating service instance INSTANCE_NAME in resource group default of account    USER...
   OK
   Service instance INSTANCE_NAME was created.

   Name:                   INSTANCE_NAME
   ID:                     crn:v1:bluemix:public:databases-for-mysql:in-che:a/cf8d4161fa0243b9a2a5494cd7ff66b7:d79c8a28-cfe0-4e25-8952-16aaa11cc5f0::
   GUID:                   d79c8a28-cfe0-4e25-8952-16aaa11cc5f0
   Location:               in-che
   State:                  provisioning
   Type:                   service_instance
   Sub Type:               Public
   Allow Cleanup:          false
   Locked:                 false
   One-time credentials:   true
   Created at:             2026-06-15T10:36:00Z
   Updated at:             2026-06-15T10:36:01Z
   Last Operation:
                        Status    create in progress
                        Message   Started create instance operation
   ```
   {: codeblock}

   - To check provisioning status, use the following command:

      ```sh
      ibmcloud resource service-instance <INSTANCE_NAME>
      ```
      {: pre}

      When complete, you will see a response like:

      ```text
      Retrieving service instance INSTANCE_NAME in resource group default under account USER's Account as USER...
      OK

      Name:                   INSTANCE_NAME
      ID:                     crn:v1:bluemix:public:databases-for-mysql:in-che:a/cf8d4161fa0243b9a2a5494cd7ff66b7:d79c8a28-cfe0-4e25-8952-16aaa11cc5f0::
      GUID:                   d79c8a28-cfe0-4e25-8952-16aaa11cc5f0
      Location:               in-che
      Service Name:           databases-for-mysql
      Service Plan Name:      standard-gen2
      Resource Group Name:    Default
      State:                  active
      Type:                   service_instance
      Sub Type:               Public
      Locked:                 false
      One-time credentials:   true
      Created at:             2026-06-14T08:48:02Z
      Created by:             USER
      Updated at:             2026-06-14T09:09:20Z
      Last Operation:
                              Status    create succeeded
                              Message   Provision completed successfully
      ```
      {: codeblock}

    - (Optional) Delete an instance by running a command like this one:

      ```sh
      ibmcloud resource service-instance-delete <INSTANCE_NAME_OR_CRN>
      ```
      {: pre}

### The `host_flavor` parameter
{: #host-flavor-parameter-cli}
{: cli}

The `host_flavor` parameter defines your Compute sizing. Gen 2 supports **Isolated Compute only**. Input the appropriate value for your desired CPU and RAM configuration.

#### Fixed host flavors
{: #fixed-host-flavors-cli}

| Host size | vCPU x RAM           | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x20      | 4 vCPU x 20 GB RAM   | bx3d.4x20.encrypted        |
| 8x40      | 8 vCPU x 40 GB RAM   | bx3d.8x40.encrypted        |
| 16x80     | 16 vCPU x 80 GB RAM  | bx3d.16x80.encrypted       |
| 32x160    | 32 vCPU x 160 GB RAM | bx3d.32x160.encrypted      |
| 48x240    | 48 vCPU x 240 GB RAM | bx3d.48x240.encrypted      |
{: caption="Fixed host flavor sizing parameter" caption-side="bottom"}

#### Flex host flavors
{: #flex-host-flavors-cli}

| Host size | vCPU x RAM           | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x16      | 4 vCPU x 16 GB RAM   | bxf.4x16.encrypted        |
| 8x32      | 8 vCPU x 32 GB RAM   | bxf.8x32.encrypted        |
| 16x64     | 16 vCPU x 64 GB RAM  | bxf.16x64.encrypted       |
| 32x128    | 32 vCPU x 128 GB RAM | bxf.32x128.encrypted      |
{: caption="Flex host flavor sizing parameter" caption-side="bottom"}

CPU and RAM autoscaling is not supported on {{site.data.keyword.databases-for}} Isolated Compute at GA. If you have provisioned an Isolated instance or switched over from a deployment with autoscaling, keep an eye on your resources using [{{site.data.keyword.monitoringfull}} integration](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-monitoring){: external}, which provides metrics for memory and disk space. To add resources to your instance, manually scale your deployment.
{: note}

### The `--parameters` parameter
{: #flags-params-service-endpoints}
{: cli}

The `service-instance-create` command supports a `-p` flag, which allows JSON-formatted parameters to be passed to the provisioning process. For example, you can pass Cloud Resource Names (CRNs) as parameter values, which uniquely identify a resource in the cloud. All parameter names and values are passed as strings.

For example, if a database is being provisioned from a particular backup and the new database deployment needs a total of 12 GB of memory across three members, then the command to provision 4 GBs per member looks like:

```sh
ibmcloud resource service-instance-create databases-for-mysql <SERVICE_NAME> standard-gen2 us-south \
-p \ '{
  "backup_id": "crn:v1:blue:public:databases-for-mysql-gen2:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
"host_flavor": "bx3d.16x80.encrypted"
```
{: pre}

## Provisioning through the Resource Controller API
{: #provision-controller-api}
{: api}

Follow these steps to provision by using the [Resource Controller API](https://cloud.ibm.com/apidocs/resource-controller/resource-controller){: external}.

1. Obtain an [IAM token](https://cloud.ibm.com/apidocs/resource-controller/resource-controller#authentication){: external}.

2. You need to know the ID of the resource group that you would like to deploy to. Use this command to obtain a list of resource groups in your account:

    ```sh
    curl -X GET "https://resource-controller.cloud.ibm.com/v2/resource_groups?account_id=<YOUR_ACCOUNT>" -H "Authorization: Bearer <TOKEN>"
    ```
    {: pre}

3. You can provision {{site.data.keyword.databases-for-mysql_full}} instances on the [Isolated Compute](/docs-draft/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute&interface=ui) hosting model on the Gen 2 platform.

    As shown, the Isolated Compute host flavors available to a {{site.data.keyword.databases-for-mysql}} instance are:

    - `bx3d.4x20.encrypted`
    - `bx3d.8x40.encrypted`
    - `mx3d.8x80.encrypted`
    - `bx3d.16x80.encrypted`
    - `bx3d.32x160.encrypted`
    - `bx3d.48x240.encrypted`

    See below for more information about the `host_flavor` parameter.

5. After you have all the information, [provision a new resource instance](https://cloud.ibm.com/apidocs/resource-controller/resource-controller#create-resource-instance){: external} with the {{site.data.keyword.cloud_notm}} Resource Controller.

    ```sh
    curl -X POST \
      https://resource-controller.cloud.ibm.com/v2/resource_instances \
      -H "Authorization: Bearer <TOKEN>" \
      -H 'Content-Type: application/json' \
        -d '{
        "name": "<INSTANCE_NAME>",
        "target": "<targeted-region>",
        "resource_group": "RESOURCE_GROUP_ID",
        "resource_plan_id": "<SERVICE_PLAN_NAME>",
        "dataservices": {
          "<db-service>":{
            "host_flavor": "<members_host_flavor_value>",
          }
        }
      }'
    ```
    {: pre}

    ### Example
    {: api}

    To make an Isolated Compute instance, follow this example:

    ```sh
      curl -X POST https://resource-controller.cloud.ibm.com/v2/resource_instances -H "Authorization: Bearer <IAM token>" -H 'Content-Type: application/json' -d '{
      "name": "my-instance",
      "target": "eu-gb",
      "resource_group": "5c49eabc-f5e8-5881-a37e-2d100a33b3df",
      "resource_plan_id": "databases-for-mysql-gen2-standard",
      "dataservices": {
        "mysql": {
            "storage_gb": 10,
            "host_flavor": "bx3d.4x20.encrypted"
          },
        "encryption": {
          "disk": "crn:v1..."
        },
        "version": "8.4",
      },
    }'
    ```
    {: pre}

    Provision a {{site.data.keyword.databases-for-mysql}} Isolated instance with the same `host_flavor` parameter, setting it to the desired Isolated size. Available hosting sizes and their `host_flavor value` parameters are listed in [Table 2](#members_host-flavor-parameter-api). For example, `{"host_flavor": "bx3d.4x20.encrypted"}`.

Since the members host flavor selection includes CPU and RAM sizes (`bx3d.4x20.encrypted` is 4 CPU and 20 RAM), this request does not accept both an Isolated size selection and separate CPU and RAM allocation selections.
{: note}

   The fields in the command are described in the table that follows.

   | Field | Description | Flag |
   |-------|------------|------------|
   | `name` [Required]{: tag-red} | The instance name can be any string and is the name that is used on the web and in the CLI to identify the new deployment. |  |
   | `resource_plan_id` [Required]{: tag-red} | Name or ID of the service. For {{site.data.keyword.databases-for-mysql}}, use `databases-for-mysql-gen2-standard`. |  |
   | `target` [Required]{: tag-red} | The location where you want to deploy. To retrieve a list of regions, use the `ibmcloud regions` command. |  |
   | `resource_group` [Required]{: tag-red} | The Resource group name. The default value is `default`. | -g |
   | `--parameters` | JSON file or JSON string of parameters to create service instance. See following information for more details. | -p |
   | `host_flavor` | To provision an Isolated Compute instance, use `{"host_flavor": "<members_host_flavor value>"}`. For Isolated Compute, select desired CPU and RAM configuration. For more information, see the following table.| |
   {: caption="Basic command format fields" caption-side="top"}

### The `host_flavor` parameter
{: #host-flavor-parameter-api}
{: api}

The `host_flavor` parameter defines your Compute sizing. To provision an Isolated Compute instance, input the appropriate value for your desired CPU and RAM configuration.

#### Fixed host flavors
{: #fixed-host-flavors-api}

| Member Host flavor | vCPU x RAM           | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x20      | 4 vCPU x 20 GB RAM   | bx3d.4x20.encrypted        |
| 8x40      | 8 vCPU x 40 GB RAM   | bx3d.8x40.encrypted        |
| 16x80     | 16 vCPU x 80 GB RAM  | bx3d.16x80.encrypted       |
| 32x160    | 32 vCPU x 160 GB RAM | bx3d.32x160.encrypted      |
| 48x240    | 48 vCPU x 240 GB RAM | bx3d.48x240.encrypted      |
{: caption="Fixed host flavor sizing parameter" caption-side="bottom"}

#### Flex host flavors
{: #flex-host-flavors-api}

| Member Host flavor | vCPU x RAM           | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x16      | 4 vCPU x 16 GB RAM   | bxf.4x16.encrypted        |
| 8x32      | 8 vCPU x 32 GB RAM   | bxf.8x32.encrypted        |
| 16x64     | 16 vCPU x 64 GB RAM  | bxf.16x64.encrypted       |
| 32x128    | 32 vCPU x 128 GB RAM | bxf.32x128.encrypted      |
{: caption="Flex host flavor sizing parameter" caption-side="bottom"}

CPU and RAM autoscaling is not supported on {{site.data.keyword.databases-for}} Isolated Compute. If you have provisioned an Isolated instance or switched over from a deployment with autoscaling, keep an eye on your resources using [{{site.data.keyword.monitoringfull}} integration](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-monitoring), which provides metrics for memory and disk space. To add resources to your instance, manually scale your deployment.
{: note}

### List of additional parameters
{: #provisioning-parameters-api}
{: api}

In the `--parameters` object you can provide additional information to create your service instance, including:

- `backup_id` - A CRN of a backup resource to restore from. The backup must be created by a database deployment with the same service ID. The backup is loaded after provisioning and the new deployment starts up that uses that data. A backup CRN is in the format `crn:v1:<...>:backup:<uuid>`. If omitted, the database is provisioned empty.
- `version` - The version of the database to be provisioned. If omitted, the database is created with the most recent major and minor version.
- `disk_encryption_key_crn` - The CRN of a KMS key ([{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about)), which is then used for disk encryption. A KMS key CRN is in the format `crn:v1:<...>:key:<id>`.
- `backup_encryption_key_crn` - The CRN of a KMS key ([{{site.data.keyword.keymanagementserviceshort}}](/docs/key-protect?topic=key-protect-about)), which is then used for backup encryption. A KMS key CRN is in the format `crn:v1:<...>:key:<id>`.

    To use a key for your backups, you must first [enable the service-to-service delegation](/docs/cloud-databases?topic=cloud-databases-key-protect&interface=api#key-byok).
    {: note}

## Provisioning with Terraform
{: #provisioning-terraform}
{: terraform}

**Before you begin:**

- [Install the Terraform CLI and the IBM Cloud Provider plug-in](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-setup_cli#tf_installation){: external}.
- Make sure you have an [IBM Cloud API key](/docs/iam?topic=iam-userapikey&interface=ui#create_user_key){: external}.

Use Terraform to manage your infrastructure through the [`ibm_database` Resource for Terraform](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database){: external} supports provisioning {{site.data.keyword.databases-for}} deployments.

Select the [hosting model](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-isolated-compute&interface=terraform) you want your database to be provisioned on. You can change this later.

### Provisioning Isolated Compute with Terraform
{: #provisioning-isolated-computer-terraform}
{: terraform}

Provision a {{site.data.keyword.databases-for-mysql}} Isolated instance with the same `"host_flavor"` parameter, setting it to the desired Isolated size. Available hosting sizes and their `host_flavor value` parameters are listed in [Table 1](#host-flavor-parameter-terraform). For example, `{"host_flavor": "bx3d.4x20.encrypted"}`. Note that since the host flavor selection includes CPU and RAM sizes (`bx3d.4x20.encrypted` is 4 CPU and 16 RAM), this request does not accept both an Isolated size selection and separate CPU and RAM allocation selections.

```terraform
data "ibm_resource_group" "group" {
  name = "<your_group>"
}
resource "ibm_database" "<your_database>" {
  name              = "<your_database_name>"
  plan              = "standard"
  location          = "eu-gb"
  service           = "databases-for-mysql"
  resource_group_id = data.ibm_resource_group.group.id
  service_endpoints = "private"
  tags              = ["tag1", "tag2"]
  adminpassword                = "password12"
  group {
    group_id = "member"
    host_flavor {
      id = "bx3d.4x20.encrypted"
    }
    disk {
      allocation_mb = 256000
    }
  }
  users {
    name     = "user123"
    password = "password12"
  }
  allowlist {
    address     = "172.168.1.1/32"
    description = "desc"
  }
}
output "ICD MySQL database connection string" {
  value = "http://${ibm_database.test_acc.ibm_database_connection.icd_conn}"
}
```
{: codeblock}

### The `host flavor` parameter
{: #host-flavor-parameter-terraform}
{: terraform}

The `host_flavor` parameter defines your Compute sizing. To provision an Isolated Compute instance, input the appropriate value for your desired CPU and RAM configuration. See the values in the following tables.

#### Fixed host flavors
{: #fixed-host-flavors-terraform}

| Host size | vCPU x RAM           | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x20      | 4 vCPU x 20 GB RAM   | bx3d.4x20.encrypted        |
| 8x40      | 8 vCPU x 40 GB RAM   | bx3d.8x40.encrypted        |
| 16x80     | 16 vCPU x 80 GB RAM  | bx3d.16x80.encrypted       |
| 32x160    | 32 vCPU x 160 GB RAM | bx3d.32x160.encrypted      |
| 48x240    | 48 vCPU x 240 GB RAM | bx3d.48x240.encrypted      |
{: caption="Fixed host flavor sizing parameter" caption-side="bottom"}

#### Flex host flavors
{: #flex-host-flavors-terraform}

| Host size | vCPU x RAM           | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x16      | 4 vCPU x 16 GB RAM   | bxf.4x16.encrypted        |
| 8x32      | 8 vCPU x 32 GB RAM   | bxf.8x32.encrypted        |
| 16x64     | 16 vCPU x 64 GB RAM  | bxf.16x64.encrypted       |
| 32x128    | 32 vCPU x 128 GB RAM | bxf.32x128.encrypted      |
{: caption="Flex host flavor sizing parameter" caption-side="bottom"}

CPU and RAM autoscaling is not supported on {{site.data.keyword.databases-for}} Isolated Compute. If you have provisioned an Isolated instance or switched over from a deployment with autoscaling, keep an eye on your resources using [{{site.data.keyword.monitoringfull}} integration](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-monitoring), which provides metrics for memory and disk space. To add resources to your instance, manually scale your deployment.
{: note}
