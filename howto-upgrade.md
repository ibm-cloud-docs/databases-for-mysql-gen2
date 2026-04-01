---
copyright:
  years: 2020, 2026
lastupdated: "2026-04-01"

keyowrds: mysql, databases, upgrading, major versions, mysql new deployment, mysql database version, mysql major version

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Upgrading to a new Major Version
{: #mysql-upgrading}

Once a major version of a database is at its End Of Life (EOL), upgrade to a current major version.

Find the available versions of MySQL on the [{{site.data.keyword.databases-for-mysql-gen2_full}} catalog](https://cloud.ibm.com/databases/databases-for-mysql-gen2/create) page, from the {{site.data.keyword.databases-for}} CLI `plug-in` command [`ibmcloud cdb deployables-show`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployables-show), or from the {{site.data.keyword.databases-for}} API [`/deployables`](https://cloud.ibm.com/apidocs/cloud-databases-api#get-all-deployable-databases) endpoint.

## Review important changes before upgrading to MySQL 8.4
{: #mysql-upgrading-reqs}

Before upgrading to MySQL v8.4, review the notable changes, deprecations, and incompatibilities between MySQL 8.0 and 8.4. Check the {{site.data.keyword.cloud}} [MySQL 8.4 GA](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-mysql8.4-ga&interface=ui) documentation to ensure compatibility and a smooth migration.

## Back up and restore upgrade
{: #mysql-backup-restore}

To upgrade your database, initiate the process by [restoring a backup](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-dashboard-backups&interface=cli#restore-backup-ui) of your data into a new deployment that is running the new database version.

### Upgrading in the UI
{: #mysql-upgrading-ui}
{: ui}

You can upgrade to a new version when [restoring a backup](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-dashboard-backups&interface=cli#restore-backup-cli) from the _Backups_ menu of your _Deployment dashboard_. Clicking **Restore** on a backup brings up a dialog box where you can change some options for the new deployment. One of them is the database version, which is auto-populated with the versions available for you to upgrade to. Select a version and click **Restore** to start the provision and restore process.

### Upgrading through the CLI
{: #mysql-upgrading-cli}
{: cli}

To upgrade and restore from backup through the {{site.data.keyword.cloud_notm}} CLI, use the [provisioning command](/docs/account?topic=account-manage_resource&interface=cli) from the Resource Controller.
```sh
ibmcloud resource service-instance-create <service-name> <service-id> <service-plan-id> <region>
```
{: pre}

The parameters `service-name`, `service-id`, `service-plan-id`, and `region` are all required. You also supply the `-p` with the `version` and `backup_ID` parameters in a JSON object. The new deployment is automatically sized with the same disk and memory as the source deployment at the time of the backup.

```sh
ibmcloud resource service-instance-create example-upgrade databases-for-mysql-gen2 standard us-south \
-p \ '{
  "backup_id": "crn:v1:bluemix:public:databases-for-mysql-gen2:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
  "version":8
}'
```
{: pre}

### Upgrading through the API
{: #mysql-upgrading-api}
{: api}

Complete the necessary steps to use the [Resource Controller API](https://cloud.ibm.com/apidocs/resource-controller/resource-controller#create-resource-instance){: external} before using it to upgrade from a backup. Then, send the API a POST request. The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required. You also supply the `version` and `backup_ID`. The new deployment has the same memory and disk allocation as the source deployment at the time of the backup.

```sh
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "my-instance",
    "target": "bluemix-us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-mysql-gen2-standard",
    "backup_id": "crn:v1:bluemix:public:databases-for-mysql-gen2:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
    "version":8
  }'
```
{: pre}
