---
title: '审核日志记录-Azure Database for PostgreSQL-超大规模 (Citus) '
description: Azure Database for PostgreSQL-超大规模 (Citus) 中的 pgAudit 审核日志的概念。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: d8a21a5583ec4655a2ee8593e50be5c7b5f702b7
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227578"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>审核日志记录 Azure Database for PostgreSQL-超大规模 (Citus) 

通过 PostgreSQL Audit extension： [pgAudit](https://www.pgaudit.org/)提供 Azure Database for PostgreSQL-超大规模 (Citus) 中的数据库活动的审核日志记录。 pgAudit 提供详细的会话和/或对象审核日志记录。

> [!IMPORTANT]
> pgAudit 在 Azure Database for PostgreSQL-超大规模 (Citus 上处于预览阶段) 

如果需要对计算和存储缩放之类的操作进行 Azure 资源级别的日志记录，请参阅 [Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="usage-considerations"></a>使用注意事项
默认情况下，使用 Postgres 的标准日志记录设备将 pgAudit 日志语句与常规日志语句一起发出。 在 Azure Database for PostgreSQL-超大规模 (Citus) 中，你可以配置要发送到 Azure Monitor 日志存储的所有日志，以便在 Log Analytics 中进行后续分析。 如果启用 Azure Monitor 资源日志记录，系统会将日志（以 JSON 格式）自动发送到 Azure 存储、事件中心和/或 Azure Monitor 日志，具体取决于你的选择。

## <a name="enabling-pgaudit"></a>启用 pgAudit

PgAudit 扩展在所有超大规模 (Citus) 服务器组节点上预安装和启用。 启用它不需要执行任何操作。

## <a name="pgaudit-settings"></a>pgAudit 设置

可以通过 pgAudit 配置会话或对象审核日志记录。 [会话审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)会发出已执行语句的详细日志。 [对象审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)是局限于特定关系的审核。 可以选择设置一个或两个类型的日志记录。 

> [!NOTE]
> pgAudit 设置在全局范围指定，不能在数据库或角色级别指定。
>
> 此外，还会在服务器组中为每个节点指定 pgAudit 设置。 若要在所有节点上进行更改，必须分别将它应用到每个节点。

必须将 pgAudit 参数配置为开始记录。 [pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)提供每个参数的定义。 请先测试参数，确认获取的是预期的行为。

> [!NOTE]
> 将 `pgaudit.log_client` 设置为 ON 会将日志重定向到客户端进程（例如 psql）而不是写入文件。 通常应让此设置保持禁用状态。 <br> <br>
> `pgaudit.log_level` 只有在 `pgaudit.log_client` 为 on 的情况下启用。

> [!NOTE]
> 在 Azure Database for PostgreSQL-超大规模 (Citus) 中， `pgaudit.log` 无法使用 `-` (减) 签名快捷方式进行设置，如 pgAudit 文档中所述。 所有必需的语句类（READ、WRITE 等）均应单独指定。

## <a name="audit-log-format"></a>审核日志格式
每个审核条目通过靠近日志行开头的 `AUDIT:` 进行指示。 条目其余部分的格式详见 [pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)。

## <a name="getting-started"></a>入门
若要快速开始，请将 `pgaudit.log` 设置为 `WRITE`，并打开服务器日志来查看输出。 

## <a name="viewing-audit-logs"></a>查看审核日志
访问日志的方式取决于所选的终结点。 有关 Azure 存储，请参阅[日志存储帐户](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)一文。 有关事件中心，请参阅[流式传输 Azure 日志](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)一文。

Azure Monitor 日志将发送到所选的工作区。 Postgres 日志使用 **AzureDiagnostics** 收集模式，因此可以从 AzureDiagnostics 表查询它们。 下面描述了该表中的字段。 在 [Azure Monitor 日志查询](../azure-monitor/log-query/log-query-overview.md)概述中详细了解查询和警报。

可以通过此查询开始使用。 可以基于查询配置警报。

搜索特定服务器在过去一天生成的 Postgres 日志中的所有 pgAudit 条目
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>后续步骤

- [了解如何在 Azure Database for PostgreSQL-超大规模 (Citus) 以及如何访问日志中设置日志记录](howto-hyperscale-logging.md)
