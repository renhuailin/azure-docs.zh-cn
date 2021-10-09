---
title: 使用 Azure 诊断设置监视 Azure Cosmos DB 数据
description: 了解如何使用 Azure 诊断设置来监视 Azure Cosmos DB 中存储的数据的性能和可用性
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 55e84478d8744aae05f8f3a0df89aac605d6de12
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779913"
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
> 我们建议[按照通过 REST API 创建诊断设置的说明](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting)，在特定于资源的模式下（除表 API 外的所有 API）创建诊断设置。 此选项通过改进的数据处理视图提供了额外的成本优化。

## <a name="create-diagnostics-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> 通过 Azure 门户创建诊断设置

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 导航到 Azure Cosmos 帐户。 打开“监视”部分下的“诊断设置”窗格，然后选择“添加诊断设置”选项  。

   :::image type="content" source="./media/monitor-cosmos-db/diagnostics-settings-selection.png" alt-text="选择诊断":::


3. 在“诊断设置”窗格中，用你喜欢的类别填充窗体。

### <a name="choose-log-categories"></a>选择日志类别

   |类别  |API   | 定义  | 键属性   |
   |---------|---------|---------|---------|
   |DataPlaneRequests     |  所有 API        |     将后端请求（为了创建、更新、删除或检索帐户中的数据而执行的请求）记录为数据平面操作。   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
   |MongoRequests     |    Mongo    |   记录来自前端的由用户发起的请求，以处理发送到 Azure Cosmos DB 的用于 MongoDB 的 API 的请求。 在启用此类别时，请确保禁用 DataPlaneRequests。      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
   |CassandraRequests     |   Cassandra      |    记录来自前端的由用户发起的请求，以处理发送到 Azure Cosmos DB 的用于 Cassandra 的 API 的请求。 在启用此类别时，请确保禁用 DataPlaneRequests。     |     `operationName`, `requestCharge`, `piiCommandText`    |
   |GremlinRequests     |    Gremlin    |     记录来自前端的由用户发起的请求，以处理发送到 Azure Cosmos DB 的用于 Gremlin 的 API 的请求。 在启用此类别时，请确保禁用 DataPlaneRequests。    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
   |QueryRuntimeStatistics     |   SQL      |     此表详细介绍了针对 SQL API 帐户执行的查询操作。 默认情况下，会对查询文本及其参数进行模糊处理，以免记录个人数据以及可以通过请求访问的全文查询日志记录。    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
   |PartitionKeyStatistics     |    所有 API     |   通过表示分区键的估计存储大小 (KB) 来记录逻辑分区键的统计信息。 此表在排查存储倾斜的问题时非常有用。 仅当满足以下条件时才发出此 PartitionKeyStatistics 日志： <br/><ul><li> 物理分区中至少有 1% 的文档具有相同的逻辑分区键。 </li><li> 在物理分区的所有键中，PartitionKeyStatistics 日志会捕获存储大小最大的前 3 个键。 </li></ul> 如果不符合上述条件，分区键统计数据将不可用。 如果帐户不满足上述条件也没关系，这通常表示不存在逻辑分区存储倾斜。 <br/><br/>注意：使用采样方法计算分区键的估计大小，该方法假定物理分区中的文档的大小大致相同。 如果物理分区中的文档的大小不一致，则估计的分区键大小可能不准确。  |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
   |PartitionKeyRUConsumption     |   SQL API    |     记录分区键的每秒 RU 聚合消耗量。 此表对于排查热分区的问题很有用。 目前，Azure Cosmos DB 仅报告 SQL API 帐户的分区键，以及时点读取/写入和存储过程操作。   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
   |ControlPlaneRequests     |   所有 API       |    记录有关控制平面操作（即，创建帐户、添加或删除区域、更新帐户复制设置等）的详细信息。     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
   |TableApiRequests     |   表 API    |     记录来自前端的由用户发起的请求，以处理发送到 Azure Cosmos DB 的用于表的 API 的请求。 在启用此类别时，请确保禁用 DataPlaneRequests。       |    `operationName`, `requestCharge`, `piiCommandText`     |

4. 选择“类别详细信息”后，将日志发送到首选目标。 如果要将日志发送到“Log Analytics 工作区”，请确保选择“特定于资源”作为目标表 。

    :::image type="content" source="./media/monitor-cosmos-db/diagnostics-resource-specific.png" alt-text="选择“启用特定于资源”":::

## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> 通过 REST API 创建诊断设置
使用 [Azure Monitor REST API](/rest/api/monitor/diagnosticsettings/createorupdate) 通过交互式控制台创建诊断设置。
> [!Note]
> 建议将“logAnalyticsDestinationType”属性设置为“专用”以启用特定于资源的表 。

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
## <a name="enable-full-text-query-for-logging-query-text"></a><a id="full-text-query"></a> 启用全文查询以记录查询文本

> [!Note]
> 启用此功能可能会产生额外的日志记录成本，有关定价详细信息，请访问 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。 建议在故障排除后禁用此功能。

Azure Cosmos DB 提供高级日志记录以进行详细的故障排除。 启用全文查询功能后，你将能够查看 Azure Cosmos DB 帐户中所有请求的已取消模糊处理的查询。  你还将为 Azure Cosmos DB 提供权限，以访问并在日志中显示此数据。 

1. 若要启用此功能，请导航到 Cosmos DB 帐户中的 `Features` 边栏选项卡。
   
   :::image type="content" source="./media/monitor-cosmos-db/full-text-query-features.png" alt-text="导航到“功能”边栏选项卡":::

2. 选择 `Enable`，接下来的几分钟内将应用此设置。 所有新引入的日志都将具有每个请求的全文或 PIICommand 文本。
   
    :::image type="content" source="./media/monitor-cosmos-db/select-enable-full-text.png" alt-text="选择启用全文":::

若要了解如何使用此新启用的功能进行查询，请访问[高级查询](cosmos-db-advanced-queries.md)。

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何查询特定于资源的表，请参阅[排查使用特定于资源的表时出现的问题](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries)。

* 若要详细了解如何查询 AzureDiagnostics 表，请参阅[排查使用 AzureDiagnostics 表时出现的问题](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries)。

* 若要详细了解如何使用 Azure 门户、CLI 或 PowerShell 创建诊断设置，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)一文。
