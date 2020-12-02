---
title: 日志-超大规模 (Citus) -Azure Database for PostgreSQL
description: '如何访问 Azure Database for PostgreSQL-超大规模 (Citus 的数据库日志) '
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: f8840d5115cb552ed203705d37f8c692b3418947
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492329"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-超大规模 (Citus 中的日志) 

PostgreSQL 日志在超大规模 (Citus) 服务器组的每个节点上可用。 可以将日志发送到存储服务器，也可以将日志发送到分析服务。 这些日志可用于识别、排除和修复配置错误和性能不佳问题。

## <a name="accessing-logs"></a>访问日志

若要访问超大规模 (Citus) 协调器或辅助节点的 PostgreSQL 日志，请打开 Azure 门户中的节点：

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="节点列表":::

对于所选节点，打开 " **诊断设置**"，然后单击 " **+ 添加诊断设置**"。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="添加诊断设置按钮":::

为新诊断设置选择一个名称，并选中 " **PostgreSQLLogs** " 框。  选择) 应接收日志的目标 (。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="选择 PostgreSQL 日志":::

## <a name="next-steps"></a>后续步骤

- [Log analytics 查询入门](../azure-monitor/log-query/log-analytics-tutorial.md)
- 了解 [Azure 事件中心](../event-hubs/event-hubs-about.md)