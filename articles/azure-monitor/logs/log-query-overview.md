---
title: Azure Monitor 中的日志查询
description: Azure Monitor 使用的 Kusto 查询语言的参考信息。 包括特定于 Azure Monitor 的附加元素以及 Azure Monitor 日志查询中不支持的元素。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 272c146c71e9caf6d7ba6a1ba165a6157f5b6de0
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445845"
---
# <a name="log-queries-in-azure-monitor"></a>Azure Monitor 中的日志查询
Azure Monitor 日志基于 Azure 数据资源管理器，日志查询是使用相同的 Kusto 查询语言 (KQL) 编写的。 这是一种旨在方便阅读和创作的丰富语言，你只需接受一些基本指导，便可开始编写查询。

Azure Monitor 中使用查询的区域包括以下几个：

- [Log Analytics](../logs/log-analytics-overview.md)。 Azure 门户中的主要工具，用于编辑日志查询以及以交互方式分析其结果。 即便你打算在 Azure Monitor 的其他位置使用日志查询，通常也是先在 Log Analytics 中编写和测试查询，然后再将其复制到最终位置。
- [日志警报规则](../alerts/alerts-overview.md)。 主动识别工作区中数据的问题。  每个警报规则均基于定期自动运行的日志查询。  对结果进行检查，确定是否应创建警报。
- [工作簿](../visualize/workbooks-overview.md)。 在 Azure 门户中通过交互式视觉对象报表中的各种可视化效果提供日志查询结果。
- [Azure 仪表板](../visualize/tutorial-logs-dashboards.md)。 将任何查询的结果固定到 Azure 仪表板，这样就可以将日志和指标数据一起可视化，并且还可以将其与其他 Azure 用户共享。
- [逻辑应用](../logs/logicapp-flow-connector.md)。  通过逻辑应用在自动化工作流中使用日志查询结果。
- [PowerShell](/powershell/module/az.operationalinsights/invoke-azoperationalinsightsquery)。 使用命令行中 PowerShell 脚本或使用 Invoke-AzOperationalInsightsQuery 的 Azure 自动化 runbook 中的日志查询结果。
- [Azure Monitor 日志 API](https://dev.loganalytics.io)。 从任何 REST API 客户端检索工作区中的日志数据。  API 请求包括针对 Azure Monitor 运行的查询，用于确定要检索的数据。

## <a name="getting-started"></a>入门
若要开始学习使用 KQL 编写日志查询，最佳方法是利用可用的教程和示例。

- [Log Analytics 教程](./log-analytics-tutorial.md) - 介绍如何使用 Log Analytics 功能的教程。Log Analytics 是 Azure 门户中用来编辑和运行查询的工具。 它还允许你在不直接使用查询语言的情况下编写简单查询。 如果你以前没有使用过 Log Analytics，可从本教程着手，了解将在其他教程和示例中使用的工具。
- [KQL 教程](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) - 引导性地介绍了基本的 KQL 概念和常用运算符。 这是着手了解语言本身和日志查询结构的最佳教程。 
- [示例查询](../logs/queries.md) - 描述 Log Analytics 中可用的示例查询。 你可以不加修改地使用这些查询，也可以将它们作为学习 KQL 的示例。
- [查询示例](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) - 演示各种不同概念的示例查询。



## <a name="reference-documentation"></a>参考文档
[KQL 文档](/azure/data-explorer/kusto/query/)包含所有命令和运算符的参考信息，可在 Azure 数据资源管理器文档中获得。 即使精通 KQL，你仍然会定期使用这些参考信息来研究以前没有用过的新命令和新方案。


## <a name="language-differences"></a>语言差异
虽然 Azure Monitor 与 Azure 数据资源管理器使用相同的 KQL，但还是有一些区别。 KQL 文档将指定那些不受 Azure Monitor 支持或具有不同功能的运算符。 Azure Monitor 内容中介绍了特定于 Azure Monitor 的运算符。 以下部分列出了该语言不同版本之间的差异，以供快速参考。

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的语句

* [Alias](/azure/kusto/query/aliasstatement)
* [Query parameters](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的函数

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current()、current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的运算符

* [跨群集加入](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Azure Monitor 中不支持的插件

* [Python 插件](/azure/kusto/query/pythonplugin)
* [sql_request 插件](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Azure Monitor 中的附加运算符
下列运算符支持特定的 Azure Monitor 功能并且在 Azure Monitor 外部不可用。

* [app()](../logs/app-expression.md)
* [resource()](./resource-expression.md)
* [workspace()](../logs/workspace-expression.md)

## <a name="next-steps"></a>后续步骤
- 演练[有关编写查询的教程](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)。
- 访问完整的 [Kusto 查询语言的参考文档](/azure/kusto/query/)。
