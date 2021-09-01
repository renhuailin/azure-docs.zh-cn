---
title: 监视 - Azure Database for MySQL
description: 本文介绍了用于对 Azure Database for MySQL 进行监视并发出警报的指标，包括 CPU、存储和连接统计信息。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 07da25b029c7268f002e8e80e5875658d7cc05ab
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652773"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>在 Azure Database for MySQL 中进行监视

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 Azure Database for MySQL 提供了各种指标来帮助用户深入了解服务器的行为。

## <a name="metrics"></a>指标

所有 Azure 指标的频率都是一分钟，每个指标提供 30 天的历史记录。 可针对指标配置警报。 有关分步指南，请参阅[如何设置警报](howto-alert-on-metric.md)。 其他任务包括设置自动操作、执行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../azure-monitor/data-platform.md)。

### <a name="list-of-metrics"></a>指标列表

这些指标适用于 Azure Database for MySQL：

|指标|指标显示名称|计价单位|说明|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用的 CPU 百分比。|
|memory_percent|内存百分比|百分比|使用的内存百分比。|
|io_consumption_percent|IO 百分比|百分比|使用的 IO 百分比。 （不适用于基本层服务器）|
|storage_percent|存储百分比|百分比|所用存储占服务器最大存储的百分比。|
|storage_used|已用的存储量|字节|使用的存储量。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|所用服务器日志存储占服务器最大服务器日志存储的百分比。|
|serverlog_storage_usage|服务器日志已用的存储量|字节|使用的服务器日志存储量。|
|serverlog_storage_limit|服务器存储空间上限|字节|此服务器的最大服务器日志存储。|
|storage_limit|存储限制|字节|此服务器的最大存储。|
|active_connections|活动连接数|计数|服务器的活动连接数。|
|connections_failed|失败的连接数|计数|服务器的失败连接数。|
|seconds_behind_master|复制延迟（秒）|计数|副本服务器滞后于源服务器的秒数。 （不适用于基本层服务器）|
|network_bytes_egress|网络传出|字节|跨活动连接的网络传出。|
|network_bytes_ingress|网络传入|字节|跨活动连接的网络传入。|
|backup_storage_used|使用的备份存储|字节|已使用的备份存储量。 此指标表示根据为服务器设置的备份保留期保留的所有完整数据库备份、差异备份和日志备份所消耗的存储的总和。 备份的频率由服务管理，并在[概念文章](concepts-backup.md)中进行了说明。 对于异地冗余存储，备份存储使用率是本地冗余存储的两倍。|

## <a name="server-logs"></a>服务器日志

可以在服务器上启用慢查询和审核日志。 这些日志也可通过 Azure Monitor 日志、事件中心和存储帐户中的 Azure 诊断日志获得。 若要详细了解日志记录，请访问[审核日志](concepts-audit-logs.md)和[慢查询日志](concepts-server-logs.md)这两篇文章。

## <a name="query-store"></a>查询存储

[查询存储](concepts-query-store.md)是一项跟踪一段时间内查询性能（包括查询运行时统计信息和等待事件）的功能。 此功能将查询运行时性能信息保留在 **mysql** 架构中。 你可以通过各种配置旋钮控制数据的收集和存储。

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) 与查询存储协同工作以提供可以从 Azure 门户访问的可视化效果。 这些图表使你能够识别对性能造成影响的关键查询。 可以在 Azure Database for MySQL 服务器门户页的“智能性能”部分中访问 Query Performance Insight。

## <a name="performance-recommendations"></a>性能建议

[性能建议](concepts-performance-recommendations.md)功能可以确定提高工作负荷性能的机会。 性能建议为你提供了有关创建新索引的建议，这些索引可能会提高工作负荷的性能。 若要生成索引建议，该功能会考虑各种数据库特征，包括其架构以及如“查询存储”报告的工作负载。 实施任何性能建议后，客户应测试性能以评估这些更改的影响。

## <a name="planned-maintenance-notification"></a>计划内维护通知

“计划内维护通知”允许你接收有关 Azure Database for MySQL 即将进行的计划内维护的警报。 这些通知与[服务运行状况](../service-health/overview.md)计划内维护集成，允许你在同一位置查看你的订阅的所有计划内维护。 它还有助于将通知扩展到不同资源组的适当受众，因为你可能有不同的联系人负责不同的资源。 你将在事件发生前的 72 小时收到有关即将进行的维护的通知。

在[计划内维护通知](./concepts-planned-maintenance-notification.md)文档中详细了解如何设置通知。

## <a name="next-steps"></a>后续步骤

- 有关如何基于指标创建警报的指南，请参阅[如何设置警报](howto-alert-on-metric.md)。
- 若要深入了解如何使用 Azure 门户、REST API 或 CLI 访问和导出指标，请参阅 [Azure 指标概述](../azure-monitor/data-platform.md)。
- 阅读我们的博客，了解[监控服务器的最佳做法](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/)。
- 详细了解 Azure Database for MySQL（单一服务器）中的[计划内维护通知](./concepts-planned-maintenance-notification.md)