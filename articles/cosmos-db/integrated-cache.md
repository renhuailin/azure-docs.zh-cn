---
title: Azure Cosmos DB 集成缓存
description: Azure Cosmos DB 集成缓存是一种内存中缓存，可帮助你在请求量增长时确保成本可管理和低延迟。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.openlocfilehash: 2642f1e85e12ce0251e9b7bfff84b5d468a342d2
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361389"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Azure Cosmos DB 集成缓存 - 概述（预览版）
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 集成缓存是一种内存中缓存，可帮助你在请求量增长时确保成本可管理和低延迟。 集成缓存易于设置，无需花费时间编写用于缓存失效或管理后端基础结构的自定义代码。 集成缓存在 Azure Cosmos DB 帐户中使用[专用网关](dedicated-gateway.md)。 集成缓存是许多 Azure Cosmos DB 功能中第一项利用专用网关提高性能的功能。 可根据工作负载所需的核心数和内存大小，在三种可用的专用网关大小中进行选择。

集成缓存在专用网关上自动配置。 集成缓存包括两个部分： 

* 用于点读取的项缓存 
* 用于查询的查询缓存

集成缓存是一种读写缓存，使用最近最少使用的 (LRU) 逐出策略。 项缓存和查询缓存在集成缓存中共享相同的容量，并且 LRU 逐出策略两者都适用。 换句话说，严格按照最近最少使用数据的时间将数据从缓存中逐出，而不考虑是点读取还是查询。

> [!NOTE]
> 你对集成缓存有何反馈？ 我们想听一听！ 欢迎直接与 Azure Cosmos DB 工程团队分享反馈：cosmoscachefeedback@microsoft.com

## <a name="workloads-that-benefit-from-the-integrated-cache"></a>受益于集成缓存的工作负载

集成缓存的主要目标是降低读取密集型工作负载的成本。 虽然低延迟的优点也有用，但这不是集成缓存的主要优势，因为 Azure Cosmos DB 已经足够块而无需缓存。

命中集成缓存的点读取和查询不会使用任何 RU。 换句话说，任何缓存命中的 RU 费用为 0。 与从后端数据库读取数据比较，缓存命中的每个操作成本要低得多。

符合以下特征的工作负载应评估集成缓存是否有助于降低成本：

-   读取密集型工作负荷
-   针对大型项的多次重复点读取
-   多次重复高 RU 查询
-   用于读取的热分区键

影响预期节省的最大因素是读取重复的程度。 如果工作负载在短时间内持续执行相同的点读取或查询，则它非常适合使用集成缓存。 使用集成缓存进行重复读取时，仅在首次读取时使用 RU。 通过相同专用网关节点（在 `MaxIntegratedCacheStaleness` 窗口中，前提是还未逐出数据）路由的后续读取不会使用吞吐量。

某些工作负载不应考虑使用集成缓存，包括：

-   写入密集型工作负荷
-  极少重复的点读取或查询

## <a name="item-cache"></a>项缓存

可将项缓存用于点读取（即基于项 ID 和分区键的键/值查找）。

### <a name="populating-the-item-cache"></a>填充项缓存

- 新写入、更新和删除会自动在项缓存中填充
- 如果应用尝试读取之前未在缓存中的特定项（缓存失误），则该项现在将存储在项缓存中

### <a name="item-cache-invalidation-and-eviction"></a>项缓存失效和逐出

- 项更新或删除
- 最近最早使用的 (LRU)
- 缓存保留时间（即 `MaxIntegratedCacheStaleness`）

## <a name="query-cache"></a>查询缓存

查询缓存可用于缓存查询。 查询缓存将查询转换为键/值查找，其中键是查询文本，值为查询结果。 集成缓存没有查询引擎，只存储每个查询的键/值查找。

### <a name="populating-the-query-cache"></a>填充查询缓存

- 如果缓存没有该查询（缓存失误）的结果，查询会发送到后端。 运行查询后，缓存将存储该查询的结果

### <a name="query-cache-eviction"></a>查询缓存逐出

- 最近最早使用的 (LRU)
- 缓存保留时间（即 `MaxIntegratedCacheStaleness`）

### <a name="working-with-the-query-cache"></a>使用查询缓存

使用查询缓存时不需要特殊代码，即使查询具有多个结果页也是如此。 无论查询是命中集成缓存还是在后端查询引擎上执行，查询分页的最佳做法和代码都是相同的。

查询缓存会自动缓存查询延续令牌（如果适用）。 如果查询包含多个结果页，在集成缓存中存储的任何页的 RU 费用为 0。 如果后续的查询结果页要求后端执行，它们将具有上一页中的延续令牌，从而避免复制以前的工作。

> [!NOTE]
> 不同专用网关节点内的集成缓存实例具有相互独立的缓存。 如果数据缓存在一个节点中，则不一定会缓存在其他节点中。

## <a name="integrated-cache-consistency"></a>集成缓存一致性

集成缓存仅支持最终[一致性](consistency-levels.md)。 如果读取具有一致的前缀、会话、有限过期或强一致性，它将始终绕过集成缓存。

要为所有读取操作配置最终一致性，最简单的方法是[在帐户级别设置](consistency-levels.md#configure-the-default-consistency-level)最终一致性。 但是，如果只希望某些读取具有最终一致性，也可在[请求级别](how-to-manage-consistency.md#override-the-default-consistency-level)配置一致性。

## <a name="maxintegratedcachestaleness"></a>MaxIntegratedCacheStaleness

`MaxIntegratedCacheStaleness` 是缓存点读取和查询的最大可接受过期时间。 `MaxIntegratedCacheStaleness` 可在请求级别进行配置。 例如，如果将 `MaxIntegratedCacheStaleness` 设置为 2 小时，则仅当数据的有效期小于 2 小时的时候，请求才会返回缓存数据。 若要增加利用集成缓存进行重复读取的可能性，应将 `MaxIntegratedCacheStaleness` 设置为业务要求允许的最高值。

请注意，在最终填充缓存的请求上配置时，`MaxIntegratedCacheStaleness` 不会影响该请求的缓存时间。 `MaxIntegratedCacheStaleness` 在尝试使用缓存数据时强制执行一致性。 没有全局 TTL 或缓存保留设置，因此，只有在集成缓存已满或新读取运行时间的 `MaxIntegratedCacheStaleness` 低于当前缓存条目的保留期时，才会从缓存中逐出数据。

这是对大多数缓存工作方式的改进，并允许以下附加自定义：

- 可以针对每个点读取或查询设置不同的过期时间要求
- 即使运行相同的点读取或查询，不同的客户端也可以配置不同的 `MaxIntegratedCacheStaleness` 值。
- 如果要在使用缓存数据时修改读取一致性，则更改 `MaxIntegratedCacheStaleness` 将立即影响读取一致性。

> [!NOTE]
> 未显式配置时，MaxIntegratedCacheStaleness 默认为 5 分钟。

为了更好地了解 `MaxIntegratedCacheStaleness` 参数，请考虑以下示例：

| 时间       | 请求                                         | 响应                                                     |
| ---------- | ----------------------------------------------- | ------------------------------------------------------------ |
| t = 0 秒  | 运行 MaxIntegratedCacheStaleness = 30 秒的查询 A | 从后端数据库返回结果（正常 RU 费用）并填充缓存     |
| t = 0 秒  | 运行 MaxIntegratedCacheStaleness = 60 秒的查询 B | 从后端数据库返回结果（正常 RU 费用）并填充缓存     |
| t = 20 秒 | 运行 MaxIntegratedCacheStaleness = 30 秒的查询 A | 从集成缓存返回结果（0 RU 费用）           |
| t = 20 秒 | 运行 MaxIntegratedCacheStaleness = 60 秒的查询 B | 从集成缓存返回结果（0 RU 费用）           |
| t = 40 秒 | 运行 MaxIntegratedCacheStaleness = 30 秒的查询 A | 从后端数据库返回结果（正常 RU 费用）并刷新缓存 |
| t = 40 秒 | 运行 MaxIntegratedCacheStaleness = 60 秒的查询 B | 从集成缓存返回结果（0 RU 费用）           |
| t = 50 秒 | 运行 MaxIntegratedCacheStaleness = 20 秒的查询 B | 从后端数据库返回结果（正常 RU 费用）并刷新缓存 |

> [!NOTE]
> 只有最新的 .NET 和 Java 预览版 SDK 支持自定义 `MaxIntegratedCacheStaleness`。

[了解如何配置 `MaxIntegratedCacheStaleness`。](how-to-configure-integrated-cache.md#adjust-maxintegratedcachestaleness)

## <a name="metrics"></a>指标

使用集成缓存时，最好监视一些关键指标。 集成缓存的指标包括：

- `DedicatedGatewayAverageCpuUsage` - 专用网关节点的平均 CPU 使用率。
- `DedicatedGatewayMaxCpuUsage` - 专用网关节点的最大 CPU 使用率。
- `DedicatedGatewayAverageMemoryUsage` - 专用网关节点的平均内存使用量，用于路由请求和缓存数据。
- `DedicatedGatewayRequests` - 所有专用网关实例的专用网关请求总数。
- `IntegratedCacheEvictedEntriesSize` - 由于 LRU 从专用网关节点的集成缓存中逐出的平均数据量。 此值不包括因超过 `MaxIntegratedCacheStaleness` 时间而过期的数据。
- `IntegratedCacheItemExpirationCount` - 由于缓存点读取超过 `MaxIntegratedCacheStaleness` 时间而从集成缓存中逐出的项数。 此值是所有专用网关节点的集成缓存实例的平均值。
- `IntegratedCacheQueryExpirationCount` - 由于缓存查询超过 `MaxIntegratedCacheStaleness` 时间而从集成缓存中逐出的查询数。 此值是所有专用网关节点的集成缓存实例的平均值。
- `IntegratedCacheItemHitRate` - 使用集成缓存的点读取比例（在通过具有最终一致性的专用网关路由的所有点读取中的占比）。 此值是所有专用网关节点的集成缓存实例的平均值。
- `IntegratedCacheQueryHitRate` - 使用集成缓存的查询比例（在通过具有最终一致性的专用网关路由的所有查询中的占比）。 此值是所有专用网关节点的集成缓存实例的平均值。

默认情况下，所有现有指标在“指标”边栏选项卡（不是“经典指标”）中提供：

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="显示集成缓存指标位置的图像" border="false":::

指标是所有专用网关节点的平均值、最大值或总和。 例如，如果预配包含五个节点的专用网关群集，则指标反映所有五个节点的聚合值。 无法确定每个单独节点的指标值。

## <a name="troubleshooting-common-issues"></a>排查常见问题

以下示例显示了如何调试一些常见场景：

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>我无法判断应用程序是否正在使用专用网关

检查 `DedicatedGatewayRequests`。 此指标包括所有使用专用网关的请求，而不考虑请求是否命中集成缓存。 如果应用程序使用标准网关或原始连接字符串的直接模式，则不会出现错误消息，但 `DedicatedGatewayRequests` 将为 0。

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>我无法判断请求是否命中集成缓存

检查 `IntegratedCacheItemHitRate` 和 `IntegratedCacheQueryHitRate`。 如果这两个值都为零，则请求不会命中集成缓存。 检查是否正在使用专用网关连接字符串，[通过网关模式连接](sql-sdk-connection-modes.md)，并[设置了最终一致性](consistency-levels.md#configure-the-default-consistency-level)。

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>我想要了解专用网关是否过小

检查 `IntegratedCacheItemHitRate` 和 `IntegratedCacheQueryHitRate`。 如果这些值较高（例如高于 0.7-0.8），则表示专用网关足够大。

如果 `IntegratedCacheItemHitRate` 或 `IntegratedCacheQueryHitRate` 较低，请查看 `IntegratedCacheEvictedEntriesSize`。 如果 `IntegratedCacheEvictedEntriesSize` 较高，可能表示更大的专用网关大小会更有用。 可通过增加专用网关大小并比较新的 `IntegratedCacheItemHitRate` 和 `IntegratedCacheQueryHitRate` 进行试验。 如果较大的专用网关无法提高 `IntegratedCacheItemHitRate` 或 `IntegratedCacheQueryHitRate`，那么读取操作可能不足以通过重复自身来使集成缓存具有影响力。

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>我想要了解专用网关是否过大

与测量专用网关是否过小相比，更难测量专用网关是否过大。 通常应从较少的量开始，然后慢慢增加专用网关大小，直到 `IntegratedCacheItemHitRate` 和 `IntegratedCacheQueryHitRate` 停止增加。 在某些情况下，两个缓存命中指标中只有一个很重要，而不是两个都重要。 例如，如果工作负载主要是查询，而不是点读取，则 `IntegratedCacheQueryHitRate` 比 `IntegratedCacheItemHitRate` 重要得多。

如果大多数数据是因为超过 `MaxIntegratedCacheStaleness`（而不是 LRU）而从缓存中逐出，则缓存可能大于所需大小。 如果 `IntegratedCacheItemExpirationCount` 和 `IntegratedCacheQueryExpirationCount` 的组合几乎与 `IntegratedCacheEvictedEntriesSize` 一样大，可尝试使用较小的专用网关大小并比较性能。

### <a name="i-want-to-understand-if-i-need-to-add-more-dedicated-gateway-nodes"></a>我想要了解是否需要添加更多专用网关节点

在某些情况下，如果延迟异常高，可能需要更多专用网关节点，而不是更大的节点。 检查 `DedicatedGatewayMaxCpuUsage` 和 `DedicatedGatewayAverageMemoryUsage`，以确定添加更多专用网关节点是否可以减少延迟。 请记住，由于集成缓存的所有实例彼此独立，因此添加更多专用网关节点不会减少 `IntegratedCacheEvictedEntriesSize`。 不过，添加更多节点可以提高专用网关群集可以处理的请求量。

## <a name="next-steps"></a>后续步骤

- [集成缓存的常见问题解答](integrated-cache-faq.md)
- [配置集成缓存](how-to-configure-integrated-cache.md)
- [专用网关](dedicated-gateway.md)