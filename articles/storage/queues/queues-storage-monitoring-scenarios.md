---
title: 有关监视 Azure 队列存储的最佳做法
description: 了解最佳做法指导，学习在使用指标和日志监视 Azure 队列存储时如何运用这些指导。
author: normesta
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.author: normesta
ms.date: 08/24/2021
ms.custom: monitoring
ms.openlocfilehash: e7344330740bebc41a49547c7602a852b5f7665f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868466"
---
# <a name="best-practices-for-monitoring-azure-queue-storage"></a>有关监视 Azure 队列存储的最佳做法

本文介绍一系列常用的队列存储监视方案，并提供实现这些方案的最佳做法指导。  

## <a name="monitor-message-counts-in-each-queue"></a>监视每个队列中的消息计数 

可使用 `QueueMessageCount` 指标来监视存储帐户中所有队列的消息计数。 该指标每日刷新一次。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如果使用 PowerShell，可使用如下所示的命令：  

```powershell
(Get-AzMetric -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/queueServices/default -MetricName "QueueMessageCount").data.Average
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果使用 Azure CLI，可使用如下所示的命令： 

```azurecli
az monitor metrics list --resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/ --metric "QueueMessageCount" --interval PT1H --query value[0].timeseries[0].data[0].average
```

---

如果需要动态确定是否调整工作负载来处理消息量，可查询每个队列的大致消息计数，然后使用相应的操作进行响应。 使用[获取队列元数据](/rest/api/storageservices/get-queue-metadata) REST 操作或使用任何受支持的 Blob 存储 SDK 来获取大致的消息计数。 

以下示例使用 Azure 存储 .NET v12 库来获取大致的消息计数。

```csharp
        static async Task<string> RetrieveNextMessageAsync(QueueClient theQueue)
        {
            if (await theQueue.ExistsAsync())
            {
                QueueProperties properties = await theQueue.GetPropertiesAsync();

                if (properties.ApproximateMessagesCount > 0)
                {
                    QueueMessage[] retrievedMessage = await theQueue.ReceiveMessagesAsync(1);
                    string theMessage = retrievedMessage[0].MessageText;
                    await theQueue.DeleteMessageAsync(retrievedMessage[0].MessageId, retrievedMessage[0].PopReceipt);
                    return theMessage;
                }

                return null;
            }

            return null;
        }
```

还请考虑使用支持每实体消息的服务总线。 若要了解详细信息，请参阅[监视 Azure 服务总线数据参考](../../service-bus-messaging/monitor-service-bus-reference.md)。 

## <a name="audit-account-activity"></a>审核帐户活动

在许多情况下，需要审核存储帐户的活动以确保安全性与合规性。 针对存储帐户的操作分为两种类别：控制平面操作和数据平面操作 。 

控制平面操作是用于创建存储帐户或更新现有存储帐户的属性的任何 Azure 资源管理器请求。 有关详细信息，请参阅 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)。 

数据平面操作是由于对存储服务终结点发出的请求而针对存储帐户中的数据执行的操作。 例如，向队列添加消息时，将执行数据平面操作。 有关详细信息，请参阅 [Azure 存储 API](/rest/api/storageservices/)。 

本部分介绍如何识别控制平面和数据平面操作的“时间”、“用户”、“内容”和“方式”信息。

### <a name="auditing-control-plane-operations"></a>审核控制平面操作

资源管理器操作会捕获到 [Azure 活动日志](../../azure-monitor/essentials/activity-log.md)中。 若要查看活动日志，请在 Azure 门户中打开你的存储帐户，然后选择“活动日志”。

> [!div class="mx-imgBorder"]
> ![活动日志](./media/queues-storage-monitoring-scenarios/activity-log.png)


打开任一日志条目以查看描述该活动的 JSON。 以下 JSON 显示了控制平面操作的“时间”、“内容”和“方式”信息：

> [!div class="mx-imgBorder"]
> ![活动日志 JSON](./media/queues-storage-monitoring-scenarios/activity-log-json.png)

是否提供“用户”信息取决于用于执行控制平面操作的身份验证方法。 如果授权是由 Azure AD 安全主体执行的，则该安全主体的对象标识符也会显示在此 JSON 输出中（例如：`"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`）。 由于你不一定总能看到其他与标识相关的信息（例如电子邮件地址或姓名），因此对象标识符始终是唯一标识安全主体的最佳方式。 

可以通过获取对象标识符的值并在 Azure 门户的“Azure AD”页中搜索安全主体，找到该安全主体的易记名称。 以下屏幕截图显示了 Azure AD 中的搜索结果。

> [!div class="mx-imgBorder"]
> ![搜索 Azure Active Directory](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>审核数据平面操作

数据平面操作会捕获到[适用于存储的 Azure 资源日志](monitor-queue-storage.md#analyzing-logs)中。 可以[配置诊断设置](monitor-queue-storage.md#send-logs-to-azure-log-analytics)以将日志导出到 Log Analytics 工作区，从而获得本机查询体验。 

以下 Log Analytics 查询将检索日志条目列表中的“时间”、“用户”、“内容”和“方式”信息。 

```kusto
StorageQueueLogs 
| where TimeGenerated > ago(3d) 
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

对于审核的“时间”部分，`TimeGenerated` 字段显示日志条目的记录时间。 

对于审核的“内容”部分，`Uri` 字段显示已修改或读取的项。 

对于审核的“方式”部分，`OperationName` 字段显示执行的操作。 

对于审核的“用户”部分，`AuthenticationType` 显示用于发出请求的身份验证类型。 此字段可以显示 Azure 存储支持的任何身份验证类型，包括使用帐户密钥、SAS 令牌或 Azure Active Directory (Azure AD) 身份验证。 

如果已使用 Azure AD 对请求进行身份验证，则 `RequesterObjectId` 字段将提供用于标识安全主体的最可靠方式。 可以通过获取 `RequesterObjectId` 字段的值并在 Azure 门户的“Azure AD”页中搜索安全主体，找到该安全主体的易记名称。 以下屏幕截图显示了 Azure AD 中的搜索结果。

> [!div class="mx-imgBorder"]
> ![搜索 Azure Active Directory-2](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

在某些情况下，用户主体名称 (UPN) 可能会显示在日志中。 例如，如果安全主体是 Azure AD 用户，则可能会显示 UPN。 对于其他类型的安全主体（例如用户分配的托管标识，或某些方案中使用的跨 Azure AD 租户身份验证），UPN 不会显示在日志中。 

此查询显示 OAuth 安全主体执行的所有写入操作。

```kusto
StorageQueueLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutMessage"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

共享密钥和 SAS 身份验证不提供审核单个标识的方式。 因此，如果你要提高基于标识进行审核的能力，我们建议过渡到 Azure AD，并阻止共享密钥和 SAS 身份验证。 若要了解如何阻止共享密钥和 SAS 身份验证，请参阅[阻止对 Azure 存储帐户进行共享密钥授权](../common/shared-key-authorization-prevent.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json&tabs=portal)。 若要开始使用 Azure AD，请参阅[使用 Azure Active Directory 授权访问 Blob](authorize-access-azure-active-directory.md)

## <a name="optimize-cost-for-infrequent-queries"></a>优化不经常使用的查询的成本

可将日志导出到 Log Analytics 以使用丰富的本机查询功能。 如果存储帐户中有大量的事务，在 Log Analytics 中使用日志所产生的成本可能很高。 参阅 [Azure Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor/)。 如果你仅打算偶尔查询日志（例如，出于合规性审核目的查询日志），可以考虑将日志导出到存储帐户，然后基于日志数据使用某种无服务器查询解决方案（例如 Azure Synapse），从而降低总体成本。

借助 Azure Synapse，可以创建无服务器 SQL 池以按需查询日志数据。 这可以大幅节省成本。 

1. 将日志导出到存储帐户。 参阅[创建诊断设置](monitor-queue-storage.md#creating-a-diagnostic-setting)。

2. 创建并配置 Synapse 工作区。 参阅[快速入门：创建 Synapse 工作区](../../synapse-analytics/quickstart-create-workspace.md)。

2. 查询日志。 参阅[在 Azure Synapse Analytics 中使用无服务器 SQL 池查询 JSON 文件](../../synapse-analytics/sql/query-json-files.md)。

   下面是一个示例：

   ```sql
    select
        JSON_VALUE(doc, '$.time') AS time,
        JSON_VALUE(doc, '$.properties.accountName') AS accountName,
        JSON_VALUE(doc, '$.identity.type') AS identityType,    
        JSON_VALUE(doc, '$.identity.requester.objectId') AS requesterObjectId,
        JSON_VALUE(doc, '$.operationName') AS operationName,
        JSON_VALUE(doc, '$.callerIpAddress') AS callerIpAddress,
        JSON_VALUE(doc, '$.uri') AS uri
        doc
    from openrowset(
            bulk 'https://demo2uswest4log.blob.core.windows.net/insights-logs-storageread/resourceId=/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/demo2uswest/blobServices/default/y=2021/m=03/d=19/h=*/m=*/PT1H.json',
            format = 'csv', fieldterminator ='0x0b', fieldquote = '0x0b'
        ) with (doc nvarchar(max)) as rows
    order by JSON_VALUE(doc, '$.time') desc

   ```

## <a name="see-also"></a>另请参阅

- [监视 Azure 队列存储](monitor-queue-storage.md)。
- [Azure 队列存储监视数据参考](monitor-queue-storage-reference.md)
- [教程：在 Azure 数据资源管理器和 Azure Monitor 中使用 Kusto 查询](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)。
- [Azure Monitor 中的日志查询入门](../../azure-monitor/logs/get-started-queries.md)。

  

