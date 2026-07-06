---

copyright:
  years: 2026
lastupdated: "2026-06-22"

keywords: mysql, databases, kubernetes, connecting application, mysql connection strings, gen2

subcollection: databases-for-mysql-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Connecting an {{site.data.keyword.cloud_notm}} application
{: #ibmcloud-app}

[Gen 2]{: tag-purple}

Applications running in {{site.data.keyword.cloud_notm}} can be bound to your {{site.data.keyword.databases-for-mysql_full}} deployment.

## Connecting a Kubernetes Service application
{: #kub-app}

Connecting a Cloud databases deployment to a Kubernetes Service application is a two-step process. First, your deployment needs to be bound to your cluster and its connection strings stored in secret. The second step is configuring your application to use the connection strings.

Before connecting your Kubernetes Service application to a deployment, make sure that the deployment and cluster are both in the same region and resource group.

### Binding your deployment
{: #bind-deployment}

**Private Endpoints** - If you want to use a private endpoint, and one is enabled on your deployment, then first you need to create a service key for your database so Kubernetes can use it when binding to the database.

```sh
ibmcloud resource service-key-create <your-private-key> --instance-name <your_database_deployment> --service-endpoint private
```
{: pre}

The private service endpoint is selected with `--service-endpoint private`. After that, you bind the database to the Kubernetes cluster through the private endpoint with the `cluster service bind` command.

```sh
ibmcloud ks cluster service bind <your_cluster_name> <resource_group> <your_database_deployment> --key <your-private-key>
```
{: pre}

**Verify** - Verify that the Kubernetes secret was created in your cluster namespace. Running the following command, you get the API key for accessing the instance of your deployment that's provisioned in your account.

```sh
kubectl get secrets --namespace=default
```
{: pre}

More information on binding services is found in the [Kubernetes Service documentation](/docs/containers?topic=containers-service-binding#bind-services).

### Configuring in your Kubernetes app
{: #config-kub-app}

When you bind your application to Kubernetes Service, it creates an environment variable from the cluster's secrets. Your deployment's connection information lives in `BINDING` as a JSON object. Load and parse the JSON object into your application to retrieve the information your application's driver needs to make a connection to the database.

The [Connection Strings](/docs/databases-for-mysql-gen2?topic=databases-for-mysql-gen2-connection-strings#connection-string-breakdown) page contains a reference of the JSON fields.

For more information, see the [Kubernetes Service docs](https://cloud.ibm.com/docs/containers?topic=containers-service-binding#reference_secret).
