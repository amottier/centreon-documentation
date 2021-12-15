---
id: cloud-azure-database-cosmosdb
title: Azure Cosmos DB
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


## Overview

Azure Cosmos DB is a fully managed NoSQL database for modern app development. Single-digit millisecond response times, and
automatic and instant scalability, guarantee speed at any scale.

The Centreon Plugin-Pack *Azure Cosmos DB* can rely on Azure API or Azure CLI to collect the metrics related to the
Cosmos DB service.

## Pack Assets

### Monitored Objects

* Azure *Cosmos DB* accounts
* Availability
* Cache
* Document
* Health
* Latency
* Throughput
* Units
* Usage

### Discovery rules

The Centreon Plugin Pack *Azure Cosmos DB* includes a Host Discovery *provider* to automatically discover the Azure instances of a given
subscription and add them to the Centreon configuration.
This provider is named **Microsoft Azure Cosmos DB Account**:

![image](../../../assets/integrations/plugin-packs/procedures/cloud-azure-database-cosmosdb-provider.png)

> This discovery feature is only compatible with the 'api' custom mode. 'azcli' is not supported yet.

More information about the Host Discovery module is available in the Centreon documentation:
[Host Discovery](../../../monitoring/discovery/hosts-discovery)

### Collected metrics & status

<Tabs groupId="operating-systems">
<TabItem value="Availability" label="Availability">

| Metric Name                                      | Description          | Unit |
| :----------------------------------------------- | :------------------- | :--- |
| cosmosdb.account.service.availability.percentage | Service Availability | %    |

</TabItem>
<TabItem value="Cache" label="Cache">

| Metric Name                                          | Description                           | Unit  |
| :--------------------------------------------------- | :------------------------------------ | :---- |
| cosmosdb.account.integratedcache.evicted.size.bytes  | Integrated Cache Evicted Entries Size | B     |
| cosmosdb.account.integratedcache.hitrate.percentage  | Integrated Cache Hit Rate             | %     |
| cosmosdb.account.integratedcache.size.bytes          | Integrated Cache Size                 | B     |
| cosmosdb.account.integratedcache.ttlexpiration.count | Integrated Cache TTL Expiration Count | Count |


</TabItem>
<TabItem value="Document" label="Document">

| Metric Name                           | Description    | Unit  |
| :------------------------------------ | :------------- | :---- |
| cosmosdb.account.document.count       | Document Count | Count |
| cosmosdb.account.document.quota.bytes | Document Quota | B     |

</TabItem>
<TabItem value="Health" label="Health">

| Status Name | Description                 |
| :---------- | :-------------------------- |
| status      | Current operational status  |
| summary     | Last related status message |

</TabItem>
<TabItem value="Latency" label="Latency">

| Metric Name                                       | Description             | Unit |
| :------------------------------------------------ | :---------------------- | :--- |
| cosmosdb.account.latency.replication.milliseconds | P99 Replication Latency | ms   |
| cosmosdb.account.latency.serverside.milliseconds  | Server Side Latency     | ms   |

</TabItem>
<TabItem value="Throughput" label="Throughput">

| Metric Name                                  | Description              | Unit  |
| :------------------------------------------- | :----------------------- | :---- |
| cosmosdb.account.troughput.autoscale.count   | Autoscale Max Throughput | Count |
| cosmosdb.account.troughput.provisioned.count | Provisioned Throughput   | Count |

</TabItem>
<TabItem value="Units" label="Units">

| Metric Name                               | Description         | Unit  |
| :---------------------------------------- | :------------------ | :---- |
| cosmosdb.account.requestunits.total.count | Total Request Units | Count |

</TabItem>
<TabItem value="Usage" label="Usage">

| Metric Name                        | Description | Unit |
| :--------------------------------- | :---------- | :--- |
| cosmosdb.account.data.usage.bytes  | Data Usage  | B    |
| cosmosdb.account.index.usage.bytes | Index Usage | B    |

</TabItem>
</Tabs>

## Prerequisites

To get data from Azure Services, following methods are available:
* Azure API ('api')
* Azure CLI ('azcli')

Centreon recommends to use the API instead of the CLI for the following reasons:
* API is much more efficient by avoiding CLI binary execution
* API supports application authentication while CLI does not (yet)

<Tabs groupId="operating-systems">
<TabItem value="Azure Monitor API" label="Azure Monitor API">

To use the 'api' custom mode, make sure to obtain the required information using the
how-to below. Keep it safe until including it in a Host or Host Template definition.

* Create an *application* in Azure Active Directory:
- Log in to your Azure account.
- Select *Azure Active directory* in the left sidebar.
- Click on *App registrations*.
- Click on *+ Add*.
- Enter Centreon as the application name (or any name of your choice), select application type(api) and sign-on-url.
- Click on the *Create* button.

* Get *Subscription ID*
- Log in to your Azure account.
- Select *Subscriptions* in the left sidebar.
- Select whichever subscription is needed.
- Click on *Overview*.
- **Copy the Subscription ID.**

* Get *Tenant ID*
- Log in to your Azure account.
- Select *Azure Active directory* in the left sidebar.
- Click on *Properties*.
- **Copy the directory ID.**

* Get *Client ID*
- Log in to your Azure account.
- Select *Azure Active directory* in the left sidebar.
- Click on *Enterprise applications*.
- Click on *All applications*.
- Select the application previously created.
- Click on *Properties*.
- **Copy the Application ID.**

* Get *Client secret*
- Log in to your Azure account.
- Select *Azure Active directory* in the left sidebar.
- Click on *App registrations*.
- Select the application previously created.
- Click on *All settings*.
- Click on *Keys*.
- Enter the key description and select the duration.
- Click on *Save*.
- **Copy and store the key value. You won't be able to retrieve it after you leave this page.**

</TabItem>
<TabItem value="Azure AZ CLI" label="Azure AZ CLI">

To use the 'azcli' custom mode, install the required packages on every Centreon poller expected to
monitor Azure Resources using CLI:

- The CLI needs at least Python version 2.7
(<https://github.com/Azure/azure-cli/blob/dev/doc/install_linux_prerequisites.md>).

On RPM-Based distributions, use the command below to install it using *root* or 'sudo':

```shell
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo echo -e "[azure-cli]\nname=Azure CLI\nbaseurl=https://packages.microsoft.com/yumrepos/azure-cli\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/azure-cli.repo
sudo yum install azure-cli
```

Then, use the *centreon-engine* account to obtain a token using command below:

```shell
su - centreon-engine
az login
```

The shell will output this message including an authentication code:

*To sign in, use a web browser to open the page https://microsoft.com/devicelogin*
*and enter the code CWT4WQZAD to authenticate.*

Go to <https://microsoft.com/devicelogin> and enter the code.

Connect using a monitoring service account, as a result, the shell should prompt
information below:

```shell
[
{
"cloudName": "AzureCloud",
"id": "0ef83f3a-d83e-2039-d930-309df93acd93d",
"isDefault": true,
"name": "N/A(tenant level account)",
"state": "Enabled",
"tenantId": "0ef83f3a-03cd-2039-d930-90fd39ecd048",
"user": {
"name": "email@mycompany.onmicrosoft.com",
"type": "user"
}
}
]
```

Credentials are now stored locally in the .accessTokens.json file so the Plugin
can use it.

</TabItem>
</Tabs>

## Setup

<Tabs groupId="licence-systems">
<TabItem value="Online IMP Licence & IT100 Editions" label="Online IMP Licence & IT100 Editions">

1. Install the Centreon Plugin package on every Centreon poller expected to monitor Azure Cosmos DB resources:

```bash
yum install centreon-plugin-Cloud-Azure-Database-CosmosDb-Api
```

2. On the Centreon Web interface, install the *Azure Cosmos DB* Centreon Plugin Pack on the "Configuration > Plugin Packs > Manager" page

</TabItem>
<TabItem value="Offline IMP License" label="Offline IMP License">

1. Install the Centreon Plugin package on every Centreon poller expected to monitor Azure Cosmos DB resources:

```bash
yum install centreon-plugin-Cloud-Azure-Database-CosmosDb-Api
```

2. Install the Centreon Plugin Pack RPM on the Centreon Central server:

```bash
yum install centreon-pack-cloud-azure-database-cosmosdb.noarch
```

3. On the Centreon Web interface, install the *Azure Cosmos DB* Centreon Plugin Pack on the "Configuration > Plugin Packs > Manager" page

</TabItem>
</Tabs>

## Configuration

### Host

* Log into Centreon and add a new Host through "Configuration > Hosts".
* In the *IP Address/FQDN* field, set the following IP address: '127.0.0.1'.

* Select the *Cloud-Azure-Database-CosmosDb-custom* template to apply to the Host.
* Once the template applied, some Macros marked as 'Mandatory' hereafter have to be configured.
These mandatory Macros differ regarding the custom mode used.

> Two methods can be used to set the Macros:
> * full ID of the Resource (```/subscriptions/<subscription_id>/resourceGroups/<resourcegroup_id>/providers/Microsoft.DocumentDB/databaseAccounts/<resource_name>```)
in *AZURERESOURCE*
> * Resource Name in *AZURERESOURCE* associated with Resource Group (in *AZURERESOURCEGROUP*) and Resource Type (in *AZURERESOURCETYPE*)

<Tabs groupId="operating-systems">
<TabItem value="Azure Monitor API" label="Azure Monitor API">

| Mandatory | Nom                | Description                                        |
| :-------- | :----------------- | :------------------------------------------------- |
| X         | AZURECUSTOMMODE    | Custom mode 'api'                                  |
| X         | AZURESUBSCRIPTION  | Subscription ID                                    |
| X         | AZURETENANT        | Tenant ID                                          |
| X         | AZURECLIENTID      | Client ID                                          |
| X         | AZURECLIENTSECRET  | Client secret                                      |
| X         | AZURERESOURCE      | ID or name of the Cosmos DB account                |
|           | AZURERESOURCEGROUP | Associated Resource Group if resource name is used |
|           | AZURERESOURCETYPE  | Associated Resource Type if resource name is used  |

</TabItem>
<TabItem value="Azure AZ CLI" label="Azure AZ CLI">

| Mandatory | Nom                | Description                                        |
| :-------- | :----------------- | :------------------------------------------------- |
| X         | AZURECUSTOMMODE    | Custom mode 'azcli'                                |
| X         | AZURESUBSCRIPTION  | Subscription ID                                    |
| X         | AZURERESOURCE      | ID or name of the Cosmos DB account                |
|           | AZURERESOURCEGROUP | Associated Resource Group if resource name is used |
|           | AZURERESOURCETYPE  | Associated Resource Type if resource name is used  |

</TabItem>
</Tabs>

## How to check in the CLI that the configuration is OK and what are the main options for ?

Once the Plugin installed, log into your Centreon Poller CLI using the *centreon-engine*
user account and test the Plugin by running the following command:

```bash
/usr/lib/centreon/plugins/centreon_azure_database_cosmosdb_api.pl \
--plugin=cloud::azure::database::cosmosdb::plugin \
--mode=latency \
--custommode=api \
--subscription='xxxxxxxxx' \
--tenant='xxxxxxxxx' \
--client-id='xxxxxxxxx' \
--client-secret='xxxxxxxxx' \
--resource='DB001ABCD' \
--resource-group='RSG1234' \
--timeframe='900' \
--interval='PT5M' \
--aggregation='Average' \
--warning-serverside-latency='800' \
--critical-serverside-latency='900'
```

Expected command output is shown below:

```bash
OK: Instance 'DB001ABCD' Statistic 'average' Metrics Server Side Latency: 0.00ms, P99 Replication Latency: 0.00ms |
'DB001ABCD~average#cosmosdb.account.latency.serverside.milliseconds'=0.00ms;0:800;0:900;0; 'DB001ABCD~average#cosmosdb.account.latency.replication.milliseconds'=0.00ms;;;0;
```

The command above checks the *latency* of an Azure *Cosmos DB* account using the 'api' custom-mode
(```--plugin=cloud::azure::database::cosmosdb::plugin --mode=latency --custommode=api```).
This Cosmos DB account is identified by its id (```--resource='DB001ABCD'```) and its associated group (```--resource-group='RSG1234'```).
The authentication parameters to be used with the custom mode are specified in the options (```--subscription='xxxxxxxxx'
--tenant='xxxxxxx' --client-id='xxxxxxxx' --client-secret='xxxxxxxxxx'```).

The calculated metrics are the average values (```--aggregation='Average'```) of a 900 secondes / 15 min period (```--timeframe='900'```)
with one sample per 5 minutes (```--interval='PT5M'```).

This command would trigger a WARNING alarm if the *latency* is reported as over 80ms (```--warning-serverside-latency='800'```)
and a CRITICAL alarm over 90ms (```--critical-serverside-latency='900'```).

All the available options for a given mode can be displayed by adding the ```--help``` parameter to the command:

```bash
/usr/lib/centreon/plugins/centreon_azure_database_cosmosdb_api.pl \
--plugin=cloud::azure::database::cosmosdb::plugin \
--mode=latency \
--help
```

### Troubleshooting

#### The Azure credentials have changed and the Plugin does not work anymore

The Plugin is using a cache file to keep connection information and avoid an authentication at each call.
If some of the authentication parameters change, you must delete the cache file.

The cache file can be found within  ```/var/lib/centreon/centplugins/``` folder with a name similar to `azure_api_<md5>_<md5>_<md5>_<md5>`.

#### ```UNKNOWN: Login endpoint API returns error code 'ERROR_NAME' (add --debug option for detailed message)```

This error means that some parameters used to authenticate the API request are wrong. The 'ERROR_NAME' string gives
some hints about where the problem stands.

As an example, if my Client ID or Client Secret are wrong, 'ERROR_DESC' value will be 'invalid_client'.

#### ```UNKNOWN: 500 Can't connect to login.microsoftonline.com:443```

This error message means that the Centreon Plugin couldn't successfully connect to the Azure Login API. Check that no third party
device (such as a firewall) is blocking the request. A proxy connection may also be necessary to connect to the API.
This can be done by using this option in the command: ```--proxyurl='http://proxy.mycompany:8080'```.

#### ```UNKNOWN: No metrics. Check your options or use --zeroed option to set 0 on undefined values```

This command result means that Azure does not have any value for the requested period.
This result can be overriden by adding the ```--zeroed``` option in the command. This will force a value of 0 when no metric has
been collected and will prevent the UNKNOWN error message.