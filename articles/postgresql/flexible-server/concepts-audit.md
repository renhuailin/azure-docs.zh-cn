---
title: 审核日志记录 - Azure Database for PostgreSQL 灵活服务器
description: Azure Database for PostgreSQL 灵活服务器中的 pgAudit 审核日志记录的概念。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d4659e44475c09a1a42c06041e3f180357af9ee2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128556012"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中的审核日志记录

Azure Database for PostgreSQL 灵活服务器中数据库活动的审核日志记录可以通过 PostgreSQL 审核扩展 [pgAudit](https://www.pgaudit.org/) 来获取。 pgAudit 提供详细的会话和/或对象审核日志记录。

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

如果需要对计算和存储缩放之类的操作进行 Azure 资源级别的日志记录，请参阅 [Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)。

## <a name="usage-considerations"></a>使用注意事项
默认情况下，使用 Postgres 的标准日志记录设备将 pgAudit 日志语句与常规日志语句一起发出。 在 Azure Database for PostgreSQL 灵活服务器中，可将所有日志配置为发送到 Azure Monitor 日志存储，以供将来在 Log Analytics 中进行分析。 如果启用 Azure Monitor 资源日志记录，系统会将日志（以 JSON 格式）自动发送到 Azure 存储、事件中心和/或 Azure Monitor 日志，具体取决于你的选择。

若要了解如何设置将日志记录到 Azure 存储、事件中心或 Azure Monitor 日志的功能，请访问[服务器日志文章](concepts-logging.md)的资源日志部分。

## <a name="installing-pgaudit"></a>安装 pgAudit

若要安装 pgAudit，需将其包括在服务器的共享预加载库中。 更改 Postgres 的 `shared_preload_libraries` 参数需要重启服务器才能生效。 可以使用 [Azure 门户](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md) 或 [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate) 更改参数。

使用 [Azure 门户](https://portal.azure.com)：

   1. 选择你的 Azure Database for PostgreSQL - 灵活服务器。
   2. 在侧栏中选择“服务器参数”。 
   3. 搜索 `shared_preload_libraries` 参数。
   4. 选择 **pgaudit**。
     :::image type="content" source="./media/concepts-audit/shared-preload-libraries.png" alt-text="显示 Azure Database for PostgreSQL - 为 pgaudit 启用 shared_preload_libraries 的屏幕截图":::
   5. 可以通过在 psql 中执行以下查询来检查 pgaudit 是否已加载到 shared_preload_libraries 中：
        ```SQL
      show shared_preload_libraries;
      ```
      你应该在返回 shared_preload_libraries 的查询结果中看到 pgaudit

   6. 使用客户端（例如 psql）连接到服务器并启用 pgAudit 扩展
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 如果看到错误，请确认是否已在保存 `shared_preload_libraries` 后重启服务器。

## <a name="pgaudit-settings"></a>pgAudit 设置

可以通过 pgAudit 配置会话或对象审核日志记录。 [会话审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)会发出已执行语句的详细日志。 [对象审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)是局限于特定关系的审核。 可以选择设置一个或两个类型的日志记录。 

> [!NOTE]
> pgAudit 设置在全局范围指定，不能在数据库或角色级别指定。

[启用 pgAudit](#installing-pgaudit) 以后，即可配置其参数，以便开始日志记录。 若要配置 pgAudit，可以按照以下说明进行操作。 使用 [Azure 门户](https://portal.azure.com)：

   1. 选择你的 Azure Database for PostgreSQL 服务器。
   2. 在侧栏中选择“服务器参数”。 
   3. 搜索 `pg_audit` 参数。
   4. 选取要编辑的适当设置参数。 例如，若要开始日志记录，请将 `pgaudit.log` 设置为 `WRITE`。:::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="此屏幕截图显示 Azure Database for PostgreSQL - 使用 pgaudit 配置日志记录":::
   5. 单击“保存”按钮保存所做的更改


[pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)提供每个参数的定义。 请先测试参数，确认获取的是预期的行为。

> [!NOTE]
> 将 `pgaudit.log_client` 设置为 ON 会将日志重定向到客户端进程（例如 psql）而不是写入文件。 通常应让此设置保持禁用状态。 <br> <br>
> `pgaudit.log_level` 只有在 `pgaudit.log_client` 为 on 的情况下启用。

> [!NOTE]
> 在 Azure Database for PostgreSQL 灵活服务器中，不能使用 `-`（减号）快捷方式来设置 `pgaudit.log`（如 pgAudit 文档中所述）。 所有必需的语句类（READ、WRITE 等）均应单独指定。

## <a name="audit-log-format"></a>审核日志格式
每个审核条目通过靠近日志行开头的 `AUDIT:` 进行指示。 条目其余部分的格式详见 [pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)。

## <a name="getting-started"></a>入门
若要快速开始，请将 `pgaudit.log` 设置为 `WRITE`，并打开服务器日志来查看输出。 

## <a name="viewing-audit-logs"></a>查看审核日志
访问日志的方式取决于所选的终结点。 有关 Azure 存储，请参阅[日志存储帐户](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)一文。 有关事件中心，请参阅[流式传输 Azure 日志](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)一文。

Azure Monitor 日志将发送到所选的工作区。 Postgres 日志使用 **AzureDiagnostics** 收集模式，因此可以从 AzureDiagnostics 表查询它们。 下面描述了该表中的字段。 在 [Azure Monitor 日志查询](../../azure-monitor/logs/log-query-overview.md)概述中详细了解查询和警报。

可以通过此查询开始使用。 可以基于查询配置警报。

搜索特定服务器在过去一天生成的 Postgres 日志中的所有 pgAudit 条目
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>后续步骤
- [了解 Azure Database for PostgreSQL 灵活服务器中的日志记录](concepts-logging.md)
- [了解如何在 Azure Database for PostgreSQL 灵活服务器中设置日志记录以及如何访问日志](howto-configure-and-access-logs.md)