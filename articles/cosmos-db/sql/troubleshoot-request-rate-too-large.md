---
title: 对 Azure Cosmos DB 请求速率过大异常进行故障排除
description: 了解如何诊断和修复请求速率过大异常。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/25/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0c8ad195ce2e5288857595b042bb30cf6b5c8a6d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618392"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-429-exceptions"></a>对 Azure Cosmos DB 请求速率过大 (429) 异常进行诊断及故障排除
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文包含 SQL API 的各种 429 状态代码错误的已知原因和解决方案。 若使用适用于 MongoDB 的 API，请参阅[对适用于 MongoDB 的 API 中的常见问题进行疑难解答](../mongodb/error-codes-solutions.md)一文，了解如何调试状态代码 16500。

“请求速率过大”异常（也称为“错误代码 429”）表示，针对 Azure Cosmos DB 提出请求时，请求速率受限。

若要使用预配吞吐量，可以工作负载所需的每秒请求单位 (RU/s) 为单位设置吞吐量。 针对服务的数据库操作（如读取、写入和查询）会消耗一定数量的请求单元 (RU)。 详细了解[请求单元](../request-units.md)。

在某一秒中，如果操作消耗的请求单位数超过预配数，Azure Cosmos DB 将返回 429 异常。 每秒重置可用的请求单位数。

在采取措施更改 RU/s 之前，必须了解速率受限的根本原因并解决根本问题。  

存在对应不同类型的 429 异常的不同错误消息：
- [请求速率很大。可能需要更多请求单位，因此未进行更改。](#request-rate-is-large)
- [由于元数据请求速率较高，请求未完成。](#rate-limiting-on-metadata-requests)
- [由于暂时性服务错误，请求未完成。](#rate-limiting-due-to-transient-service-error)


## <a name="request-rate-is-large"></a>请求速率太大
这是最常见的情况。 当数据操作消耗的请求单位数超过预配的 RU/s 数时，即会发生这种情况。 

### <a name="step-1-check-the-metrics-to-determine-the-percentage-of-requests-with-429-error"></a>第 1 步：检查指标，确定出现 429 错误的请求百分比
看到 429 错误消息并不一定表示数据库或容器存在问题。

#### <a name="how-to-investigate"></a>调查方式

与成功请求的总计数进行比较，确定出现 429 错误的数据库或容器请求所占的百分比。 从“Azure Cosmos DB 帐户”边栏选项卡中，导航到“见解” > “请求” > “按状态代码显示的请求总数”。 筛选到特定数据库和容器。 

默认情况下，Azure Cosmos DB 客户端 SDK 和数据导入工具（如 Azure 数据工厂和批量执行工具库）会自动重试提交出现 429 错误的请求。 重试次数通常最多 9 次。 因此，虽然指标中可能会显示 429 错误，但这些错误甚至可能尚未返回到应用程序。 

:::image type="content" source="media/troubleshoot-request-rate-too-large/insights-429-requests.png" alt-text="“按状态代码显示的请求总数”图表，其中显示的是出现 429 和 2xx 错误的请求数。":::


#### <a name="recommended-solution"></a>建议的解决方案
一般情况下，对于生产工作负载而言，如果看到出现 429 代码的请求百分比在 1% - 5% 之间，并且端到端延迟可接受，那么这就是一个可以表明 RU/s 受到充分利用的良好迹象。 不需要执行任何操作。 否则，请继续执行接下来的故障排除步骤。

### <a name="step-2-determine-if-there-is-a-hot-partition"></a>第 2 步：确定是否存在热分区
当一个或几个逻辑分区键由于请求量较高而消耗了过多的总 RU/s 时，即会出现热分区。 而造成这一情况的原因可能是分区键设计导致请求未能均匀分布。 这就导致许多请求被定向到一小部分逻辑（亦指物理）分区)，因而分区变“热”。 由于逻辑分区的所有数据都驻留在一个物理分区上，并且总 RU/s 在物理分区间均匀分布，因此热分区便可能会导致出现 429 错误以及吞吐量利用效率较低。 

下面是导致热分区的一些分区策略示例：
- 你有一个用于存储 IoT 设备写入密集型工作负载数据的容器，并按 `date` 进行分区。 单个日期的所有数据将驻留在同一逻辑分区和物理分区中。 由于每天写入的所有数据的日期都相同，因此会导致每天都产生热分区。 
    - 对于这种情况，使用 `id` 等分区键（GUID 或设备 ID）或是组合 `id` 和 `date` 的[合成分区键](./synthetic-partition-keys.md)反而将生成更高的值基数，并更均匀地分布请求量。
- 你有一个多租户方案，使用的容器按 `tenantId` 进行分区。 如果一个租户的活跃性明显高于其他租户，则会导致热分区。 举例来说，如果最大租户有 100,000 名用户，但大多数租户的用户数少于 10 人，则按 `tenantID` 分区时，将会产生热分区。 
    - 对于上述方案，应考虑为最大租户创建专用容器，并按更精细的属性（如 `UserId`）进行分区。 
    
#### <a name="how-to-identify-the-hot-partition"></a>如何识别热分区

若要验证是否存在热分区，请导航到“见解” > “吞吐量” > “按分区键范围 ID 显示的规范化 RU 消耗量 (%)”。 筛选到特定数据库和容器。 

每个分区键范围 ID 映射到一个物理分区。 如果有一个分区键范围 ID 的规范化 RU 消耗量明显高于其他分区键范围 ID（例如，有一个一直为 100%，但另一个为 30% 或更低），则这可能就是存在热分区的标志。 详细了解[规范化 RU 消耗量指标](../monitor-normalized-request-units.md)。

:::image type="content" source="media/troubleshoot-request-rate-too-large/split-norm-utilization-by-pkrange-hot-partition.png" alt-text="按分区键范围 ID 显示的规范化 RU 消耗量图表，其中存在热分区。":::

如需了解消耗 RU/s 最多的逻辑分区键，请查阅 [Azure 诊断日志](../cosmosdb-monitor-resource-logs.md)。 此示例查询汇总了每个逻辑分区键每秒消耗的请求单位总数。 

> [!IMPORTANT]
> 启用诊断日志将单独产生 Log Analytics 服务费（按引入的数据量计费）。 我们建议你在有限时间内启用诊断日志进行调试；若不再需要时，建议关闭诊断日志。 请参阅[定价页面](https://azure.microsoft.com/pricing/details/monitor/)了解详细信息。

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24hour)
| where Category == "PartitionKeyRUConsumption"
| where collectionName_s == "CollectionName" 
| where isnotempty(partitionKey_s)
// Sum total request units consumed by logical partition key for each second
| summarize sum(todouble(requestCharge_s)) by partitionKey_s, operationType_s, bin(TimeGenerated, 1s)
| order by sum_requestCharge_s desc
```
此示例输出显示，在某一分钟内，值为“Contoso”的逻辑分区键的消耗量约为 12,000 RU/s，而值为“Fabrikam”的逻辑分区键的消耗量则少于 600 RU/s。 如果此模式在速率受限发生期间一直存在，则表明存在热分区。 

:::image type="content" source="media/troubleshoot-request-rate-too-large/hot-logical-partition-key-results.png" alt-text="每秒消耗最多请求单位数的逻辑分区键。":::

> [!TIP]
> 在任何工作负载中，跨逻辑分区的请求量都存在自然变化。 你应当确定导致产生热分区的原因是因分区键选择造成的基本偏斜（可能需要更改分区键）还是因工作负荷模式的自然变化而暂时出现峰值。

#### <a name="recommended-solution"></a>建议的解决方案
查看有关[如何选择良好分区键](../partitioning-overview.md#choose-partitionkey)的指导。

如果速率受限请求占比较高，并且不存在热分区：
- 可以使用客户端 SDK、Azure 门户、PowerShell、CLI 或 ARM 模板来增加数据库或容器的 [RU/s](../set-throughput.md)。 按照[缩放预配吞吐量（RU/秒）的最佳做法](../scaling-provisioned-throughput-best-practices.md)，确定要设置的正确 RU/秒。

如果速率受限请求的百分比较高，并且存在相关热分区：
-  从长期来看，为获得最佳成本效益和性能，应考虑更改分区键。 由于无法就地更新分区键，因此需要将相关数据迁移到具有不同分区键的新容器。 Azure Cosmos DB 支持使用[实时数据迁移工具](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)来实现此目的。
- 短期而言，可以暂时增加 RU/s，以提高热分区的吞吐量。 不过不建议长期采用此策略，因为这将导致 RU/s 预配过度且成本也有所增加。 

> [!TIP]
>  增加吞吐量时，纵向扩展操作可能会即时或最多需要 5-6 小时完成，具体视要纵向扩展的 RU/s 数量而定。 若要了解在不触发异步纵向扩展操作（需要使用 Azure Cosmos DB 预配更多物理分区）的情况下，可以设置的最高 RU/s 数，请将不同的分区键范围 ID 乘以 100,000 RU/s。 例如，如果已预配 30,000 RU/s 及 5 个物理分区（每个物理分区已分配 6,000 RU/s），则可以在即时纵向扩展操作中将 RU/s 数增至 50,000 RU/s（每个物理分区 10,000 RU/s）。 但若要将 RU/s 数增至 50,000 RU/s 以上，则需要执行异步纵向扩展操作。

### <a name="step-3-determine-what-requests-are-returning-429s"></a>第 3 步：确定返回 429 错误的请求

#### <a name="how-to-investigate-requests-with-429s"></a>如何调查出现 429 错误的请求
使用 [Azure 诊断日志](../cosmosdb-monitor-resource-logs.md)来确定返回 429 错误的请求，以及这些请求消耗的请求单位数。 此示例查询按分钟聚合。 

> [!IMPORTANT]
> 启用诊断日志将单独产生 Log Analytics 服务费（按引入的数据量计费）。 我们建议你在有限时间内启用诊断日志进行调试；若不再需要时，建议关闭诊断日志。 请参阅[定价页面](https://azure.microsoft.com/pricing/details/monitor/)了解详细信息。

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24h)
| where Category == "DataPlaneRequests"
| summarize throttledOperations = dcountif(activityId_g, statusCode_s == 429), totalOperations = dcount(activityId_g), totalConsumedRUPerMinute = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, OperationName, requestResourceType_s, bin(TimeGenerated, 1min)
| extend averageRUPerOperation = 1.0 * totalConsumedRUPerMinute / totalOperations 
| extend fractionOf429s = 1.0 * throttledOperations / totalOperations
| order by fractionOf429s desc
```
例如，此示例输出显示，每分钟有 30% 的创建文档请求的速率受到限制，并且每个请求平均消耗 17 个请求单位。
:::image type="content" source="media/troubleshoot-request-rate-too-large/throttled-requests-diagnostic-logs-results.png" alt-text="诊断日志中出现 429 错误的请求。":::

#### <a name="recommended-solution"></a>建议的解决方案
##### <a name="429s-on-create-replace-or-upsert-document-requests"></a>创建、替换或更新插入文档请求出现 429 错误
- 默认情况下，SQL API 会默认对所有属性都编制索引。 你可以调整[索引策略](../index-policy.md)，以仅对所需属性编制索引。
这将减少每个创建文档操作所需的请求单位数，从而降低看到 429 错误的可能性，或者允许你在使用相同数量预配 RU/s 的情况下，每秒执行更多操作。 

##### <a name="429s-on-query-document-requests"></a>查询文档请求出现 429 错误
- 遵循[对 RU 费用较高的查询进行故障排除](troubleshoot-query-performance.md#querys-ru-charge-is-too-high)的相关指导

##### <a name="429s-on-execute-stored-procedures"></a>执行存储过程时出现 429 错误
- [存储过程](stored-procedures-triggers-udfs.md)适用于需要跨分区键值写入事务的操作。 如果是需要执行大量读取或查询操作的情形，则不建议使用存储过程。 若要获得最佳性能，应在客户端使用 Cosmos SDK 完成这些读取或查询操作。 

## <a name="rate-limiting-on-metadata-requests"></a>元数据请求速率受限

对数据库和/或容器执行大量元数据操作时，可能会发生元数据速率受限问题。 元数据操作包括：
- 创建、读取、更新或删除容器或数据库
- 列出 Cosmos 帐户中的数据库或容器
- 查询产品/服务，以查看当前预配的吞吐量 

这些操作需遵守系统保留的 RU 限制，所以增加数据库或容器的预配 RU/s 将不会产生任何效用，我们也不建议采取此做法。 请参阅[元数据操作限制](../concepts-limits.md#metadata-request-limits)。

#### <a name="how-to-investigate"></a>调查方式
导航到“见解” > “系统” > “按状态代码显示的元数据请求”。 根据需要，筛选到特定数据库和容器。 

:::image type="content" source="media/troubleshoot-request-rate-too-large/metadata-throttling-insights.png" alt-text="“见解”中“按状态代码显示的元数据请求”图表。":::

#### <a name="recommended-solution"></a>建议的解决方案
- 如果应用程序需要执行元数据操作，请考虑实施回退策略以降低该等请求的发送速率。 

- 使用静态 Cosmos DB 客户端实例。 初始化 DocumentClient 或 CosmosClient 时，Cosmos DB SDK 将提取有关该帐户的元数据，包括与一致性级别、数据库、容器、分区以及产品/服务相关的信息。 此初始化可能需要使用大量 RU，不应频繁执行。 在应用程序的生存期内使用单个 DocumentClient 实例。

- 缓存数据库和容器名称。 从配置中检索数据库和容器的名称，或者一开始就将其缓存。 ReadDatabaseAsync/ReadDocumentCollectionAsync 或 CreateDatabaseQuery/CreateDocumentCollectionQuery 等调用将导致元数据调用相关服务，而该等调用将会消耗系统保留的有限 RU。 因此，你应该避免频繁执行此类操作。

## <a name="rate-limiting-due-to-transient-service-error"></a>因暂时性服务错误而导致速率受限

当请求遇到暂时性服务错误时，系统将返回此 429 错误。 增加数据库或容器上的 RU/s 将不会产生任何效用，因此不建议采取此做法。

#### <a name="recommended-solution"></a>建议的解决方案
重试请求。 如果数分钟后错误仍然存在，请从 [Azure 门户](https://portal.azure.com/)提交支持票证。

## <a name="next-steps"></a>后续步骤
* 监视数据库或容器的[规范化 RU/s 消耗量](../monitor-normalized-request-units.md)。
* [诊断和排查](troubleshoot-dot-net-sdk.md)在使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。
* [诊断和排查](troubleshoot-java-sdk-v4-sql.md)使用 Azure Cosmos DB Java v4 SDK 时遇到的问题。
* 了解 [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) 的性能准则。
