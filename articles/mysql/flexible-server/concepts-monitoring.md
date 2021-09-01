---
title: 监视 - Azure Database for MySQL - 灵活服务器
description: 本文介绍了用于对 Azure Database for MySQL 灵活服务器进行监视并发出警报的指标，包括 CPU、存储和连接统计信息。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 778c5e99c91cede0e67ee4250abeeb4b73e49965
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652272"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>利用内置的指标监视 Azure Database for MySQL 灵活服务器

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

Azure Database for MySQL 灵活服务器通过 Azure Monitor 提供服务器监视。 指标是数字值，用于描述某方面的服务器资源在特定时间的状况。 通过允许你监视对你最重要的内容，对服务器的资源进行监视可帮助你排查工作负荷问题并优化工作负荷。 监视适当的指标有助于保持服务器和应用程序的性能、可靠性与可用性。

在本文中，你将了解可用于灵活服务器的各种指标，以便深入了解服务器的行为。

## <a name="available-metrics"></a>可用度量值

Azure Database for MySQL 灵活服务器提供用于了解工作负荷的执行状况的各种指标，你可以基于此数据了解对服务器和应用程序的影响。 例如，在灵活服务器中，可以监视 **主机 CPU 百分比**、**活动连接**、**IO 百分比** 和 **主机内存百分比**，以确定何时存在性能影响。 基于此信息，你可能必须优化工作负荷，通过更改计算层来进行纵向缩放，或使用只读副本进行横向缩放。

所有 Azure 指标的频率都是一分钟，每个指标提供 30 天的历史记录。 可针对指标配置警报。 有关分步指南，请参阅[如何设置警报](./how-to-alert-on-metric.md)。 其他任务包括设置自动操作、执行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../../azure-monitor/data-platform.md)。

### <a name="list-of-metrics"></a>指标列表
这些指标适用于 Azure Database for MySQL：

|指标显示名称|指标|计价单位|说明|
|---|---|---|---|
|主机 CPU 百分比|cpu_percent|百分比|服务器上 CPU 使用率的百分比，包括来自客户工作负荷和 Azure MySQL 进程的 CPU 使用率|
|主机网络流入量 |network_bytes_ingress|字节|服务器上的传入网络流量，包括来自客户数据库和 Azure MySQL 功能（如复制、监视、日志等）的流量。|
|主机网络流出量|network_bytes_egress|字节|服务器上的传出网络流量，包括来自客户数据库和 Azure MySQL 功能（如复制、监视、日志等）的流量。|
|复制延迟时间|replication_lag|秒|自上次重放事务以来所经历的时间。 此指标仅适用于副本服务器。|
|活动连接数|active_connection|计数|服务器的活动连接数。|
|使用的备份存储|backup_storage_used|字节|已使用的备份存储量。|
|IO 百分比|io_consumption_percent|百分比|使用的 IO 百分比。|
|主机内存百分比|memory_percent|百分比|服务器上正在使用的内存的百分比，包括来自客户工作负荷和 Azure MySQL 进程的内存使用率|
|存储限制|storage_limit|字节|此服务器的最大存储。|
|存储百分比|storage_percent|百分比|所用存储占服务器最大存储的百分比。|
|已用的存储量|storage_used|字节|使用的存储量。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|
|总连接数|total_connections|计数|与服务器的连接总数|
|已中止的连接数|aborted_connections|计数|连接到 MySQL 的失败尝试（例如，由于凭据错误而导致的失败连接）次数。|
|查询|查询|计数|每秒查询次数|

## <a name="next-steps"></a>后续步骤
- 有关如何基于指标创建警报的指南，请参阅[如何设置警报](./how-to-alert-on-metric.md)。
- 详细了解[缩放 IOPS](./concepts/../concepts-compute-storage.md#iops) 以提高性能。