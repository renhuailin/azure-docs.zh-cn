---
title: 监视用于 Redis 的 Azure 缓存
description: 了解如何监视用于 Redis 的 Azure 缓存实例的运行状况和性能
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 80290a9214e7d4b2715251f49b407d7456bd1fb6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114293966"
---
# <a name="monitor-azure-cache-for-redis"></a>监视用于 Redis 的 Azure 缓存

用于 Redis 的 Azure 缓存使用 [Azure Monitor](../azure-monitor/index.yml) 提供用于监视缓存实例的几个选项。 借助这些工具，可以监视 Azure Redis 缓存实例的运行状况，以及管理缓存应用程序。

使用 Azure Monitor 可以：

- 查看指标
- 将指标图表固定到启动板
- 自定义监视图表的日期和时间范围
- 在图表中添加和删除指标
- 设置在符合特定条件时发出的警报

Azure Cache for Redis 实例的指标是使用 Redis [INFO](https://redis.io/commands/info) 命令收集的。 指标每分钟大约收集两次，并自动存储 30 天，因此，它们可以显示在指标图表中，并根据警报规则进行评估。

若要配置不同的保留期策略，请参阅[导出缓存指标](#export-cache-metrics)。  

有关用于每个缓存度量值的不同 INFO 值的详细信息，请参阅 [可用度量值和报告间隔](#available-metrics-and-reporting-intervals)。

<a name="view-cache-metrics"></a>

若要查看缓存指标，请[浏览](cache-configure.md#configure-azure-cache-for-redis-settings)到 [Azure 门户](https://portal.azure.com)中的缓存实例。  Azure Cache for Redis 使用“概述”和“Redis 指标”在左侧提供一些内置图表 。 通过添加或删除指标以及更改报告间隔可以自定义每个图表。

![显示了六个图形。 其中一个是过去一小时的缓存命中数和缓存未命中数。](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>查看预配置的指标图表

左侧的“概述”提供了以下预配置的监视图表。

- [监视图表](#monitoring-charts)
- [图表使用情况](#usage-charts)

### <a name="monitoring-charts"></a>监视图表

左侧“概述”中的“监视”部分包含“命中数和未命中数”、“获取次数和设置次数”、“连接数”以及“命令总数”图表     。

![监视图表](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>图表使用情况

左侧“概述”中的“使用情况”部分包含“Redis 服务器负载”、“内存使用率”、“网络带宽”和“CPU 使用率”图表，并显示缓存实例的“定价层”      。

![图表使用情况](./media/cache-how-to-monitor/redis-cache-usage-part.png)

“定价层”显示缓存定价层，并可用于将缓存[缩放](cache-how-to-scale.md)到不同的定价层。 

## <a name="view-metrics-charts-for-all-your-caches-with-azure-monitor-for-azure-cache-for-redis"></a>使用适用于 Azure Cache for Redis 的 Azure Monitor 查看所有缓存的指标图表

使用[适用于 Azure Cache for Redis 的 Azure Monitor](../azure-monitor/insights/redis-cache-insights-overview.md)（预览版）可以查看所有 Azure Cache for Redis 资源的总体性能、故障、容量和运行状况。 在可自定义的统一交互式体验中查看指标，并可以向下钻取到各个资源的详细信息。 适用于 Azure Cache for Redis 的 Azure Monitor 基于 [Azure Monitor 的工作簿功能](../azure-monitor/visualize/workbooks-overview.md)，为指标和其他数据提供丰富的可视化效果。 若要了解详细信息，请参阅文章[探究适用于 Azure Cache for Redis 的 Azure Monitor](../azure-monitor/insights/redis-cache-insights-overview.md)。

## <a name="view-metrics-with-azure-monitor-metrics-explorer"></a>通过 Azure Monitor 指标资源管理器查看指标

如果不需要适用于 Azure Cache for Redis 的 Azure Monitor 的这种程度的全面灵活性，可以使用 Azure Monitor 指标资源管理器查看指标和创建自定义图表。 在“资源”菜单中选择“指标”，然后使用偏好的指标、报告间隔、图表类型等元素来自定义图表 。

![在 contoso55 的左侧导航窗格中，“指标”是“监视”下的一个选项，并突出显示。 “指标”上有一个指标列表。 已选择缓存命中数和缓存未命中数。](./media/cache-how-to-monitor/redis-cache-monitor.png)

有关使用 Azure Monitor 处理指标的详细信息，请参阅 [Microsoft Azure 中的指标概述](../azure-monitor/data-platform.md)。

<a name="enable-cache-diagnostics"></a>

## <a name="export-cache-metrics"></a>导出缓存指标

默认情况下，Azure Monitor 中的缓存指标将[存储 30 天](../azure-monitor/essentials/data-platform-metrics.md)，过后被删除。 若要延长缓存指标的保存时间，使其超过 30 天，可以为缓存指标[指定存储帐户](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)，并指定“保留期（天）”  策略。

若要配置缓存指标的存储帐户，请执行以下操作：

1. 在“Azure Cache for Redis”  页面中，在“监视”  标题下，选择“诊断”  。
1. 选择“+ 添加诊断设置”。 
1. 为设置命名。
1. 选中“存档到存储帐户”。  向存储帐户发送诊断时，会针对存储和事务向你收取正常数据费率。
1. 选择“配置”  以选择要在其中存储缓存指标的存储帐户。
1. 在表标题“指标”  下，选中你要存储的行项（例如“AllMetrics”）旁边的复选框。  指定一个“保留期(天)”  策略。 可以指定的最大保留天数为 **365 天**。 但是，如果你要永久保留指标数据，请将“保留期(天)”设置为 0 。
1. 选择“保存”。

![Redis 诊断](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>除了将缓存指标存档到存储中外，还可以[将其流式传输到事件中心或将其发送到 Azure Monitor 日志](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values)。
>

若要访问指标，可按前文所述在 Azure 门户中查看它们。 还可以使用 [Azure Monitor 指标 REST API](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) 对其进行访问。

> [!NOTE]
> 如果更改存储帐户，以前配置的存储帐户中的数据仍可供下载，但并不会显示在 Azure 门户中。  
>

## <a name="available-metrics-and-reporting-intervals"></a>可用指标和报告间隔

将使用多个报告间隔报告缓存指标，其中包括“前一小时”  、“今天”  、“前一周”  和“自定义”  。 在左侧可以看到，每个指标图表的“指标”选项在图表中显示了每个指标的平均值、最小值和最大值，并且一些指标还显示了报告间隔内的总计。

每个度量值均包含两个版本。 一个指标测量整个缓存的性能以及使用[群集](cache-how-to-premium-clustering.md)的缓存的性能，名称中包含 `(Shard 0-9)` 的另一指标版本则测量缓存中单个分片的性能。 例如，如果缓存有四个分片，`Cache Hits` 就是整个缓存的命中总数，而 `Cache Hits (Shard 3)` 只是该缓存分片的命中数。

> [!NOTE]
> 查看聚合类型时：
>
> - “计数”显示 2，表示对于你的时间粒度（1 分钟），指标收到了 2 个数据点。
> - “最大值”显示时间粒度中某个数据点的最大值。
> - “最小值”显示时间粒度中某个数据点的最小值。
> - “平均值”显示时间粒度中所有数据点的平均值。
> - “总和”显示时间粒度中所有数据点的总和，可能有误导性，具体取决于特定的指标。
> 在正常情况下，“平均值”和“最大值”非常相似，因为只有一个节点（主节点）发出了这些指标。 如果连接的客户端数快速变化，则“最大值”、“平均值”和“最小值”将显示差别很大的值，这也是预期的行为。
>
> 一般情况下，“平均值”将显示所需指标的平滑图表，并且能够很好地根据时间粒度的变化而变化。 如果时间粒度较大，则“最大值”和“最小值”可能无法表明指标中的较大变化，但对较小的时间粒度使用这些聚合可以帮助查明指标中发生较大变化的确切时间。
>
> “计数”和“总和”对于某些指标（包括连接的客户端数）而言可能有误导性。
>
> 因此，我们建议查看“平均值”指标，而不要查看“总和”指标。

> [!NOTE]
> 即使在缓存处于空闲状态，且没有连接的活动客户端应用程序时，也可能会看到一些缓存活动，例如，连接的客户端、内存使用率以及正在执行的操作。 在用于 Redis 的 Azure 缓存实例运行期间，此活动是正常情况。
>
>

| 指标 | 说明 |
| --- | --- |
| 缓存命中数 |在指定的报告间隔期间，成功的键查找次数。 此数目映射到 Redis [INFO](https://redis.io/commands/info) 命令输出中的 `keyspace_hits`。 |
| 缓存延迟（预览） | 使用缓存的节点间延迟计算的缓存延迟。 此指标以微秒为单位，具有三个维度：`Avg`、`Min` 和 `Max`。 这些维度表示在指定的报告间隔期间缓存的平均延迟、最小延迟和最大延迟。 |
| 缓存未命中数 |在指定的报告间隔期间，失败的键查找次数。 此数目映射到 Redis INFO 命令输出中的 `keyspace_misses`。 缓存未命中并不一定意味着缓存出现了问题。 例如，在使用缓存端编程模式时，应用程序会首先查找缓存中的项。 如果缓存中不存在该项（缓存未命中），则将从数据库中检索该项并将其添加到缓存中供下次使用。 对于缓存端编程模式，缓存未命中是正常行为。 如果缓存未命中数大于预期值，请检查从缓存中填充并读取的应用程序逻辑。 如果由于内存压力而从缓存中逐出了项，则可能存在一些缓存未命中情况，但用于监视内存压力的更好指标是 `Used Memory` 或 `Evicted Keys`。 |
| 缓存读取量 |指定报告间隔期间，从缓存中读取的数据量，以每秒兆字节数（MB/秒）为单位。 此值派生自支持托管缓存的虚拟机的网络接口卡，且不特定于 Redis。 **此值对应于该缓存使用的网络带宽。如果要针对服务器端网络带宽限制设置警报，则可使用此 `Cache Read` 计数器来创建警报。请参阅 [此表](./cache-planning-faq.yml#azure-cache-for-redis-performance)，了解各种缓存定价层和大小所遵循的带宽限制。** |
| 缓存写入量 |指定报告间隔期间，写入缓存中的数据量，以每秒兆字节数（MB/秒）为单位。 此值派生自支持托管缓存的虚拟机的网络接口卡，且不特定于 Redis。 此值对应于从客户端发送到缓存的数据的网络带宽。 |
| 连接的客户端数 |指定的报告间隔期间，客间户端与缓存的连接数。 此数目映射到 Redis INFO 命令输出中的 `connected_clients`。 达到[连接限制](cache-configure.md#default-redis-server-configuration)后，尝试连接到缓存将会失败。 即使没有活动的客户端应用程序，也仍可能会由于存在内部进程和连接而存在少量已连接的客户端实例。 |
| CPU |指定报告间隔期间，用于 Redis 的 Azure 缓存服务器的 CPU 使用率（以百分比表示）。 此值映射到操作系统 `\Processor(_Total)\% Processor Time` 性能计数器。 |
| 错误 | 在指定的报告间隔期间，缓存可能会出现的特定故障和性能问题。 该指标具有八个维度，表示不同的错误类型，但在将来可能会添加更多维度。 现在所代表的错误类型如下所示： <br/><ul><li>**Failover** - 缓存故障转移时（从属缓存提升为主缓存）</li><li>**Dataloss** - 缓存上发生数据丢失时</li><li>**UnresponsiveClients** - 客户端从服务器读取数据的速度不够快时</li><li>**AOF** - 存在与 AOF 持久性相关的问题时</li><li>**RDB** - 存在与 RDB 持久性相关的问题时</li><li>**Import** - 存在与导入 RDB 有关的问题时</li><li>**Export** - 存在与导出 RDB 有关的问题时</li></ul> |
| 逐出的密钥数 |由于 `maxmemory` 限制而在指定的报告间隔期间从缓存中逐出的项数。 此数目映射到 Redis INFO 命令输出中的 `evicted_keys`。 |
| 过期的密钥数 |指定的报告间隔期间，缓存中过期的项目数。 此值映射到 Redis INFO 命令输出中的 `expired_keys` 。|
| 获取数 |指定的报告间隔期间，缓存中的获取操作数。 此值是以下 Redis INFO 所有命令中的值的总和：`cmdstat_get`、`cmdstat_hget`、`cmdstat_hgetall`、`cmdstat_hmget`、`cmdstat_mget`、`cmdstat_getbit` 和 `cmdstat_getrange`，并且等效于报告间隔期间缓存命中和未命中数的总和。 |
| 每秒操作数 | 指定的报告间隔期间，由缓存服务器处理的每秒命令总数。  此值映射到 Redis INFO 命令中的“instantaneous_ops_per_sec”。 |
| Redis 服务器负载 |Redis 服务器忙于处理消息并且非空闲等待消息的周期百分比。 如果此计数器达到 100，则意味着 Redis 服务器已达到性能上限并且 CPU 无法更快地工作。 如果你发现 Redis 服务器负载较高，则会在客户端中看到超时异常。 在这种情况下，应该考虑将数据扩大或分区到多个缓存。 |
| 设置数 |指定的报告间隔期间，对缓存的设置操作数。 此值是以下 Redis INFO 所有命令中的值的总和：`cmdstat_set`、`cmdstat_hset`、`cmdstat_hmset`、`cmdstat_hsetnx`、`cmdstat_lset`、`cmdstat_mset`、`cmdstat_msetnx`、`cmdstat_setbit`、`cmdstat_setex`、`cmdstat_setrange` 和 `cmdstat_setnx`。 |
| 总密钥数  | 在上一个报告时段缓存中的最大密钥数。 此数目映射到 Redis INFO 命令输出中的 `keyspace`。 由于已启用群集的缓存的基础指标系统存在限制，因此，“总密钥数”将返回在上一个报告时段内密钥数最多的分片的最大密钥数。  |
| 总操作数 |指定的报告间隔期间，由缓存服务器处理的命令总数。 此值映射到 Redis INFO 命令输出中的 `total_commands_processed` 。 当 Azure Cache for Redis 纯粹用于发布/订阅时，将不存在 `Cache Hits`、`Cache Misses`、`Gets` 或 `Sets` 的度量值，但存在 `Total Operations` 度量值，该度量值反映发布/订阅操作的缓存使用情况。 |
| 已用内存 |在指定的报告间隔期间，缓存中的键/值对所用的缓存内存量（以 MB 为单位）。 此值映射到 Redis INFO 命令输出中的 `used_memory` 。 此值不包括元数据或碎片。 |
| 已用内存百分比 | 指定报告间隔期间使用的总内存的百分比。  此值引用 Redis INFO 命令输出中的 `used_memory` 值来计算百分比。 |
| 已用内存 RSS |指定报告间隔期间所用的缓存内存量（以 MB 为单位），包括碎片和元数据。 此值映射到 Redis INFO 命令输出中的 `used_memory_rss` 。 |

<a name="operations-and-alerts"></a>

## <a name="alerts"></a>警报

可配置为基于指标和活动日志接收警报。 通过 Azure Monitor 可配置警报，使警报触发时执行以下操作：

- 发送电子邮件通知
- 调用 Webhook
- 调用 Azure 逻辑应用

若要配置缓存的警报规则，请在“资源”菜单中选择“警报规则” 。

![监视](./media/cache-how-to-monitor/redis-cache-monitoring.png)

有关配置和使用警报的详细信息，请参阅[警报概述](../azure-monitor/alerts/alerts-classic-portal.md)。

## <a name="activity-logs"></a>活动日志

活动日志提供有关在 Azure Cache for Redis 实例上完成的操作的见解。 活动日志以前称为“审核日志”或“操作日志”。 通过活动日志，可确定对用于 Redis 的 Azure 缓存实例执行的任何写入操作 (PUT、POST、DELETE) 的“操作内容、操作人员和操作时间”。

> [!NOTE]
> 活动日志不包括读取 (GET) 操作。
>

若要查看缓存的活动日志，请在“资源”菜单中选择“活动日志” 。

有关活动日志的详细信息，请参阅 [Azure 活动日志概述](../azure-monitor/essentials/platform-logs-overview.md)。