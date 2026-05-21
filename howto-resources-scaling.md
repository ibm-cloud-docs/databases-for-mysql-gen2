---

copyright:
  years: 2026
lastupdated: "2026-05-21"

keywords: mysql, databases, scaling, memory, disk IOPS, CPU, mysql dedicated cores, sclaing mysql, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Scaling disk, RAM, and CPU
{: #resources-scaling}

[Gen 2]{: tag-purple}

Gen 2 supports **Isolated Compute only** at launch. To scale an Isolated Compute instance, set the relevant `hostflavor` parameter to the Isolated Compute size you're targeting, such as "b3c.4x16.encrypted". As this includes CPU and RAM allocation selections, do not separately select CPU and RAM.

**Gen 2 Scaling Limits:**
- Maximum CPU: 30 vCPU at GA
- Maximum RAM: 240 GB at GA
- Maximum Disk: 9 TB at GA (scaling to 32 TB in Q3 hardening)
- Disk can be scaled up but not down
{: cli}

Gen 2 supports **Isolated Compute only** at launch. To scale an Isolated Compute instance, set the relevant `host_flavor` parameter to the Isolated Compute size you're targeting, such as "b3c.4x16.encrypted". As this includes CPU and RAM allocation selections, do not separately select CPU and RAM.

**Gen 2 Scaling Limits:**
- Maximum CPU: 30 vCPU at GA
- Maximum RAM: 240 GB at GA
- Maximum Disk: 9 TB at GA (scaling to 32 TB in Q3 hardening)
- Disk can be scaled up but not down
{: api}

Gen 2 supports **Isolated Compute only** at launch. To scale an Isolated Compute instance, set the relevant `host_flavor` parameter to the Isolated Compute size you're targeting, such as "b3c.4x16.encrypted". As this includes CPU and RAM allocation selections, do not separately select CPU and RAM.

**Gen 2 Scaling Limits:**
- Maximum CPU: 30 vCPU at GA
- Maximum RAM: 240 GB at GA
- Maximum Disk: 9 TB at GA (scaling to 32 TB in Q3 hardening)
- Disk can be scaled up but not down
{: terraform}

You can manually adjust the amount of resources available to your {{site.data.keyword.databases-for-mysql_full}} deployment to suit your workload and the size of your data.

## Resource breakdown
{: #resources-breakdown}

{{site.data.keyword.databases-for-mysql}} Gen 2 deployments have a 2-node cluster, and resources are allocated to members equally. For example, if you allocate 20 GB of disk, each member gets 20 GB of disk. The total resources allocated to your deployment is 40 GB of disk (20 GB × 2 members).

Billing is based on the _total_ amount of resources that are allocated to the service.
{: tip}

### Disk
{: #resources-disk}

Your disk allocation must be enough to store all of your data. Gen 2 uses Regional File Storage (RFS) with storage-based replication, so both nodes access the same storage. The total amount of disk you allocate is shared across the 2-node cluster.

Disk allocation also affects the performance of the disk, with larger disks having higher performance. Baseline input/output operations per second (IOPS) performance for disk is 10 IOPS for each GB. Scale disk to increase the IOPS that your deployment can handle.

**Gen 2 Disk Limits:**
- Maximum: 9 TB at GA (scaling to 32 TB in Q3 hardening)
- Disk can be scaled up but not down

You cannot scale down storage.
{: tip}

### RAM
{: #resources-ram}

If you find that your deployment is suffering from performance issues due to a lack of memory, you can scale the amount of RAM allocated to it. Gen 2 supports up to 240 GB RAM per member at GA. The amount of memory allocated to the database's shared buffer pool is not adjusted automatically when you scale your deployment. It's recommended to be set to 25% of the deployment's total memory.

**Gen 2 RAM Limits:**
- Maximum: 240 GB per member at GA (30 vCPU × 8 GB RAM per vCPU)
- RAM auto-scaling will be available in Q3 hardening

### vCPU
{: #resources-cores}

If you find that your database workloads need more CPU resources, you can scale the amount of CPU allocated to your service. Gen 2 supports Isolated Compute only. Select the CPU x RAM configuration that matches your resource needs from the available host flavors.

**Gen 2 CPU Limits:**
- Maximum: 30 vCPU per member at GA

## Scaling considerations
{: #resources-scaling-consider}

- Scaling your deployment up might cause your databases to restart. If you scale RAM or CPU and your deployment needs to be moved to a host with more capacity, then the databases are restarted as part of the move.

- Scaling down RAM or CPU does not trigger database restarts.

- Disk cannot be scaled down.

- Scaling between hosting models (Shared Compute, Isolated Compute, and Dedicated Cores) moves your deployment to new hosts. Your databases are restarted as part of that move. As your deployment is moved to a new host, this can also take longer than just adding more resources. For more information, see [Shared Compute and Isolated Compute](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models).

- Similarly, drastically increasing RAM or Disk can take longer than smaller increases to account for provisioning more underlying hardware resources.

- Scaling operations are logged in [{{site.data.keyword.atracker_full}}](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-at_events).

- If you find consistent trends in resource usage or would like to set up scaling when certain resource thresholds are reached, check out enabling [autoscaling](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-autoscaling-mysql) on your deployment.

## Review current resources and hosting model
{: #review-resources-ui}
{: ui}

In the **Resources** tab, you find the **Hosting model** and **Resource allocations** tiles. These tiles reflect your current resources and hosting model. Select *Configure* to adjust the settings in each tile.

## Scaling in the UI
{: #resources-scaling-ui}

In the **Resources** tab of the UI, select *Configure* on the **Resource allocations** tile. This opens up a panel where you can adjust your resources.

Gen 2 uses the Isolated Compute hosting model. You will see a "Host sizes" table, where you can select the vCPU and RAM configuration per member for your database. Available configurations range from 4 CPU x 16 GB RAM up to 30 CPU x 240 GB RAM.

The "Disk (GB/member)" slider is your disk selection per member. Drag the slider or adjust the number in the input box to change the number of GB disk. Note that Disk is tied to IOPS at 1 GB = 10 IOPS. Maximum disk is 9 TB at GA (scaling to 32 TB in Q3 hardening).

Members is the number of members of your database. For MySQL Gen 2, members are set to 2.

Review your total estimated cost in the calculator on the bottom.

Click *Apply changes* to trigger this scaling operation.

## Review current resources and hosting model
{: #review-resources-cli}

The [{{site.data.keyword.databases-for}} CLI plug-in](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference) supports viewing and scaling the resources on your deployment. To scale any of the available resource groups, use `cdb deployment-groups-set` command.

For example, with the following command you can view the resource groups for a deployment named "example-deployment". Note that this command will also reveal if your database is a [Shared Compute](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models&interface=ui#hosting-models-shared-compute-ui) or [Isolated Compute](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models&interface=ui#hosting-models-iso-compute-ui) instance through the `hostflavor` attribute. If the `hostflavor` is null, it is on an old style hosting model.

```sh
ibmcloud cdb deployment-groups example-deployment
```
{: pre}

The command produces the following output:

```sh
Group   member
Count   3
|
+   Memory
|   Allocation                      3072mb
|   Allocation per member           1024mb
|   Minimum                         1024mb
|   Step Size                       256mb
|   Adjustable                      true
|   Cpu Enforcement Ratio Ceiling   49152mb
|   Cpu Enforcement Ratio           8192mb
|
+   CPU
|   Allocation              6
|   Allocation per member   3
|   Minimum                 6
|   Step Size               2
|   Adjustable              true
|
+   HostFlavor
|   ID            multitenant
|   Name
|   HostingSize
|
+   Disk
|   Allocation              15360mb
|   Allocation per member   5120mb
|   Minimum                 15360mb
|   Step Size               15360mb
|   Adjustable              true
```
{: pre}

The deployment has three members, with 3072 MB of RAM and 15360 MB of disk allocated in total. The "per member" allocation is 1024 MB of RAM and 5120 MB of disk. The minimum value is the lowest the total allocation can be set. The step size is the smallest amount by which the total allocation can be adjusted.

## Resources and scaling in the CLI
{: #resources-scaling-cli}
{: cli}

The `cdb deployment-groups-set` command allows either the total RAM or total disk allocation to be set in MB. For example, to scale the memory of the "example-deployment" to 4096 MB of RAM for each memory member (for a total memory of 12288 MB), you use the command:

```sh
ibmcloud cdb deployment-groups-set example-deployment member --memory 12288
```
{: pre}

## Determine the hosting model of your database
{: #resources-hosting-determine-cli}
{: cli}

Use the following command to review the value of the `hostflavor` attribute. This will be null if the database is on a deprecated hosting model (not Shared or Isolated Compute).

```sh
ibmcloud cdb groups <deployment_id> --json
```
{: pre}

## Switching to and between hosting models in the CLI
{: #resources-switching-cli}
{: cli}

If your database is a [Shared Compute](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models&interface=ui#hosting-models-shared-compute-ui) instance, you can adjust the memory, CPU, and disk options with the following command. If your database is not on Shared Compute, this command will also move a database from a different hosting model to the Shared Compute hosting model.

```sh
ibmcloud cdb deployment-groups-set <deploymentid> <groupid> [--memory <val>] [--cpu <val>] [--disk <val>] [--hostflavor multitenant]
```
{: pre}

For example, use the following to scale to a Shared Compute instance or scale up your Shared Compute instance:

```sh
ibmcloud cdb deployment-groups-set crn:abc ... xyz:: member  --memory 24576 --cpu 6  --hostflavor multitenant
```
{: pre}

If your database is an [Isolated Compute](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models&interface=ui#hosting-models-iso-compute-ui) instance, memory and CPU are adjusted together by selecting the Isolated Compute size (see all sizes in Table 1). Disk is scaled separately. If your database is not on Isolated Compute, this command will also move a database from a different hosting model to the Isolated Compute hosting model.

Note that since the host flavor selection includes CPU and RAM sizes (`b3c.4x16.encrypted` is 4 CPU and 16 RAM), this request does not accept both an Isolated size selection and separate CPU and RAM allocation selections.

```sh
ibmcloud cdb deployment-groups-set <deploymentid> <groupid> [--disk <val>] [--hostflavor <hostflavor>]
```
{: pre}

For example, use the following to scale to an Isolated Compute instance or scale up your Isolated Compute instance:

```sh
ibmcloud cdb deployment-groups-set crn:abc ... xyz:: member  --hostflavor b3c.8x32.encrypted
```
{: pre}

### The `hostflavor` parameter
{: #host-flavor-parameter-cli}
{: cli}

The `hostflavor` parameter defines your compute sizing. To provision a Shared Compute instance, specify `multitenant`. To provision an Isolated Compute instance, input the appropriate value for your desired CPU and RAM configuration.

| **Host flavor** | **hostflavor value** |
|:-------------------------:|:---------------------:|
| Shared Compute            | `multitenant`    |
| 4 CPU x 16 RAM            | `b3c.4x16.encrypted`    |
| 8 CPU x 32 RAM            | `b3c.8x32.encrypted`    |
| 8 CPU x 64 RAM            | `m3c.8x64.encrypted`    |
| 16 CPU x 64 RAM           | `b3c.16x64.encrypted`   |
| 32 CPU x 128 RAM          | `b3c.32x128.encrypted`  |
| 30 CPU x 240 RAM          | `m3c.30x240.encrypted`  |
{: caption="Host flavor sizing parameter" caption-side="bottom"}

## Review current resources and hosting model
{: #review-resources-api}
{:api}

The _Foundation Endpoint_ shown on the _Overview_ panel _Deployment details_ of your service provides the base URL to access this deployment through the API. Use it with the `/groups` endpoint if you need to manage or automate scaling programmatically.

To view the current and scalable resources on a deployment, use the [/deployments/{id}/groups](https://cloud.ibm.com/apidocs/cloud-databases-api#get-currently-available-scaling-groups-from-a-depl) endpoint. Note that this command will also reveal if your database is a [Shared Compute](https://cloud.ibm.com/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models&interface=ui#hosting-models-shared-compute-ui) or [Isolated Compute](https://cloud.ibm.com/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models&interface=ui#hosting-models-iso-compute-ui) instance through the `host_flavor` attribute. If the `host_flavor` is null, it is on an old style hosting model.

```sh
curl -X GET -H "Authorization: Bearer $APIKEY" 'https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/groups'
```
{: pre}

To scale the memory of a deployment to 4096 MB of RAM for each memory member (for a total memory of 12288 MB), use the following command:

```sh
curl -X PATCH 'https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/groups/member' \
-H "Authorization: Bearer $APIKEY" \
-H "Content-Type: application/json" \
-d '{"memory": {
        "allocation_mb": 12288
      }
    }'
```
{: pre}

For more information, see the [API reference](/apidocs/cloud-databases-api#get-currently-available-scaling-groups-from-a-depl).

## Determine the hosting model of your database
{: #resources-hosting-determine-api}
{: api}

Use the following command to review the value of the `host_flavor` attribute. This will be null if the database is on a deprecated hosting model (not Shared or Isolated Compute).

```sh
curl -X GET https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/groups
-H 'Authorization: Bearer <>' \
```
{: pre}

## Switching to and between hosting models in the API
{: #resources-switching-api}
{: api}

To scale any {{site.data.keyword.databases-for}} Shared Compute instance, use the the following command, setting `host_flavor` to `multitenant`. If your database is not on Shared Compute, this command will also move a database from a different hosting model to the Shared Compute hosting model.

```sh
curl -X PATCH https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/groups/member
-H 'Authorization: Bearer <>'
-H 'Content-Type: application/json'
-d '{"host_flavor": {"id": "multitenant"},
     "cpu": {"allocation_count": 3},
     "memory": {"allocation_mb": 12288}
    }' \
```
{: pre}

To scale any instance into a {{site.data.keyword.databases-for}} Isolated Compute instance or to scale to a different Isolated Compute size, use the `host_flavor` parameter, this time set to the desired Isolated Compute size. Available hosting sizes and their `host_flavor` value parameters are listed in [Table 1](#host-flavor-parameter-api). For example, `{"host_flavor": "b3c.4x16.encrypted"}`. Note that since the host flavor selection includes CPU and RAM sizes (`b3c.4x16.encrypted` is 4 CPU and 16 RAM), this request does not accept both, an Isolated size selection and separate CPU and RAM allocation selections. Scale with the {{site.data.keyword.databases-for}} [API scaling endpoint](https://cloud.ibm.com/apidocs/cloud-databases-api/cloud-databases-api-v5#setdeploymentscalinggroup){: external}, with a command like:

```sh
curl -X PATCH https://api.{region}.databases.cloud.ibm.com/v5/ibm/deployments/{id}/groups/member
-H 'Authorization: Bearer <>'
-H 'Content-Type: application/json'
-d '{"host_flavor": {"id": "b3c.4x16.encrypted"}}' \
```
{: pre}

CPU and RAM allocation is not allowed when provisioning or scaling through Isolated Compute. Specify `mulitenant` for the `host_flavor` parameter to have independent CPU and RAM selections.
{: note}

CPU and RAM autoscaling is not supported on {{site.data.keyword.databases-for}} Isolated Compute. Disk autoscaling is available. If you have provisioned an Isolated instance or switched over from a deployment with autoscaling, keep an eye on your resources using [{{site.data.keyword.monitoringfull}} integration](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-monitoring), which provides metrics for memory, disk space, and disk I/O utilization. To add resources to your instance, manually scale your deployment.
{: note}

### The `host flavor` parameter
{: #host-flavor-parameter-api}
{: api}

The `host_flavor` parameter defines your compute sizing. To provision a Shared Compute instance, specify `multitenant`. To provision an Isolated Compute instance, input the appropriate value for your desired CPU and RAM configuration.

| **Host flavor** | **host_flavor value** |
|:-------------------------:|:---------------------:|
| Shared Compute            | `multitenant`    |
| 4 CPU x 16 RAM            | `b3c.4x16.encrypted`    |
| 8 CPU x 32 RAM            | `b3c.8x32.encrypted`    |
| 8 CPU x 64 RAM            | `m3c.8x64.encrypted`    |
| 16 CPU x 64 RAM           | `b3c.16x64.encrypted`   |
| 32 CPU x 128 RAM          | `b3c.32x128.encrypted`  |
| 30 CPU x 240 RAM          | `m3c.30x240.encrypted`  |
{: caption="Table 1 Host flavor sizing parameter" caption-side="bottom"}

## Review current resources and hosting model
{: #review-resources-terraform}
{: terraform}

Review resource allocations to your database by checking your terraform scripts for `cpu { allocation_count = }`, `memory {allocation_mb = }`, and `disk { allocation_mb = }`. Review the `host_flavor` setting to determine if your database is a [Shared Compute](https://cloud.ibm.com/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models&interface=ui#hosting-models-shared-compute-ui) or [Isolated Compute](https://cloud.ibm.com/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models&interface=ui#hosting-models-iso-compute-ui) style hosting model. If `host_flavor` does not exist, your database is on an old style hosting model.


## Scaling with Terraform
{: #resources-scaling-terraform}
{: terraform}

Before executing a Terraform script on an existing instance, use the `terraform plan` command to compare the current infrastructure state with the desired state defined in your Terraform files. Any alteration to the `resource_group_id`, `service plan`, `version`, `key_protect_instance`, `key_protect_key`, `backup_encryption_key_crn` attributes recreates your instance. For a list of current argument references with the `Forces new resource` specification, see the [ibm_database Terraform Registry](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/database){: external}.
{: important}

Scale your instance by adjusting your Terraform script for the resource you're interested in. In the following example, `cpu`, `memory`, and `disk` allocations are specified. Note that if you have a host flavor selected (Isolated Compute or Shared Compute Multitenant), keep the host flavor selection in your script.

To implement your change, run `terraform apply`.

```terraform
data "ibm_resource_group" "group" {
  name = "<your_group>"
}
resource "ibm_database" "<your_database>" {
  name              = "<your_database_name>"
  plan              = "standard"
  location          = "eu-gb"
  service           = "databases-for-mysql-gen2"
  resource_group_id = data.ibm_resource_group.group.id
  tags              = ["tag1", "tag2"]
  adminpassword     = "password12"
  group {
    group_id = "member"
    cpu {
      allocation_count = 6
    }
    memory {
      allocation_mb = 24576
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

Alternatively, you can use pre-built, open-source, and enterprise-ready [Terraform IBM Modules (TIM)](https://cloud.ibm.com/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-about-tim) for [{{site.data.keyword.databases-for-mysql}}](https://registry.terraform.io/modules/terraform-ibm-modules/icd-mysql/ibm/latest){: external} that support the auto-scaling feature.

## Switching to and scaling hosting models in Terraform
{: #resources-switching-terraform}
{: terraform}

Select the [hosting model](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-hosting-models) you want your database to be scaled to. You can change this later.

To scale your {{site.data.keyword.databases-for-mysql}} instance to the Shared Compute hosting flavor, set the `"host_flavor"` parameter to `multitenant`. This works if you want to scale to the Shared Compute hosting flavor, or if you want to keep the host flavor and scale your resources. To implement your change, run `terraform apply`.

See the following example:

```terraform
data "ibm_resource_group" "group" {
  name = "<your_group>"
}
resource "ibm_database" "<your_database>" {
  name              = "<your_database_name>"
  plan              = "standard"
  location          = "eu-gb"
  service           = "databases-for-mysql-gen2"
  resource_group_id = data.ibm_resource_group.group.id
  tags              = ["tag1", "tag2"]
  adminpassword     = "password12"
  group {
    group_id = "member"
    host_flavor {
      id = "multitenant"
    },
    cpu {
      allocation_count = 6
    }
    memory {
      allocation_mb = 24576
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

Scale your {{site.data.keyword.databases-for-mysql}} instance to Isolated Compute with the same `"host_flavor"` parameter, set to the desired Isolated size. This command works to scale your database instance to a different Isolated Compute size, as well as to move from another host flavor to the Isolated Compute host flavor. Available hosting sizes and their `host_flavor value` parameters are listed in [Table 1](#host-flavor-parameter-terraform). For example, `{"host_flavor": "b3c.4x16.encrypted"}`. Note that because the host flavor selection includes CPU and RAM sizes (`b3c.4x16.encrypted` is 4 CPU and 16 RAM), this request does not accept both an Isolated size selection and separate CPU and RAM allocation selections.

To implement your change, run `terraform apply`.

```terraform
data "ibm_resource_group" "group" {
  name = "<your_group>"
}
resource "ibm_database" "<your_database>" {
  name              = "<your_database_name>"
  plan              = "standard"
  location          = "eu-gb"
  service           = "databases-for-mysql-gen2"
  resource_group_id = data.ibm_resource_group.group.id
  tags              = ["tag1", "tag2"]
  adminpassword     = "password12"
  group {
    group_id = "member"
    host_flavor {
      id = "b3c.8x32.encrypted"
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
