---
title: 有关监视 Azure Blob 存储的最佳做法
description: 了解最佳做法指导，以及在使用指标和日志监视 Azure Blob 存储时如何运用这些指导。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 07/30/2021
ms.custom: monitoring
ms.openlocfilehash: 8a4d6ab98bd126d2b8ec98650821d191dec4ed6d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771407"
---
# <a name="best-practices-for-monitoring-azure-blob-storage"></a>有关监视 Azure Blob 存储的最佳做法

本文介绍一系列常用的存储监视方案，并提供用于实现这些方案的最佳做法指导。  

## <a name="identify-storage-accounts-with-no-or-low-use"></a>识别不使用的或很少使用的存储帐户

存储见解是基于 Azure 存储指标和日志构建的仪表板。 可以使用存储见解来检查所有帐户的事务量和已用容量。 这些信息可帮助你确定要停用哪些帐户。 若要配置存储见解，请参阅[使用 Azure Monitor 存储见解监视存储服务](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)。 

### <a name="analyze-transaction-volume"></a>分析事务量

通过 [Azure Monitor 中的存储见解视图](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json#view-from-azure-monitor)，使用“事务”列将帐户按升序排序。 下图显示了在指定时段内事务量较低的帐户。 

> [!div class="mx-imgBorder"]
> ![存储见解中的事务量](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-volume.png)

单击帐户链接了解有关这些事务的详细信息。 在此示例中，大多数请求都是对 Blob 存储服务发出的。 

> [!div class="mx-imgBorder"]
> ![按服务类型列出的事务](./media/blob-storage-monitoring-scenarios/storage-insights-transactions-by-storage-type.png)

若要确定发出的请求类型，请深入钻取到“按 API 名称列出的事务”图表。 

> [!div class="mx-imgBorder"]
> ![存储事务 API](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-apis.png)

在此示例中，所有请求都是列出操作或者对帐户属性信息的请求。 没有读取和写入事务。 这可能会让你觉得该帐户未得到有效利用。 

### <a name="analyze-used-capacity"></a>分析已用容量

通过  [Azure Monitor 中的存储见解视图](../common/storage-insights-overview.md#view-from-azure-monitor)的“容量”选项卡 ，使用“帐户已用容量”列将帐户按升序排序 。 下图显示某个帐户的容量低于其他帐户。 

> [!div class="mx-imgBorder"]
> ![已用存储容量](./media/blob-storage-monitoring-scenarios/storage-insights-capacity-used.png)

若要检查与此已用容量关联的 Blob，可以使用存储资源管理器。 如果有大量的 Blob，请考虑使用 [Blob 清单策略](blob-inventory.md)生成报告。 

## <a name="monitor-the-use-of-a-container"></a>监视容器的使用

如果按容器将客户数据分区，则可以监视每个客户使用了多少容量。 可以使用 Azure 存储 Blob 清单来创建包含大小信息的 Blob 清单。 然后，可以在容器级别聚合大小和计数。 有关示例，请参阅[使用 Azure 存储空间清单计算每个容器的 Blob 计数和总大小](calculate-blob-count-size.md)。

还可以通过查询日志来计算容器级别的流量。 若要详细了解如何编写 Log Analytics 查询，请参阅 [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)。 若要详细了解存储日志架构，请参阅 [Azure Blob 存储监视数据参考](monitor-blob-storage-reference.md#resource-logs-preview)。

以下查询获取读取事务数量，以及在每个容器上读取的字节数。


```kusto
StorageBlobLogs
| where OperationName  == "GetBlob"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize ReadSize = sum(ResponseBodySize), ReadCount = count() by tostring(ContainerName)
```

以下查询使用类似的查询来获取有关写入操作的信息。 

```kusto
StorageBlobLogs
| where OperationName == "PutBlob" or
  OperationName == "PutBlock" or
  OperationName == "PutBlockList" or
  OperationName == "AppendBlock" or 
  OperationName == "SnapshotBlob" or
  OperationName == "CopyBlob" or 
  OperationName == "SetBlobTier" 
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize WriteSize = sum(RequestBodySize), WriteCount = count() by tostring(ContainerName)
```

以上查询引用多个操作的名称，因为有多种类型的操作可以算作写入操作。 若要详细了解哪些操作被视为读取操作和写入操作，请参阅 [Azure Blob 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)或 [Azure Data Lake Storage 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="audit-account-activity"></a>审核帐户活动

在许多情况下，需要审核存储帐户的活动以确保安全性与合规性。 针对存储帐户的操作分为两种类别：控制平面操作和数据平面操作 。 

控制平面操作是用于创建存储帐户或更新现有存储帐户的属性的任何 Azure 资源管理器请求。 有关详细信息，请参阅 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)。 

数据平面操作是由于对存储服务终结点发出的请求而针对存储帐户中的数据执行的操作。 例如，在将 Blob 上传到存储帐户或者从存储帐户下载 Blob 时，就会执行数据平面操作。 有关详细信息，请参阅 [Azure 存储 API](/rest/api/storageservices/)。 

本部分介绍如何识别控制平面和数据平面操作的“时间”、“用户”、“内容”和“方式”信息。

### <a name="auditing-control-plane-operations"></a>审核控制平面操作

资源管理器操作会捕获到 [Azure 活动日志](../../azure-monitor/essentials/activity-log.md)中。 若要查看活动日志，请在 Azure 门户中打开你的存储帐户，然后选择“活动日志”。

> [!div class="mx-imgBorder"]
> ![活动日志](./media/blob-storage-monitoring-scenarios/activity-log.png)


打开任一日志条目以查看描述该活动的 JSON。 以下 JSON 显示了控制平面操作的“时间”、“内容”和“方式”信息：

> [!div class="mx-imgBorder"]
> ![活动日志 JSON](./media/blob-storage-monitoring-scenarios/activity-log-json.png)

是否提供“用户”信息取决于用于执行控制平面操作的身份验证方法。 如果授权是由 Azure AD 安全主体执行的，则该安全主体的对象标识符也会显示在此 JSON 输出中（例如：`"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`）。 由于你不一定总能看到其他与标识相关的信息（例如电子邮件地址或姓名），因此对象标识符始终是唯一标识安全主体的最佳方式。 

可以通过获取对象标识符的值并在 Azure 门户的“Azure AD”页中搜索安全主体，找到该安全主体的易记名称。 以下屏幕截图显示了 Azure AD 中的搜索结果。

> [!div class="mx-imgBorder"]
> ![搜索 Azure Active Directory](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>审核数据平面操作

数据平面操作会捕获到[适用于存储的 Azure 资源日志](monitor-blob-storage.md#analyzing-logs)中。 可以[配置诊断设置](monitor-blob-storage.md#send-logs-to-azure-log-analytics)以将日志导出到 Log Analytics 工作区，从而获得本机查询体验。 

以下 Log Analytics 查询将检索日志条目列表中的“时间”、“用户”、“内容”和“方式”信息。 

```kusto
StorageBlobLogs 
| where TimeGenerated > ago(3d) 
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

对于审核的“时间”部分，`TimeGenerated` 字段显示日志条目的记录时间。 

对于审核的“内容”部分，`Uri` 字段显示已修改或读取的项。 

对于审核的“方式”部分，`OperationName` 字段显示执行的操作。 

对于审核的“用户”部分，`AuthenticationType` 显示用于发出请求的身份验证类型。 此字段可以显示 Azure 存储支持的任何身份验证类型，包括使用帐户密钥、SAS 令牌或 Azure Active Directory (Azure AD) 身份验证。 

如果已使用 Azure AD 对请求进行身份验证，则 `RequesterObjectId` 字段将提供用于标识安全主体的最可靠方式。 可以通过获取 `RequesterObjectId` 字段的值并在 Azure 门户的“Azure AD”页中搜索安全主体，找到该安全主体的易记名称。 以下屏幕截图显示了 Azure AD 中的搜索结果。

> [!div class="mx-imgBorder"]
> ![搜索 Azure Active Directory](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

在某些情况下，用户主体名称 (UPN) 可能会显示在日志中。 例如，如果安全主体是 Azure AD 用户，则可能会显示 UPN。 对于其他类型的安全主体（例如用户分配的托管标识，或某些方案中使用的跨 Azure AD 租户身份验证），UPN 不会显示在日志中。 

此查询显示 OAuth 安全主体执行的所有读取操作。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "GetBlob"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

共享密钥和 SAS 身份验证不提供审核单个标识的方式。 因此，如果你要提高基于标识进行审核的能力，我们建议过渡到 Azure AD，并阻止共享密钥和 SAS 身份验证。 若要了解如何阻止共享密钥和 SAS 身份验证，请参阅[阻止对 Azure 存储帐户进行共享密钥授权](../common/shared-key-authorization-prevent.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=portal)。 若要开始使用 Azure AD，请参阅[使用 Azure Active Directory 授权访问 Blob](authorize-access-azure-active-directory.md)

## <a name="optimize-cost-for-infrequent-queries"></a>优化不经常使用的查询的成本

可将日志导出到 Log Analytics 以使用丰富的本机查询功能。 如果存储帐户中有大量的事务，在 Log Analytics 中使用日志所产生的成本可能很高。 参阅 [Azure Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor/)。 如果你仅打算偶尔查询日志（例如，出于合规性审核目的查询日志），可以考虑将日志导出到存储帐户，然后基于日志数据使用某种无服务器查询解决方案（例如 Azure Synapse），从而降低总体成本。

借助 Azure Synapse，可以创建无服务器 SQL 池以按需查询日志数据。 这可以大幅节省成本。 

1. 将日志导出到存储帐户。 参阅[创建诊断设置](monitor-blob-storage.md#creating-a-diagnostic-setting)。

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

## <a name="see-also"></a>请参阅

- [监视 Azure Blob 存储](monitor-blob-storage.md)。
- [教程：在 Azure 数据资源管理器和 Azure Monitor 中使用 Kusto 查询](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)。
- [Azure Monitor 中的日志查询入门](../../azure-monitor/logs/get-started-queries.md)。

