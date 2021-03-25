---
title: Azure Monitor Log Analytics 中的日志查询范围
description: 介绍 Azure Monitor Log Analytics 中的日志查询的范围和时间范围。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 43e4e861905352c2818dfb08b8cb442bd70481c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047173"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics 中的日志查询范围和时间范围
在 [Azure 门户上的 Log Analytics 中](../logs/log-analytics-tutorial.md)运行[日志查询](../logs/log-query-overview.md)时，该查询评估的数据集取决于所选的范围和时间范围。 本文介绍范围和时间范围，以及如何根据要求设置这两项。 本文还介绍了不同范围类型的行为。


## <a name="query-scope"></a>查询范围
查询范围定义查询评估的记录。 通常，这包括单个 Log Analytics 工作区或 Application Insights 应用程序中的所有记录。 Log Analytics 还允许针对特定的受监视 Azure 资源设置范围。 这样，资源所有者就可以专注于其数据，即使该资源向多个工作区写入数据。

范围始终显示在 Log Analytics 窗口的左上方。 如果出现了图标，则表示范围是 Log Analytics 工作区或 Application Insights 应用程序。 如果没有图标，则表示范围是另一个 Azure 资源。

![门户中显示的范围](media/scope/scope.png)

范围由启动 Log Analytics 所用的方法确定，在某些情况下，可以通过单击范围来更改范围。 下表列出了所用的不同类型范围及其各种详细信息。

> [!IMPORTANT]
> 如果在 Application Insights 中使用基于工作区的应用程序，则其数据与所有其他的日志数据一起存储在 Log Analytics 工作区中。 考虑到后向兼容性，当你选择应用程序作为范围时，系统会让你获得经典的 Application Insights 体验。 若要在 Log Analytics 工作区中查看此数据，请将范围设置为工作区。

| 查询范围 | 范围中的记录 | 如何选择 | 更改范围 |
|:---|:---|:---|:---|
| Log Analytics 工作区 | Log Analytics 工作区中的所有记录。 | 从“Azure Monitor”菜单或“Log Analytics 工作区”菜单中选择“日志”。    | 可将范围更改为任何其他资源类型。 |
| Application Insights 应用程序 | Application Insights 应用程序中的所有记录。 | 从应用程序的“Application Insights”菜单中选择“日志”。 | 只能将范围更改为另一个 Application Insights 应用程序。 |
| 资源组 | 资源组中的所有资源创建的记录。 可以包含多个 Log Analytics 工作区中的数据。 | 从资源组菜单中选择“日志”。 | 无法更改范围。|
| 订阅 | 订阅中的所有资源创建的记录。 可以包含多个 Log Analytics 工作区中的数据。 | 从订阅菜单中选择“日志”。   | 无法更改范围。 |
| 其他 Azure 资源 | 资源创建的记录。 可以包含多个 Log Analytics 工作区中的数据。  | 从资源菜单中选择“日志”。<br>OR<br>从“Azure Monitor”菜单中选择“日志”，然后选择新范围。  | 只能将范围更改为相同的资源类型。 |

### <a name="limitations-when-scoped-to-a-resource"></a>将范围限定为资源时的限制

如果查询范围是 Log Analytics 工作区或 Application Insights 应用程序，门户中的所有选项以及所有查询命令均可用。 不过，如果将范围限定为资源，则门户中的以下选项不可用，因为它们与单个工作区或应用程序相关联：

- 保存
- 查询资源管理器
- 新建警报规则

将范围限定为资源时无法使用以下命令，因为查询范围已经包含了带有该资源或资源集的数据的所有工作区：

- [app](../logs/app-expression.md)
- [工作区](../logs/workspace-expression.md)
 

## <a name="query-scope-limits"></a>查询范围限制
将范围设置为一个资源或一组资源是 Log Analytics 的特别强大的功能，因为它允许你在单个查询中自动合并分布式数据。 不过，如果需要从多个 Azure 区域的工作区检索数据，它可能会显著影响性能。

Log Analytics 有助于防止跨多个区域中工作区的查询的过量开销，其方法是在特定数目的区域被使用时发出警告或错误。 如果范围包含的工作区位于 5 个或更多个区域中，则查询会收到警告。 查询仍会运行，但可能需要很长的时间才能完成。

![查询警告](media/scope/query-warning.png)

如果范围包含的工作区位于 20 个或更多个区域中，则系统会阻止查询运行。 在这种情况下，系统会要求你减少工作区区域的数目，然后尝试再次运行查询。 下拉菜单会显示查询范围内的所有区域，你应该减少区域的数目，然后尝试再次运行查询。

![查询失败](media/scope/query-failed.png)


## <a name="time-range"></a>时间范围
时间范围根据记录的创建时间，指定查询要评估的记录集。 此项设置由工作区或应用程序中每条记录上的 TimeGenerated 列定义，如下表所指定的那样。 对于经典 Application Insights 应用程序，timestamp 列将用于时间范围。


若要设置时间范围，可在 Log Analytics 窗口顶部的时间选取器中进行选择。  可以选择预定义的时间段，或选择“自定义”来指定特定的时间范围。

![时间选取器](media/scope/time-picker.png)

如果在使用上表所示的“标准时间”列的查询中设置筛选器，时间选取器将更改为“在查询中设置”并且会被禁用。 在这种情况下，最有效的做法是将筛选器放在查询的顶部，这样，以后只需处理筛选的记录。

![筛选的查询](media/scope/query-filtered.png)

如果使用 [workspace](../logs/workspace-expression.md) 或 [app](../logs/app-expression.md) 命令从另一个工作区或经典应用程序检索数据，时间选取器的行为可能有所不同。 如果范围是 Log Analytics 工作区，而你使用的是 app，或者，如果范围是经典 Application Insights 应用程序，而你使用的是 workspace，则 Log Analytics 可能不知道应该由筛选器中使用的列来确定时间筛选器。

在以下示例中，范围设置为 Log Analytics 工作区。  查询使用 **workspace** 从另一个 Log Analytics 工作区检索数据。 时间选取器将更改为“在查询中设置”，因为它会看到一个使用预期的 **TimeGenerated** 列的筛选器。

![使用 workspace 的查询](media/scope/query-workspace.png)

不过，如果查询使用 app 从经典 Application Insights 应用程序检索数据，Log Analytics 将无法识别筛选器中的 timestamp 列，而时间选取器将保持不变。 在这种情况下，会应用这两个筛选器。 在该示例中，即使查询在 **where** 子句中指定了 7 天，它也只包含过去 24 小时创建的记录。

![使用 app 的查询](media/scope/query-app.png)

## <a name="next-steps"></a>后续步骤

- 演练[有关在 Azure 门户中使用 Log Analytics 的教程](../logs/log-analytics-tutorial.md)。
- 演练[有关编写查询的教程](../logs/get-started-queries.md)。