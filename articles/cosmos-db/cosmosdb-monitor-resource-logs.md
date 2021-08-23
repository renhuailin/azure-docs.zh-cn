---
title: 使用 Azure 诊断设置监视 Azure Cosmos DB 数据
description: 了解如何使用 Azure 诊断设置来监视 Azure Cosmos DB 中存储的数据的性能和可用性
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 8c9303097a7b3b545d8fa106dd23f8d5662fc69d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111964223"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>使用 Azure 中的诊断设置监视 Azure Cosmos DB 数据
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure 中的诊断设置用于收集资源日志。 Azure 资源日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 这些日志是按请求捕获的，也称为“数据平面日志”。 部分数据平面操作的示例包括 delete、insert 和 readFeed。 这些日志的内容因资源类型而异。

平台指标和活动日志是自动收集的；必须创建一个诊断设置才能收集资源日志，或在 Azure Monitor 外部转发这些日志。 可以打开 Azure Cosmos DB 帐户的诊断设置，并将资源日志发送到以下源：
- Log Analytics 工作区
  - 发送到 Log Analytics 的数据可以写入到 Azure 诊断（旧版）或特定于资源的（预览版）表中  
- 事件中心
- 存储帐户
  
> [!NOTE]
> 对于 SQL API 帐户，建议[遵循通过 REST API 创建诊断设置的说明](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting)，在资源特定模式下创建诊断设置。 此选项通过改进的数据处理视图提供了额外的成本优化。

## <a name="create-using-the-azure-portal"></a>使用 Azure 门户进行创建

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到 Azure Cosmos 帐户。 打开“诊断设置”窗格，然后选择“添加诊断设置”选项。 

2. 在“诊断设置”窗格中，用你喜欢的类别填充窗体。

### <a name="choosing-log-categories"></a>选择日志类别

|类别  |API   | 定义  | 键属性   |
|---------|---------|---------|---------|
|DataPlaneRequests     |  所有 API        |     将后端请求（为了创建、更新、删除或检索帐户中的数据而执行的请求）记录为数据平面操作。   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
|MongoRequests     |    Mongo    |   记录来自前端的由用户发起的请求，以处理发送到 Azure Cosmos DB 的用于 MongoDB 的 API 的请求。 在启用此类别时，请确保禁用 DataPlaneRequests。      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
|CassandraRequests     |   Cassandra      |    记录来自前端的由用户发起的请求，以处理发送到 Azure Cosmos DB 的用于 Cassandra 的 API 的请求。 在启用此类别时，请确保禁用 DataPlaneRequests。     |     `operationName`, `requestCharge`, `piiCommandText`    |
|GremlinRequests     |    Gremlin    |     记录来自前端的由用户发起的请求，以处理发送到 Azure Cosmos DB 的用于 Gremlin 的 API 的请求。 在启用此类别时，请确保禁用 DataPlaneRequests。    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
|QueryRuntimeStatistics     |   SQL      |     此表详细介绍了针对 SQL API 帐户执行的查询操作。 在默认情况下，会模糊处理查询文本及其参数，以避免通过由请求提供的全文查询日志记录来记录 PII 数据。    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
|PartitionKeyStatistics     |    所有 API     |   通过表示分区键的存储大小 (KB) 来记录逻辑分区键的统计信息。 此表在排查存储倾斜的问题时非常有用。      |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
|PartitionKeyRUConsumption     |   SQL API    |     记录分区键的每秒 RU 聚合消耗量。 此表对于排查热分区的问题很有用。 目前，Azure Cosmos DB 仅报告 SQL API 帐户的分区键，以及时点读取/写入和存储过程操作。   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
|ControlPlaneRequests     |   所有 API       |    记录有关控制平面操作（即，创建帐户、添加或删除区域、更新帐户复制设置等）的详细信息。     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
|TableApiRequests     |   表 API    |     记录来自前端的由用户发起的请求，以处理发送到 Azure Cosmos DB 的用于表的 API 的请求。 在启用此类别时，请确保禁用 DataPlaneRequests。       |    `operationName`, `requestCharge`, `piiCommandText`     |


## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> 通过 REST API 创建诊断设置
使用 [Azure Monitor REST API](/rest/api/monitor/diagnosticsettings/createorupdate) 通过交互式控制台创建诊断设置。
> [!Note]
> 如果使用 SQL API，建议将 logAnalyticsDestinationType 属性设置为“专用”，以便启用特定于资源的表 。

### <a name="request"></a>请求

```HTTP
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

### <a name="headers"></a>头文件

|参数/标头  | 值/说明  |
|---------|---------|
|name     |  诊断设置的名称。      |
|resourceUri     |   subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}      |
|api-version     |    2017-05-01-preview     |
|Content-Type     |    application/json     |

### <a name="body"></a>正文

```json
{
    "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}",
    "type": "Microsoft.Insights/diagnosticSettings",
    "name": "name",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": null,
        "serviceBusRuleId": null,
        "workspaceId": "/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "logs": [
            {
                "category": "DataPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "QueryRuntimeStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyRUConsumption",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ControlPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ],
        "logAnalyticsDestinationType": "Dedicated"
    },
    "identity": null
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>通过 Azure CLI 创建诊断设置
在 Azure CLI 中使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 命令来创建诊断设置。 有关参数说明，请参阅此命令的文档。

> [!Note]
> 如果使用 SQL API，建议将 export-to-resource-specific 属性设置为 true 。

```azurecli-interactive
az monitor diagnostic-settings create --resource /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/ --name {DIAGNOSTIC_SETTING_NAME} --export-to-resource-specific true --logs '[{"category": "QueryRuntimeStatistics","categoryGroup": null,"enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]' --workspace /subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}"
```

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何查询特定于资源的表，请参阅[排查使用特定于资源的表时出现的问题](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries)。

* 若要详细了解如何查询 AzureDiagnostics 表，请参阅[排查使用 AzureDiagnostics 表时出现的问题](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries)。

* 若要详细了解如何通过使用 Azure 门户、CLI 或 PowerShell 来创建诊断设置，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)一文。