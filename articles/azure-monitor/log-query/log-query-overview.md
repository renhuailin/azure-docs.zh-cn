---
title: Azure Monitor 中的日志查询
description: Azure Monitor 使用的 Kusto 查询语言的参考信息。 包括特定于 Azure Monitor 的附加元素以及 Azure Monitor 日志查询中不支持的元素。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 6174bcbe5a014cff8dbd8dff242880d7f0ef7aa0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491386"
---
# <a name="log-queries-in-azure-monitor"></a>Azure Monitor 中的日志查询
Azure Monitor 日志基于 Azure 数据资源管理器，日志查询是使用相同的 Kusto 查询语言 (KQL) 编写的。 这是一种丰富的语言，旨在易于阅读和创作，因此您应该能够使用一些基本指导开始编写查询。

将使用查询 Azure Monitor 中的区域包括以下内容：

- [Log Analytics](../log-query/log-analytics-overview.md)。 Azure 门户中的主要工具，用于编辑日志查询并以交互方式分析它们的结果。 即使您打算在 Azure Monitor 中的其他地方使用日志查询，通常也可以在将其复制到其最终位置之前，先将其写入 Log Analytics 并进行测试。
- [日志警报规则](../platform/alerts-overview.md)。 主动识别工作区中数据的问题。  每个警报规则都基于定期自动运行的日志查询。  对结果进行检查，确定是否应创建警报。
- [工作簿](../platform/workbooks-overview.md)。 在 Azure 门户的交互式视觉对象报表中使用不同的可视化效果，包括日志查询的结果。
- [Azure 仪表板](../learn/tutorial-logs-dashboards.md)。 将任何查询的结果固定到 Azure 仪表板，以便将日志和指标数据可视化，并选择性地与其他 Azure 用户共享。
- [逻辑应用](../platform/logicapp-flow-connector.md)。  使用逻辑应用在自动工作流中使用日志查询的结果。
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult)。 使用命令行或 Azure 自动化 runbook （使用 AzOperationalInsightsSearchResults）在 PowerShell 脚本中使用日志查询的结果。
- [Azure Monitor 日志 API](https://dev.loganalytics.io)。 从任何 REST API 客户端的工作区中检索日志数据。  API 请求包括针对 Azure Monitor 运行的查询，用于确定要检索的数据。

## <a name="getting-started"></a>开始使用
若要开始学习使用 KQL 编写日志查询，最佳方法是利用可用的教程和示例。

- [Log Analytics 教程](log-analytics-tutorial.md) -如何使用 Log Analytics 的功能，这是在 Azure 门户中用于编辑和运行查询的工具。 它还允许您在不使用查询语言的情况下编写简单查询。 如果你之前未使用过 Log Analytics，请从此处开始了解你将在其他教程和示例中使用的工具。
- [KQL 教程](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) 指导演练基本 KQL 概念和常见运算符。 这是从语言本身和日志查询结构入手的最佳入门场所。 
- [示例查询](example-queries.md) -Log Analytics 中提供的示例查询的说明。 无需修改即可使用查询，也无需将其用作示例即可了解 KQL。
- [查询示例](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) -演示各种不同概念的示例查询。



## <a name="reference-documentation"></a>参考文档
Azure 数据资源管理器文档提供了[有关 KQL 的文档](/azure/data-explorer/kusto/query/)，其中包括所有命令和操作员的参考。 即使您使用 KQL 时，仍会定期使用该参考来调查以前未使用过的新命令和方案。


## <a name="language-differences"></a>语言差异
尽管 Azure Monitor 与 Azure 数据资源管理器使用相同的 KQL，但也存在一些差异。 KQL 文档将指定 Azure Monitor 或具有不同功能的那些运算符。 Azure Monitor 内容中介绍了特定于 Azure Monitor 的运算符。 以下各节提供了用于快速参考的语言版本之间的差异列表。

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

* [app()](app-expression.md)
* [资源 ( # B1 ](resource-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>后续步骤
- 演练[有关编写查询的教程](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)。
- 访问完整的 [Kusto 查询语言的参考文档](/azure/kusto/query/)。

