---
title: 监视和优化 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 本文介绍了 Azure Database for PostgreSQL - 超大规模 (Citus) 中的监视和优化功能
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/26/2021
ms.openlocfilehash: cb88998009ab05eb91b8945a138ef935660dac35
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710765"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>监视和优化 Azure Database for PostgreSQL - 超大规模 (Citus)

监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 超大规模 (Citus) 提供各种监视选项，帮助用户深入了解服务器组中的节点的行为。

## <a name="metrics"></a>指标

超大规模 (Citus) 为服务器组中的节点提供指标，并整体聚合组的指标。 这些指标有助于深入了解支持资源的行为。 每项指标以一分钟为频率发出，历史记录长达 30 天。

除了查看指标图形，还可以配置警报。 有关分步指南，请参阅[如何设置警报](howto-hyperscale-alert-on-metric.md)。  其他任务包括设置自动操作、运行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../azure-monitor/data-platform.md)。

### <a name="per-node-vs-aggregate"></a>每节点与聚合

默认情况下，Azure 门户在服务器组中的节点上聚合超大规模 (Citus) 指标。 但是，某些指标（如磁盘使用百分比）在每个节点的基础上提供更多信息。 要查看单独显示的节点的指标，按服务器名称使用 Azure Monitor [指标拆分](../azure-monitor/essentials/metrics-charts.md#metric-splitting)。

> [!NOTE]
>
> 某些超大规模 (Citus) 服务器组不支持指标拆分。 在这些服务器组上，可以单击服务器组“概述”页中的节点名称来查看各个节点的指标。 然后打开节点的“指标”页。

### <a name="list-of-metrics"></a>指标列表

这些指标适用于超大规模 (Citus) 节点：

|指标|指标显示名称|计价单位|说明|
|---|---|---|---|
|active_connections|活动连接数|计数|服务器的活动连接数。|
|cpu_percent|CPU 百分比|百分比|使用的 CPU 百分比。|
|iops|IOPS|Count|请参阅 [IOPS 定义](../virtual-machines/premium-storage-performance.md#iops)和[超大规模 (Citus) 吞吐量](concepts-hyperscale-configuration-options.md)|
|memory_percent|内存百分比|百分比|使用的内存百分比。|
|network_bytes_ingress|网络传入|字节|跨活动连接的网络传入。|
|network_bytes_egress|网络传出|字节|跨活动连接的网络传出。|
|storage_percent|存储百分比|百分比|所用存储占服务器最大存储的百分比。|
|storage_used|已用的存储量|字节|使用的存储量。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|

Azure 总体上不提供群集的聚合指标，但可以将多个节点的指标放在同一图形上。

## <a name="next-steps"></a>后续步骤

- 有关如何基于指标创建警报的指南，请参阅[如何设置警报](howto-hyperscale-alert-on-metric.md)。
- 了解如何执行[指标拆分](../azure-monitor/essentials/metrics-charts.md#metric-splitting)检查服务器组中每个节点的指标。
