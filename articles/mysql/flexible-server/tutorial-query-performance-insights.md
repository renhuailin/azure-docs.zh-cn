---
title: 教程：Azure Database for MySQL 灵活服务器的 Query Performance Insight
description: 教程：Azure Database for MySQL 灵活服务器的 Query Performance Insight
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: ed78f493021c94c8beeecb8d5470d9a846b19d8d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621231"
---
# <a name="tutorial-query-performance-insights-for-azure-database-for-mysql--flexible-server"></a>教程：Azure Database for MySQL 灵活服务器的 Query Performance Insight
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Query Performance Insight 提供建议，以便为数据库提供智能查询分析。 要获取的首选见解是工作负载模式和运行时间较长的查询的相关见解。 这有助于查找需要优化的查询，以提高整体性能并有效地使用可用的资源。 Query Performance Insight 提供如下所示的详细信息，来帮助减少排查数据库性能问题的时间
* 前 N 个长时间运行的查询及其趋势。
* 能够向下钻取查询的详细信息，查看查询文本以及含最小、最大、平均和标准偏差查询时间的执行历史记录
* 资源利用率（CPU、内存和存储）
 
在本教程中，你将了解如何使用 MySQL 慢查询日志、Log Analytics 工具或工作簿模板来直观展示 Azure Database for MySQL 灵活服务器的查询性能见解。 

## <a name="prerequisites"></a>先决条件
- 你需要创建一个 Azure Database for MySQL 灵活服务器实例。 有关分步过程，请参阅[创建 Azure Database for MySQL 灵活服务器实例](./quickstart-create-server-portal.md)
- 需要创建 Log Analytics 工作区。 有关分步过程，请参阅[创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)

在本教程中，将了解如何：
>[!div class="checklist"]
> * 通过门户或使用 Azure CLI 配置慢查询日志
> * 设置诊断
> * 使用 Log Analytics 查看慢查询 
> * 使用工作簿查看慢查询 

## <a name="configure-slow-query-logs-from-portal"></a>从门户配置慢查询日志 


1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择灵活服务器。

1. 在侧栏的“设置”部分，选择“服务器参数”。 
   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="服务器参数页。":::

1. 将 slow_query_log 参数更新为“启用” 。
   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="启用慢查询日志。":::

1. 更改所需的任何其他参数（例如 `long_query_time`, `log_slow_admin_statements`). 请参阅[慢查询日志](./concepts-slow-query-logs.md#configure-slow-query-logging)文档获取更多参数。  
   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="更新慢查询日志相关参数。":::

1. 选择“保存” 。 
   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="保存慢查询日志参数。":::

在“服务器参数”  页上，可以通过关闭该页来返回到日志列表。



## <a name="configure-slow-query-logs-from-azure-cli"></a>通过 Azure CLI 配置慢速查询日志
 
若要使用 Azure CLI 执行上述操作，可以使用 CLI 为服务器启用并配置慢查询日志 

> [!IMPORTANT]
> 建议仅记录审核所需的事件类型和用户，以确保服务器的性能不会受到严重影响。

启用和配置服务器的慢速查询日志。

```azurecli
# Turn on statement level log

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec

# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs



az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>设置诊断

慢查询日志与 Azure Monitor 诊断设置集成，你便可以通过管道将日志传输到 Azure Monitor 日志、事件中心或 Azure 存储。

1. 在边栏中的“监视”  部分下，选择“诊断设置”   > “添加诊断设置”  。

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="“诊断设置”选项的屏幕截图":::

1. 提供诊断设置名称。

1. 指定发送慢查询日志的目标（日志分析工作区、存储帐户或事件中心）

    >[!Note]
    > 对于本教程，我们需要将慢查询日志发送到 Log Analytics 工作区

1. 选择 **MySqlSlowLogs** 作为日志类型。
    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="“诊断设置配置”选项的屏幕截图":::

1. 配置可以通过管道向其传送慢查询日志的数据接收器后，选择“保存”。 
    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="“诊断设置配置”选项的屏幕截图，其中突出显示了“保存”":::

    >[!Note]
    >在配置诊断设置之前，应创建数据接收器（Log Analytics 工作区、存储帐户或事件中心）。 可在配置的数据接收器（Log Analytics 工作区、存储帐户或事件中心）中访问慢查询日志。最多需要 10 分钟来显示日志。
 
## <a name="view-query-insights-using-log-analytics"></a>使用 Log Analytics 查看查询见解 

导航到“监视”部分下的“日志” 。 关闭“查询”窗口。

:::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Log Analytics 的屏幕截图":::

在查询窗口中，可以写入要执行的查询。  此处，我们使用了查询来查找特定服务器上超过 10 秒的查询

```kusto
 AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
```
    
选择“时间范围”并“运行”查询 。 你将在下方看到查询结果。  

:::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="慢查询日志的屏幕截图":::

## <a name="view-query-insights-using-workbooks"></a>使用工作簿查看查询见解 

1.  在 Azure 门户上，导航到 Azure Database for MySQL 灵活服务器的“监视”边栏选项卡，然后选择“工作簿” 。
2.  你应该能够看到模板。 选择“Query Performance Insight” 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="工作簿模板的屏幕截图":::

你将能够看到以下可视化效果 
>[!div class="checklist"]
> * 查询加载
> * 活动连接总数
> * 慢查询趋势(>10 秒查询时间)
> * 慢查询详细信息
> * 列出前 5 个最长查询
> * 按最小值、最大值、平均值和标准偏差查询时间汇总慢查询

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="长时间运行的查询的屏幕截图":::

>[!Note]
> * 对于资源利用率，可以使用“概述”模板。
> * 还可以编辑这些模板并根据需要进行自定义，了解更多详细信息，请参阅 [Azure Monitor 工作簿概述 - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)
> * 若想快速查看，可以将工作簿或 Log Analytics 查询固定到仪表板。 有关详细信息，请参阅[在 Azure 门户中创建仪表板 - Azure 门户](../../azure-portal/azure-portal-dashboards.md) 

Query Performance Insight 中有两个指标可以帮助找到潜在瓶颈：持续时间和执行计数。 长时间运行的查询长时间锁定资源、阻止其他用户和限制可伸缩性的可能性最大。 在某些情况下，较高的执行计数可能会导致网络往返增加。 往返会影响性能。 它们会造成网络延迟和下游服务器的延迟。 因此，执行计数有助于查找频繁执行（“琐碎”）的查询。 这些查询是最需要优化的优化候选项。 

## <a name="next-steps"></a>后续步骤
- [入门 Azure Monitor 工作簿](../../azure-monitor/visualize/workbooks-overview.md#visualizations)，详细了解工作簿丰富的可视化效果选项。
- 详细了解[慢查询日志](concepts-slow-query-logs.md)


