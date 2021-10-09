---
title: 日志 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 如何访问 Azure Database for PostgreSQL 超大规模 (Citus) 的数据库日志
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/13/2021
ms.openlocfilehash: 72996a44892d17a44ea58405e92bb9ac6cc52c68
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546430"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL 超大规模 (Citus) 中的日志

超大规模 (Citus) 服务器组的每个节点都可以获取 PostgreSQL 数据库服务器日志。 可以将日志发送到存储服务器或分析服务。 这些日志可用于识别、排除和修复配置错误和性能不佳问题。

## <a name="capturing-logs"></a>捕获日志

要访问超大规模 (Citus) 协调器或工作器节点的 PostgreSQL 日志，必须启用 PostgreSQLLogs 诊断设置。 在 Azure 门户中，打开“诊断设置”，然后选择“+ 添加诊断设置”。 

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="“添加诊断设置”按钮":::

为新的诊断设置选择一个名称，选中“PostgreSQLLogs”框，然后选中“发送到 Log Analytics 工作区”框。   再选择“保存”。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="选择 PostgreSQL 日志":::

## <a name="viewing-logs"></a>查看日志

为查看和筛选日志，我们将使用 Kusto 查询。 在你的超大规模 (Citus) 服务器组的 Azure 门户中，打开“日志”。 如果显示查询选择对话框，请将其关闭：

:::image type="content" source="media/howto-hyperscale-logging/logs-dialog.png" alt-text="打开对话框时的“日志”页":::

然后，会看到用于输入查询的输入框。

:::image type="content" source="media/howto-hyperscale-logging/logs-query.png" alt-text="用于查询日志的输入框":::

输入以下查询并选择“运行”按钮。

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
```

上面的查询列出了所有节点的日志消息及其严重性和时间戳。 可以添加 `where` 子句来筛选结果。 例如，若要仅查看协调器节点中的错误，请按如下所示筛选错误级别和服务器名称：

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
| where LogicalServerName_s == 'example-server-group-c'
| where errorLevel_s == 'ERROR'
```

将上述示例中的服务器名称替换为你的服务器名称。 协调器节点名称具有后缀 `-c`，而工作节点则以 `-w0`、`-w1` 等后缀命名。

可以通过不同方式筛选 Azure 日志。 下面介绍如何查找过去一天中消息与正则表达式匹配的日志。

```kusto
AzureDiagnostics
| where TimeGenerated > ago(24h)
| order by TimeGenerated desc
| where Message matches regex ".*error.*"
```

## <a name="next-steps"></a>后续步骤

- [Log Analytics 查询入门](../azure-monitor/logs/log-analytics-tutorial.md)
- 了解 [Azure 事件中心](../event-hubs/event-hubs-about.md)
