---
title: Azure Database for PostgreSQL（单一服务器）中的审核日志记录
description: Azure Database for PostgreSQL（单一服务器）中的 pgAudit 审核日志记录的概念。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 8a60c1db1ca3b3037aded6ed7d7a88f237edfe4e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458693"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的审核日志记录

Azure Database for PostgreSQL（单一服务器）中数据库活动的审核日志记录可通过 PostgreSQL 审核扩展 [pgAudit](https://www.pgaudit.org/) 获得。 pgAudit 扩展提供详细会话和对象审核日志记录。

> [!NOTE]
> pgAudit 扩展在 Azure Database for PostgreSQL 上为预览版。 它只能在常规用途和内存优化服务器上启用。

如果需要计算和存储缩放之类的操作的 Azure 资源级日志，请参阅 [Azure 平台日志概述](../azure-monitor/essentials/platform-logs-overview.md)。

## <a name="usage-considerations"></a>使用注意事项

默认使用 Postgres 标准日志记录设备将 pgAudit 日志语句与常规日志语句一起发出。 在 Azure Database for PostgreSQL 中，这些 .log 文件可以通过 Azure 门户或  Azure CLI 下载。 文件集的最大存储为 1 GB。 每个文件的可用期最长为 7 天。 默认为 3 天。 此服务是一个短期存储选项。

也可将所有日志配置为发送到 Azure Monitor 日志存储，以便日后在 Log Analytics 中进行分析。 如果启用 Monitor 资源日志记录，日志会自动以 JSON 格式发送到 Azure 存储、Azure 事件中心或 Monitor Logs，具体取决于你的选择。

启用 pgAudit 会在服务器上生成大量日志记录，这会影响性能和日志存储。 建议使用 Monitor Logs，它提供更长期的存储选项以及分析和警报功能。 请关闭标准日志记录，以降低额外的日志记录对性能的影响：

   1. 将参数 `logging_collector` 设置为 OFF。
   1. 重启服务器以应用该更改。

若要了解如何设置将日志记录到存储、事件中心或 Monitor Logs 的功能，请参阅 [Azure Database for PostgreSQL（单一服务器）日志](concepts-server-logs.md)中的资源日志部分。

## <a name="install-pgaudit"></a>安装 pgAudit

若要安装 pgAudit，需将其包括在服务器的共享预加载库中。 更改 Postgres 的 `shared_preload_libraries` 参数需要重启服务器才能生效。 可以使用[门户](howto-configure-server-parameters-using-portal.md)、[CLI](howto-configure-server-parameters-using-cli.md) 或 [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate) 更改参数。

若要使用[门户](https://portal.azure.com)更改参数，请执行以下操作：

   1. 选择你的 Azure Database for PostgreSQL 服务器。
   1. 在左侧的“设置”下，选择“服务器参数” 。
   1. 搜索 shared_preload_libraries。
   1. 选择 PGAUDIT。
   
      :::image type="content" source="./media/concepts-audit/share-preload-parameter.png" alt-text="显示为 PGAUDIT 启用 shared_preload_libraries 的 Azure Database for PostgreSQL 的屏幕截图。":::

   1. 重启服务器以应用更改。
   1. 在 psql 中执行以下查询，检查 `pgaudit` 是否已加载到 `shared_preload_libraries` 中：
   
        ```SQL
      show shared_preload_libraries;
      ```
      应在返回 `shared_preload_libraries` 的查询结果中看到 `pgaudit`。

   1. 使用客户端（例如 psql）连接到服务器，并启用 pgAudit 扩展：
   
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 如果看到错误，请确认是否在保存 `shared_preload_libraries` 后重启了服务器。

## <a name="pgaudit-settings"></a>pgAudit 设置

可以通过 pgAudit 配置会话或对象审核日志记录。 [会话审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)会发出已执行语句的详细日志。 [对象审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)是局限于特定关系的审核。 可以选择设置一个或两个类型的日志记录。

> [!NOTE]
> pgAudit 设置在全局范围指定，不能在数据库或角色级别指定。

[安装 pgAudit](#install-pgaudit) 以后，即可配置其参数以开始日志记录。

若要在[门户](https://portal.azure.com)中配置 pgAudit，请执行以下操作：

   1. 选择你的 Azure Database for PostgreSQL 服务器。
   1. 在左侧的“设置”下，选择“服务器参数” 。
   1. 搜索 pg_audit 参数。
   1. 选择适当的设置参数进行编辑。 例如，若要启动日志记录，请将 pgaudit.log 设置为 WRITE 。
   
       :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="显示使用 pgAudit 配置日志记录的 Azure Database for PostgreSQL 的屏幕截图。":::
   1. 选择“保存”  以保存更改。

[pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)提供每个参数的定义。 请先测试参数，确认正常运行。 例如：

- 启用 pgaudit.log_client 设置后，它会将日志重定向到客户端进程（如 psql）而不是写入文件。 通常应将此设置保持禁用状态。
- 参数 pgaudit.log_level 仅在 pgaudit.log_client 为启用的情况下才启用 。

> [!NOTE]
> 在 Azure Database for PostgreSQL 中，不能使用 pgAudit 文档中所述的减号快捷方式 (`-`) 来设置 pgaudit.log。 应单独指定所有必需的语句类，例如 READ 和 WRITE。

### <a name="audit-log-format"></a>审核日志格式

每个审核条目通过靠近日志行开头的 `AUDIT:` 进行指示。 条目其余部分的格式详见 [pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)。

如果需要任何其他的字段来满足审核要求，请使用 Postgres 参数 `log_line_prefix`。 每个 Postgres 日志行开头的输出都是 `log_line_prefix` 字符串。 例如，以下 `log_line_prefix` 设置提供时间戳、用户名、数据库名称和进程 ID：

```
t=%m u=%u db=%d pid=[%p]:
```

若要了解有关 `log_line_prefix` 的详细信息，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)。

### <a name="get-started"></a>入门

若要快速开始，请将 pgaudit.log 设置为 WRITE 。 然后打开日志来查看输出。

## <a name="view-audit-logs"></a>查看审核日志

如果使用的是 .log 文件，则审核日志将与 PostgreSQL 错误日志包含在同一文件中。 可以从[门户](howto-configure-server-logs-in-portal.md)或 [CLI](howto-configure-server-logs-using-cli.md) 下载日志文件。

如果使用的是 Azure 资源日志记录，则日志的访问方式取决于所选的终结点。 对于存储，请参阅 [Azure 资源日志](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)。 对于事件中心，也请参阅 [Azure 资源日志](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)。

对于 Monitor Logs，日志将发送到所选的工作区。 Postgres 日志使用 `AzureDiagnostics` 收集模式，因此可以利用 `AzureDiagnostics` 表进行查询，如下所示。 若要详细了解查询和警报，请参阅[在 Azure Monitor 中记录查询](../azure-monitor/logs/log-query-overview.md)。

通过此查询开始使用。 可以基于查询配置警报。

搜索特定的服务器在过去一天生成的所有 Postgres 日志：

```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>后续步骤

- [了解 Azure Database for PostgreSQL 中的日志记录](concepts-server-logs.md)。
- 了解如何使用 [Azure 门户](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md) 或 [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate) 来设置参数。
