---
title: 审核日志 - Azure Database for MySQL
description: 介绍了 Azure Database for MySQL 中提供的审核日志，以及用于启用日志记录级别的可用参数。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: 54e619491a52828475143fbe5656736063e84c9d
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653434"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的审核日志

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

在 Azure Database for MySQL 中，审核日志可供用户使用。 审核日志可以用来跟踪数据库级别的活动，通常用于确保符合性。

## <a name="configure-audit-logging"></a>配置审核日志记录

>[!IMPORTANT]
> 建议仅记录审核所需的事件类型和用户，以确保服务器的性能不会受到严重影响。

默认情况下，审核日志被禁用。 若要启用它，请将 `audit_log_enabled` 设置为 ON。

可以调整的其他参数包括：

- `audit_log_events`：控制要记录的事件。 请查看下表以了解具体的审核事件。
- `audit_log_include_users`：要包括 MySQL 用户进行日志记录。 此参数的默认值为空，这将包括所有用户进行日志记录。 此参数的优先级高于 `audit_log_exclude_users`。 此参数的最大长度为 512 个字符。
- `audit_log_exclude_users`：不对 MySQL 用户进行日志记录。 此参数的最大长度为 512 个字符。

> [!NOTE]
> `audit_log_include_users` 的优先级高于 `audit_log_exclude_users`。 例如，如果 `audit_log_include_users` = `demouser` 并且 `audit_log_exclude_users` = `demouser`，则会将该用户包括在审核日志中，因为 `audit_log_include_users` 的优先级更高。

| **事件** | **说明** |
|---|---|
| `CONNECTION` | - 启动连接（成功或不成功） <br> - 在会话期间使用不同的用户/密码对用户重新进行身份验证 <br> - 终止连接 |
| `DML_SELECT`| SELECT 查询 |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE 查询 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 类似“DROP DATABASE”的查询 |
| `DCL` | 类似“GRANT PERMISSION”的查询 |
| `ADMIN` | 类似“SHOW STATUS”的查询 |
| `GENERAL` | All in DML_SELECT, DML_NONSELECT, DML, DDL, DCL, and ADMIN |
| `TABLE_ACCESS` | - 适用于 MySQL 5.7 和 MySQL 8.0 <br> - 表读取语句，例如 SELECT 或 INSERT INTO ...SELECT <br> - 表删除语句，例如 DELETE 或 TRUNCATE TABLE <br> - 表插入语句，例如 INSERT 或 REPLACE <br> - 表更新语句，例如 UPDATE |

## <a name="access-audit-logs"></a>访问审核日志

审核日志与 Azure Monitor 诊断日志集成。 在 MySQL 服务器上启用审核日志后，可以将它们发送到 Azure Monitor 日志、事件中心或 Azure 存储。 若要详细了解如何在 Azure 门户中启用诊断日志，请参阅[审核日志门户文章](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)。

## <a name="diagnostic-logs-schemas"></a>诊断日志架构

以下部分介绍基于事件类型的 MySQL 审核日志的输出是什么。 根据输出方法，包含的字段以及这些字段出现的顺序可能会有所不同。

### <a name="connection"></a>连接

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志的类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 服务器的名称 |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`、`DISCONNECT`、`CHANGE USER`（仅适用于 MySQL 5.7） |
| `connection_id_d` | MySQL 生成的唯一连接 ID |
| `host_s` | 空白 |
| `ip_s` | 连接到 MySQL 的客户端的 IP 地址 |
| `user_s` | 执行查询的用户的名称 |
| `db_s` | 连接的数据库的名称 |
| `\_ResourceId` | 资源 URI |

### <a name="general"></a>常规

下面的架构适用于 GENERAL、DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN 事件类型。

> [!NOTE]
> 对于 `sql_text`，如果日志超过 2048 个字符，则会截断日志。

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志的类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 服务器的名称 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`、`ERROR`、`RESULT`（仅适用于 MySQL 5.6） |
| `event_time` | 查询开始时间（UTC 时间戳） |
| `error_code_d` | 查询失败时的错误代码。 `0` 意味着无错误 |
| `thread_id_d` | 执行了查询的线程的 ID |
| `host_s` | 空白 |
| `ip_s` | 连接到 MySQL 的客户端的 IP 地址 |
| `user_s` | 执行查询的用户的名称 |
| `sql_text_s` | 完整查询文本 |
| `\_ResourceId` | 资源 URI |

### <a name="table-access"></a>表访问权限

> [!NOTE]
> 只有 MySQL 5.7 输出表访问日志。<br>对于 `sql_text`，如果日志超过 2048 个字符，则会截断日志。

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志的类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 服务器的名称 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`、`INSERT`、`UPDATE` 或 `DELETE` |
| `connection_id_d` | MySQL 生成的唯一连接 ID |
| `db_s` | 访问的数据库的名称 |
| `table_s` | 访问的表的名称 |
| `sql_text_s` | 完整查询文本 |
| `\_ResourceId` | 资源 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>分析 Azure Monitor 日志中的日志

将审核日志通过诊断日志以管道方式传送到 Azure Monitor 日志后，便可以对审核事件进行进一步分析。 下面是一些可帮助你入门的示例查询。 请确保使用你的服务器名称更新下面的内容。

- 列出特定服务器上的 GENERAL 事件

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- 列出特定服务器上的 CONNECTION 事件

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- 汇总特定服务器上的已审核事件

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- 绘制特定服务器上的审核事件类型分布图

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- 列出已为审核日志启用诊断日志的所有 MySQL 服务器上的已审核事件

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>后续步骤

- [如何在 Azure 门户中配置审核日志](howto-configure-audit-logs-portal.md)
