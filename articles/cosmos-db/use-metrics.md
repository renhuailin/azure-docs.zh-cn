---
title: 使用 Azure Cosmos DB 中的见解进行监视和调试
description: 使用 Azure Cosmos DB 中的指标调试常见问题和监视数据库。
ms.author: esarroyo
author: StefArroyo
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/14/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e85fa72288ab31fd8e61fcda731debb3517057f
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393343"
---
# <a name="monitor-and-debug-with-insights-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的见解进行监视和调试
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 提供吞吐量、存储、一致性、可用性和延迟方面的见解。 Azure 门户提供这些指标的聚合视图。 也可通过 Azure Monitor API 查看 Azure Cosmos DB 指标。 指标的维度值（例如容器名称）不区分大小写。 因此，在对这些维度值进行字符串比较时，需要使用不区分大小写的比较。 若要了解如何查看 Azure Monitor 中的指标，请参阅[获取 Azure Monitor 中的指标](./monitor-cosmos-db.md)一文。

本文介绍常见用例，以及如何使用 Azure Cosmos DB 见解来分析和调试这些问题。 默认情况下，指标见解每五分钟收集一次，并保留 7 天。

## <a name="view-insights-from-azure-portal"></a>从 Azure 门户查看见解

1. 登录到 [Azure 门户](https://portal.azure.com/)，导航到你的 Azure Cosmos DB 帐户。

1. 可以从“指标”窗格或“见解(预览版)”窗格中查看帐户指标 。

   * 指标：此窗格提供定期收集的数字指标，用于描述系统在某一特定时间的某些情况。 例如，可以查看和监视[服务器端延迟指标](monitor-server-side-latency.md)、[标准化请求单位使用指标](monitor-normalized-request-units.md)等。

   * 见解(预览版)：此窗格为 Azure Cosmos DB 提供自定义监视体验。 见解使用 Azure Monitor 中收集的指标和日志，并显示帐户的聚合视图。

1. 打开“见解(预览版)”窗格。 默认情况下，“见解”窗格会显示帐户中每个容器的吞吐量、请求、存储、可用性、延迟、系统和账户管理指标。 可以选择要查看其见解的时间范围、数据库和容器  。 “概述”选项卡显示所选数据库和容器的 RU/s 使用情况、数据使用情况、索引使用情况、限制请求和标准化 RU/s 使用。

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Azure 门户中的 Cosmos DB 性能指标" lightbox="./media/use-metrics/performance-metrics.png" :::

1. “见解”窗格提供以下指标：

   * 吞吐量 - 此选项卡显示已使用或已失败（因为已超出为容器预配的吞吐量或存储容量，响应代码为 429）的请求单元总数。

   * 请求 - 此选项卡显示按状态代码、操作类型和失败请求数（响应代码 429）处理的请求总数。 超出为容器预配的吞吐量或存储容量时，请求将失败。

   * 存储 - 此选项卡显示所选时间段内的数据大小和索引使用情况。

   * 可用性 - 此选项卡显示每小时成功请求数占总请求数的百分比。 成功率按 Azure Cosmos DB SLA 定义。

   * 延迟 - 此选项卡显示 Azure Cosmos DB 在帐户运行区域观察到的读写延迟。 可以针对异地复制帐户跨区域将延迟可视化。 还可以通过其他操作将服务器端延迟可视化。 此指标不表示端到端请求延迟。

   * 系统 - 此选项卡显示主分区处理的元数据请求数。 此指标还有助于确定限制的请求数。

   * 帐户管理 - 此选项卡显示帐户管理活动的指标，例如帐户创建、删除、密钥更新、网络和复制设置。

以下部分介绍可以使用 Azure Cosmos DB 指标的常见场景。

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>了解成功的请求数或导致错误的请求数

若要开始，请前往 [Azure 门户](https://portal.azure.com)并导航到“见解”边栏选项卡。 在此边栏选项卡中，打开“请求”选项卡，它会显示一个图表，其中包含按状态代码和操作类型细分的请求总数。 有关 HTTP 状态代码的详细信息，请参阅 [Azure Cosmos DB 的 HTTP 状态代码](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)。

最常见的错误状态代码为 429（速率限制）。 此错误意味着对 Azure Cosmos DB 的请求超过预配的吞吐量。 此问题最常见的解决方案是为给定集合[纵向扩展 RU](./set-throughput.md)。

:::image type="content" source="media/use-metrics/request-count.png" alt-text="每分钟的请求数" lightbox= "media/use-metrics/request-count.png":::

## <a name="determine-the-throughput-consumption-by-a-partition-key-range"></a>按分区键范围确定吞吐量使用量

对任何可伸缩应用程序而言，均必须具有良好的分区键基数。 若要确定任何由分区键范围 ID 细分为分区容器的吞吐量分布，请导航到“见解(预览版)”窗格。 打开“吞吐量”选项卡，图表中将显示不同分区键范围内的标准化 RU/s 使用量。

:::image type="content" source="media/use-metrics/throughput-consumption-partition-key-range.png" alt-text="按分区键范围 ID 划分的标准化吞吐量使用量" lightbox="media/use-metrics/throughput-consumption-partition-key-range.png":::

使用此图表可确定是否存在热分区。 吞吐量分布不均可能导致热分区，进而造成请求受阻和需要重新分区  。 确定导致分布倾斜的分区键之后，可能需使用进一步分布的分区键重新执行容器分区。 若要深入了解如何在 Azure Cosmos DB 中进行分区，请参阅[在 Azure Cosmos DB 中进行分区和缩放](./partitioning-overview.md)。

## <a name="determine-the-data-and-index-usage"></a>确定数据和索引使用情况

务必根据数据使用情况、索引使用情况和文档使用情况确定任何分区容器的存储分布。 可以最大限度地减少索引使用量，最大限度地增加数据使用量并优化查询。 若要获取此数据，请导航到“见解(预览版)”窗格并打开“存储”选项卡 ：

:::image type="content" source="media/use-metrics/data-index-consumption.png" alt-text="数据、索引和文档使用" lightbox="media/use-metrics/data-index-consumption.png" :::

## <a name="compare-data-size-against-index-size"></a>比较数据与索引的大小

在 Azure Cosmos DB 中，所用存储空间总量是指数据大小和索引大小的总和。 索引大小通常只是数据大小的一小部分。 若要了解详细信息，请参阅[索引大小](index-policy.md#index-size)一文。 在 [Azure 门户](https://portal.azure.com)的“指标”边栏选项卡，“存储”选项卡显示基于数据和索引的存储空间使用量详情。

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

若要节省索引空间，可调整[索引策略](index-policy.md)。

## <a name="debug-why-queries-are-running-slow"></a>调试查询运行缓慢的原因

在 SQL API SDK 中，Azure Cosmos DB 提供查询执行的统计信息。

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

QueryMetrics 提供执行各查询组件所用时长的详细信息  。 导致查询长时间运行的最常见根因是扫描，这意味着查询无法利用索引。 可通过设置更好的筛选条件来解决此问题。

## <a name="next-steps"></a>后续步骤

前文介绍了如何使用 Azure 门户中提供的指标来监视和调试问题。 以下文章进一步介绍了如何提高数据库性能：

* 若要了解如何查看 Azure Monitor 中的指标，请参阅[获取 Azure Monitor 中的指标](./monitor-cosmos-db.md)一文。 
* [执行 Azure Cosmos DB 缩放和性能测试](performance-testing.md)
* [Azure Cosmos DB 性能提示](performance-tips.md)