---
title: Azure 监视 REST API 演练
description: 如何对请求进行身份验证，以及如何使用 Azure Monitor REST API 检索可用的指标定义和指标值。
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: c15e985cd46c283856fd0ac2f9a374e31753c5fc
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234103"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure 监视 REST API 演练

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文说明如何执行身份验证，使代码能够遵循 [Microsoft Azure 监视器 REST API 参考](/rest/api/monitor/)。

使用 Azure Monitor API 能够以编程方式检索可用的默认指标定义、维度值和指标值。 可将数据保存在独立的数据存储（例如 Azure SQL 数据库、Azure Cosmos DB 或 Azure Data Lake）中。 然后，可以根据需要从该处执行其他分析。

除了处理各种指标数据点以外，使用监视 API 还可以列出警报规则、查看活动日志以及执行其他许多操作。 有关可用操作的完整列表，请参阅 [Microsoft Azure 监视器 REST API 参考](/rest/api/monitor/)。

## <a name="authenticating-azure-monitor-requests"></a>对 Azure Monitor 请求进行身份验证

第一步是对请求进行身份验证。

针对 Azure 监视器 API 执行的所有任务都使用 Azure Resource Manager 身份验证模型。 因此，所有请求必须使用 Azure Active Directory (Azure AD) 进行身份验证。 对客户端应用程序进行身份验证的方法之一是创建 Azure AD 服务主体，并检索身份验证 (JWT) 令牌。 以下示例脚本演示如何通过 PowerShell 创建 Azure AD 服务主体。 有关更详细的演练，请参阅有关[使用 Azure PowerShell 创建用于访问资源的服务主体](/powershell/azure/create-azure-service-principal-azureps)的文档。 还可以[通过 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

若要查询 Azure 监视器 API，客户端应用程序应使用事先创建的服务主体进行身份验证。 以下示例 PowerShell 脚本演示了一种使用 [Active Directory 身份验证库](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) 来获取 JWT 身份验证令牌的方法。 JWT 令牌作为请求中 HTTP 授权参数的一部分传递给 Azure Monitor REST API。

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

进行身份验证后，可以针对 Azure Monitor REST API 执行查询。 有两个有用的查询：

1. 列出资源的指标定义
2. 检索指标值

> [!NOTE]
> 有关使用 Azure REST API 进行身份验证的其他信息，请参阅 [Azure REST API 参考](/rest/api/azure/)。
>
>

## <a name="retrieve-metric-definitions"></a>检索指标定义

使用 [Azure Monitor 指标定义 REST API](/rest/api/monitor/metricdefinitions) 可以访问服务可用的指标列表。

**方法**：GET

**请求 URI**：https:\/\/management.azure.com/subscriptions/ *{subscriptionId}* /resourceGroups/ *{resourceGroupName}* /providers/ *{resourceProviderNamespace}* / *{resourceType}* / *{resourceName}* /providers/microsoft.insights/metricDefinitions?api-version= *{apiVersion}*

例如，若要检索 Azure 存储帐户的指标定义，请求将如下所示：

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> 较旧版本的指标定义 API 不支持维度。 建议使用 API 版本“2018-01-01”或更高版本。
>
>

生成的 JSON 响应正文将类似于以下示例：（请注意第二个指标具有维度）

```json
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values"></a>检索维度值

了解可用的指标定义后，可能会发现一些指标具有多个维。 在查询指标前，可能需要查明某个维具有的值的范围。 然后，根据这些维值，在查询指标时，可以选择根据维值对指标进行筛选或分段。  使用 [Azure Monitor 指标 REST API](/rest/api/monitor/metrics) 查找给定指标维度的所有可能值。

对于任何筛选请求，请使用指标的名称“value”（而非“localizedValue”）。 如果未指定筛选器，则返回默认指标。 使用此 API 仅允许一个维度具有通配符筛选器。 维度值请求和指标数据请求之间的主要差别在于指定“resultType=metadata”查询参数。

> [!NOTE]
> 若要使用 Azure Monitor REST API 检索维度值，请使用“2019-07-01”或更高版本的 API。
>
>

**方法**：GET

**请求 URI**：https\://management.azure.com/subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/ *{resource-provider-namespace}* / *{resource-type}* / *{resource-name}* /providers/microsoft.insights/metrics?metricnames= *{metric}* &timespan= *{starttime/endtime}* &$filter= *{filter}* &resultType=metadata&api-version= *{apiVersion}*

例如，若要检索为“事务”指标的“API 名称维度”发出的维度值列表，其中在指定时间范围内 GeoType 维度为“Primary”，则请求将如下所示：

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=GeoType eq 'Primary' and ApiName eq '*'&api-version=2019-07-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

生成的 JSON 响应正文将类似于以下示例：

```json
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values"></a>检索指标值

知道可用的指标定义和可能的维值后，即可检索相关的指标值。  为此，请使用 [Azure Monitor 指标 REST API](/rest/api/monitor/metrics)。

对于任何筛选请求，请使用指标的名称“value”（而非“localizedValue”）。 如果未指定维筛选器，则会返回汇总的聚合指标。 若要提取具有特定维度值的多个时序，请指定一个筛选器查询参数，该参数指定两个维度值，例如“&$filter=ApiName eq 'ListContainers' or ApiName eq 'GetBlobServiceProperties'”。 若要返回给定维度的每个值的时序，请使用“ *”筛选器，例如“&$filter=ApiName eq '* '”。 “Top”和“OrderBy”查询参数可用于限制返回的时序数以及对这些时序进行排序。

> [!NOTE]
> 若要使用 Azure Monitor REST API 检索多维指标值，请使用“2019-07-01”或更高版本的 API。
>
>

**方法**：GET

**请求 URI**：https:\//management.azure.com/subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/ *{resource-provider-namespace}* / *{resource-type}* / *{resource-name}* /providers/microsoft.insights/metrics?metricnames= *{metric}* &timespan= *{starttime/endtime}* &$filter= *{filter}* &interval= *{timeGrain}* &aggregation= *{aggreation}* &api-version= *{apiVersion}*

例如，若要在 5 分钟时间范围内按“事务”数降序值检索前 3 个 API，其中 GeotType 为 “Primary”，则请求将如下所示：

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=apiname eq 'GetBlobProperties'&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2019-07-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

生成的 JSON 响应正文将类似于以下示例：

```json
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

### <a name="use-armclient"></a>使用 ARMClient

另一种方法是使用 Windows 计算机上的 [ARMClient](https://github.com/projectkudu/armclient)。 ARMClient 将自动处理 Azure AD 身份验证（及生成的 JWT 令牌）。 以下步骤概述如何使用 ARMClient 检索指标数据：

1. 安装 [Chocolatey](https://chocolatey.org/) 和 [ARMClient](https://github.com/projectkudu/armclient)。
2. 在终端窗口中，键入 *armclient.exe login*。 这样做会提示登录到 Azure。
3. 键入 *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. 键入 *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

例如，若要检索特定逻辑应用的指标定义，请发出以下命令：

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>检索资源 ID

使用 REST API 确实有助于了解可用的指标定义、粒度和相关值。 使用 [Azure 管理库](/previous-versions/azure/reference/mt417623(v=azure.100))时，这些信息很有用。

对于上述代码，要使用的资源 ID 是所需 Azure 资源的完整路径。 例如，要针对某个 Azure Web 应用执行查询，资源 ID 将是：

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

以下列表包含各种 Azure 资源的几个资源 ID 格式示例：

* **IoT 中心** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Devices/IotHubs/ *{iot-hub-name}*
* **SQL 弹性池** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Sql/servers/ *{pool-db}* /elasticpools/ *{sql-pool-name}*
* **SQL 数据库 (v12)** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Sql/servers/ *{server-name}* /databases/ *{database-name}*
* **服务总线** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.ServiceBus/ *{namespace}* / *{servicebus-name}*
* **虚拟机规模集** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Compute/virtualMachineScaleSets/ *{vm-name}*
* **VM** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Compute/virtualMachines/ *{vm-name}*
* **事件中心** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.EventHub/namespaces/ *{eventhub-namespace}*

还可以通过其他方法检索资源 ID，包括使用 Azure 资源浏览器，以及通过 Azure 门户、PowerShell 或 Azure CLI 查看所需的资源。

### <a name="azure-resource-explorer"></a>Azure 资源浏览器

若要查找所需资源的资源 ID，一种有效的方法是使用 [Azure 资源浏览器](https://resources.azure.com)工具。 导航到所需的资源，并查看如以下屏幕截图中所示的 ID：

![Alt "Azure 资源浏览器"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure 门户

还可以从 Azure 门户获取资源 ID。 为此，请导航到所需的资源，并选择“属性”。 资源 ID 将显示在“属性”部分中，如以下屏幕截图中所示：

![Alt "Azure 门户的“属性”边栏选项卡中显示的资源 ID"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

也可以使用 Azure PowerShell cmdlet 检索资源 ID。 例如，若要获取某个 Azure 逻辑应用的资源 ID，请执行 Get-AzureLogicApp cmdlet，如以下示例中所示：

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

结果应类似于以下示例：

```output
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 检索某个 Azure 存储帐户的资源 ID，请执行 `az storage account show` 命令，如以下示例中所示：

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

结果应类似于以下示例：

```json
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure 逻辑应用尚不能通过 Azure CLI 获取，因此，前面的示例中显示了一个 Azure 存储帐户。
>
>

## <a name="retrieve-activity-log-data"></a>检索活动日志数据

除了指标定义和相关值以外，还可以使用 Azure Monitor REST API 检索有关 Azure 资源的其他需关注的深入信息。 例如，可查询[活动日志](/rest/api/monitor/activitylogs)数据。 以下示例请求使用 Azure Monitor REST API 查询活动日志。

在有过滤器的情况下获取活动日志：

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

在有过滤器的情况下获取活动日志，然后选择：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

获取活动日志且选择：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

在没有过滤器的情况下获取活动日志，或选择：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```

## <a name="troubleshooting"></a>疑难解答

如果收到 429、503 或 504 错误，请在一分钟内重试 API。


## <a name="next-steps"></a>后续步骤

* 查看 [监视概述](../overview.md)。
* 查看 [Azure 监视器支持的指标](./metrics-supported.md)。
* 查看 [Microsoft Azure 监视器 REST API 参考](/rest/api/monitor/)。
* 查看 [Azure 管理库](/previous-versions/azure/reference/mt417623(v=azure.100))。
