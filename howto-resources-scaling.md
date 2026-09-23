---
copyright:
  years: 2026
lastupdated: "2026-09-23"

keywords: mysql, scaling, memory, disk bandwidth, CPU, mysql dedicated cores, scaling mysql

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Adding disk, memory, and CPU
{: #resources-scaling}

[Gen 2]{: tag-purple}

To scale an [isolated compute](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-isolated-compute&interface=cli#isolated-compute-sizing) host flavor instance, set the relevant `hostflavor` parameter to the isolated compute size that you want to use, such as "b3c.4x16.encrypted". As this includes vCPU and RAM allocation selections, do not separately select vCPU and RAM.
{: cli}

To scale an [isolated compute](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-isolated-compute&interface=api#isolated-compute-sizing-api) host flavor instance, set the relevant `host_flavor` parameter to the isolated compute size you want to use, such as "b3c.4x16.encrypted". As this includes vCPU and RAM allocation selections, do not separately select vCPU and RAM.
{: api}

To scale an [isolated compute](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-isolated-compute&interface=terraform#isolated-compute-sizing) host flavor instance, set the relevant `host_flavor` parameter to the isolated compute size you want to use, such as "b3c.4x16.encrypted". As this includes CPU and RAM allocation selections, do not separately select vCPU and RAM.
{: terraform}

You can manually adjust the resources available to your {{site.data.keyword.databases-for-mysql_full}} deployment to suit your workload and the size of your data.

## Resource breakdown
{: #resource-breakdown}

{{site.data.keyword.databases-for-mysql}} deployments have two data members in a cluster. Compute resources, including RAM and vCPU, are allocated equally to both members that access a regional storage. Deployments use a regional storage layer with storage-based replication across all the available zones. Storage is allocated once for the deployment and is not doubled per member.

Billing is based on the _total_ resources that are allocated to the service.
{: tip}

### Disk usage
{: #resources-scaling-disk-usage}

Storage shows the amount of disk space that is allocated to your service. {{site.data.keyword.databases-for-mysql}} uses a single storage solution with storage-based replication, so the allocated storage is not split or duplicated across members.



You cannot scale down storage. If your data set size has decreased, you can recover space by backing up and restoring to a new deployment.
{: tip}

### RAM
{: #ram-allocation}

If you find that your queries and database activity suffer from performance issues due to a lack of memory, you can scale the instance to the next available host size. In Gen 2, {{site.data.keyword.databases-for-mysql}} uses only the isolated compute hosting model, so RAM is increased by selecting a larger vCPU x RAM host size.

Adding memory to the total allocation adds memory to both members equally. RAM can be scaled up or down by changing the selected host size.

### vCPU
{: #resources-scaling-cpu}

If you find that your database workloads need more vCPU resources, scale your instance to the host size that matches your resource needs. vCPU and RAM are scaled together through the selected host size.

## Scaling considerations
{: #resources-scaling-consider}

- Scaling up might cause your deployment to restart. If your deployment needs to be moved to a host with more capacity, the deployment is restarted as part of the move.
- Scaling down RAM or vCPU does not trigger restarts.
- Disk cannot be scaled down.
- Drastically scaling up vCPU, RAM, or storage can take longer to run than small resource increases to account for provisioning more underlying hardware resources.
- Scaling operations are logged in [{{site.data.keyword.atracker_full}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-at_events).


## Review current resources and hosting model
{: #review-resources-ui}
{: ui}

In the **Resources** tab, you find both **Hosting model** and **Resource allocations** tiles. These tiles reflect your current resources and hosting model. Select *Configure* to adjust the settings in each tile.

## Scaling in the UI
{: #resources-scaling-ui}
{: ui}

In the **Resources** tab of the UI, select *Configure* on the **Resource allocations** tile. This opens up a panel where you can adjust your resources.

Look for a "Host sizes" table, where you can select the vCPU and RAM configuration per member for your database.

The storage slider is your storage selection for the deployment. Drag the slider or adjust the number in the input box to change the number of GB.

Members is the number of members of your database. For {{site.data.keyword.databases-for-mysql}}, members are set to 2.

Review your total estimated cost in the calculator on the bottom.

After you are done, click *Apply changes* to trigger the scaling operation.

## Review current resources and hosting model
{: #review-resources-cli}
{: cli}

To interact with {{site.data.keyword.databases-for}} on Gen 2 via the CLI you must utilize the IBM Cloud Resource Controller's CLI. For more information, see the [General IBM Cloud CLI (ibmcloud) commands](https://cloud.ibm.com/docs/cli?topic=cli-ibmcloud_cli). To get information about a particular instance, use the following command:

```sh
ibmcloud resource service-instance <INSTANCE_NAME> -o JSON
```
{: pre}

## Resources and scaling in the CLI
{: #resources-scaling-cli}
{: cli}

To update your instance (this includes operations, such as scaling and modifying other parts of your service), use the `ibmcloud resource service-instance-update` command.

```sh
ibmcloud resource service-instance-update <INSTANCE_NAME> -p '<{FIELDS_TO_UPDATE}>'
```
{: pre}

For example, to update the `host_flavor` of a {{site.data.keyword.databases-for-mysql}} instance, use a command like:

```sh
ibmcloud resource service-instance-update test-database databases-for-mysql standard us-south -p '{"host_flavor": "mx3d.8x80.encrypted", "storage_gb": 10 }'
```
{: pre}

### The `host_flavor` parameter
{: #host-flavor-parameter-cli}
{: cli}

Isolated compute offers six size options to choose from.

The `host_flavor` parameter defines your compute sizing. Input the appropriate value for your desired size.

#### Fixed host flavors
{: #fixed-host-flavors-cli}

| Member Host flavor | vCPU x RAM  | host_flavor value          |
|-----------|----------------------|------------------------ ---|
| 4x20      | 4 vCPU x 20 GB RAM   | bx3d.4x20.encrypted        |
| 8x40      | 8 vCPU x 40 GB RAM   | bx3d.8x40.encrypted        |
| 16x80     | 16 vCPU x 80 GB RAM  | bx3d.16x80.encrypted       |
| 32x160    | 32 vCPU x 160 GB RAM | bx3d.32x160.encrypted      |
| 48x240    | 48 vCPU x 240 GB RAM | bx3d.48x240.encrypted      |
{: caption="Fixed host flavor sizing parameter" caption-side="bottom"}

#### Flex host flavors
{: #flex-host-flavors-cli}

| Member Host flavor | vCPU x RAM  | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x16      | 4 vCPU x 16 GB RAM   | bxf.4x16.encrypted        |
| 8x32      | 8 vCPU x 32 GB RAM   | bxf.8x32.encrypted        |
| 16x64     | 16 vCPU x 64 GB RAM  | bxf.16x64.encrypted       |
| 32x128    | 32 vCPU x 128 GB RAM | bxf.32x128.encrypted      |
{: caption="Flex host flavor sizing parameter" caption-side="bottom"}

## Review current resources and hosting model
{: #review-resources-api}
{: api}

The _Foundation endpoint_ that is shown on the _Overview_ panel of your service provides the base URL to access this deployment through the API. Use it with the `/groups` endpoint if you need to manage or automate scaling programmatically.

To view the current and scalable resources on a deployment, use the [/deployments/{id}/groups](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-api) endpoint.

```sh
curl -X GET https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/groups -H 'Authorization: Bearer <>' \
```
{: pre}

## Scaling with the API
{: #resources-scaling-api}
{: api}

API scaling allocations use total allocation values.
{: note }

To scale the `host_flavor` of a deployment to `bx3d.8x40.encrypted`, use the following command:

```sh
curl -X POST https://resource-controller.cloud.ibm.com/v2/resource_instances
-H 'Authorization: Bearer <>'
-H 'Content-Type: application/json'
-d '{
   "name": "my-instance",
    "target": "ca-mon",
    "resource_group": "5c49eabc-f5e8-5881-a37e-2d100a33b3df",
    "resource_plan_id": "databases-for-mysql-standard",
    "dataservices": {
      "resources": {
        "database": {
          "host_flavor": "bx3d.8x40.encrypted"
        }
      }
     }
   }'
```
{: pre}

For more information, see the [API reference](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-api).

### The `host_flavor` parameter
{: #host-flavor-parameter-api}
{: api}

Isolated compute offers six size options to choose from.

The `host_flavor` parameter defines your compute sizing. Choose the appropriate value for your desired size.

#### Fixed host flavors
{: #fixed-host-flavors-api}

| Member Host flavor | vCPU x RAM  | host_flavor value          |
|-----------|----------------------|------------------------ ---|
| 4x20      | 4 vCPU x 20 GB RAM   | bx3d.4x20.encrypted        |
| 8x40      | 8 vCPU x 40 GB RAM   | bx3d.8x40.encrypted        |
| 16x80     | 16 vCPU x 80 GB RAM  | bx3d.16x80.encrypted       |
| 32x160    | 32 vCPU x 160 GB RAM | bx3d.32x160.encrypted      |
| 48x240    | 48 vCPU x 240 GB RAM | bx3d.48x240.encrypted      |
{: caption="Fixed host flavor sizing parameter" caption-side="bottom"}

#### Flex host flavors
{: #flex-host-flavors-api}

| Member Host flavor | vCPU x RAM  | host_flavor value         |
|-----------|----------------------|---------------------------|
| 4x16      | 4 vCPU x 16 GB RAM   | bxf.4x16.encrypted        |
| 8x32      | 8 vCPU x 32 GB RAM   | bxf.8x32.encrypted        |
| 16x64     | 16 vCPU x 64 GB RAM  | bxf.16x64.encrypted       |
| 32x128    | 32 vCPU x 128 GB RAM | bxf.32x128.encrypted      |
{: caption="Flex host flavor sizing parameter" caption-side="bottom"}

## Review current resources and hosting model
{: #review-resources-terraform}
{: terraform}

Review resource allocations to your database by checking your Terraform scripts for `host_flavor` and `storage_gb` values in `parameters_json`.

## Scaling with Terraform
{: #resources-scaling-terraform}
{: terraform}

Terraform scaling allocations are per-member.
{: note }

Before executing a Terraform script on an existing instance, use the `terraform plan` command to compare the current infrastructure state with the desired state defined in your Terraform files. Any alteration to the `resource_group_id`, `service plan`, `version`, `key_protect_instance`, `key_protect_key`, `backup_encryption_key_crn` attributes recreates your instance. For a list of current argument references with the `Forces new resource` specification, see the [ibm_database Terraform Registry](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database){: external}.
{: important}

Scale your instance by adjusting your Terraform script for the resource you're interested in. In the following example, `host_flavor` and `storage_gb` allocations are specified.

To implement your change, run `terraform apply`.

```terraform
data "ibm_resource_group" "default" {
  name = "Default"
}

resource "ibm_resource_instance" "mysql_demo" {
  name              = "mysql-demo"
  service           = "databases-for-mysql"
  plan              = "standard-gen2"
  location          = "ca-mon"
  resource_group_id = data.ibm_resource_group.default.id
  parameters_json = jsonencode(
    {
      "dataservices": {
        "mysql": {
          "storage_gb": 9600,
          "host_flavor": "bx3d.8x40.encrypted"
          "members": 3
        }
      }
    }
  )
}
```
{: codeblock}

## Switching to and scaling hosting models in Terraform
{: #resources-switching-terraform}
{: terraform}

Select the [hosting model](/docs/cloud-databases?topic=cloud-databases-hosting-models) you want your database to be scaled to. You can change this later.

Update the `host_flavor` value in the `parameters_json` section from the example above with the intended value, then run `terraform apply`.
