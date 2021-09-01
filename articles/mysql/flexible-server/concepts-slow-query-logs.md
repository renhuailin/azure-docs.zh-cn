---
title: 慢查询日志 - Azure Database for MySQL - 灵活服务器
description: 描述 Azure Database for MySQL 灵活服务器中提供的慢查询日志。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 7550fafa0a1fdf67baca4f9c777a4a064f522728
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651677"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Azure Database for MySQL 灵活服务器中的慢查询日志（预览版）

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

在 Azure Database for MySQL 灵活服务器中，用户可配置和访问慢查询日志。 慢查询日志默认禁用，你可启用它来在故障排除期间帮助识别性能性能瓶颈。

有关 MySQL 慢查询日志的详细信息，请参阅 MySQL 引擎文档中的[“慢查询日志”部分](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="configure-slow-query-logging"></a>配置慢查询日志记录 
慢查询日志默认禁用。 若要启用这些日志，请将 `slow_query_log` 服务器参数设置为 ON。 可使用 Azure 门户或 Azure CLI 配置此功能 <!-- add link to server parameter-->. 

可调整来控制慢查询日志记录行为的其他参数包括：

- **long_query_time**：如果查询超过 `long_query_time`（秒）才完成，则记录该查询。 默认为 10 秒。
- **log_slow_admin_statements**：确定是否记录管理语句（例如 `ALTER_TABLE`、`ANALYZE_TABLE`）。
- **log_queries_not_using_indexes**：确定是否记录没有使用索引的查询。
- **log_throttle_queries_not_using_indexes**：限制可写入慢查询日志的非索引查询的数目。 当 `log_queries_not_using_indexes` 设置为 ON 时，此参数生效

> [!IMPORTANT]
>如果表未编制索引，则将 `log_queries_not_using_indexes` 和 `log_throttle_queries_not_using_indexes` 参数设置为 ON 可能会影响 MySQL 性能，因为对这些非索引表运行的所有查询都将写入到慢查询日志。

有关慢查询日志参数的完整说明，请参阅 MySQL [慢查询日志文档](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="access-slow-query-logs"></a>访问慢查询日志

慢查询日志与 Azure Monitor 诊断设置相集成。 在 MySQL 灵活服务器上启用慢查询日志后，可将它们发送到 Azure Monitor 日志、事件中心或 Azure 存储。 若要详细了解诊断设置，请参阅[诊断日志文档](../../azure-monitor/essentials/platform-logs-overview.md)。 若要详细了解如何在 Azure 门户启用诊断设置，请参阅[慢查询日志门户文章](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics)。

下表描述了慢查询日志的输出。 根据输出方法，包含的字段以及这些字段出现的顺序可能会有所不同。

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | 记录日志时的时间戳 (UTC) |
| `Type` | 日志的类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 服务器的名称 |
| `start_time_t` [UTC] | 查询开始时间 |
| `query_time_s` | 执行查询所需的总时间（秒） |
| `lock_time_s` | 锁定查询的总时间（秒） |
| `user_host_s` | 用户名 |
| `rows_sent_s` | 发送的行数 |
| `rows_examined_s` | 检查的行数 |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | 插入 ID |
| `sql_text_s` | 完整查询 |
| `server_id_s` | 服务器 ID |
| `thread_id_s` | 线程 ID |
| `\_ResourceId` | 资源 URI |

> [!Note]
> 对于 `sql_text_s`，如果日志超过 2048 个字符，则会截断日志。

## <a name="analyze-logs-in-azure-monitor-logs"></a>分析 Azure Monitor 日志中的日志

通过诊断日志将慢速查询日志传输到 Azure Monitor 日志后，可以进一步分析慢速查询。 下面是一些可帮助你入门的示例查询。 请确保使用你的服务器名称更新下面的内容。

- 特定服务器上超过 10 秒的查询

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 列出特定服务器上最长的 5 个查询

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 按最小、最大、平均和标准偏差查询时间汇总特定服务器上的慢速查询

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 绘制特定服务器上的慢速查询的分布图

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 显示启用了诊断日志的所有 MySQL 服务器上超过 10 秒的查询

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>后续步骤
- 详细了解[审核日志](concepts-audit-logs.md)
- 通过 [Azure 门户](how-to-configure-slow-query-logs-portal.md)配置慢查询日志
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->