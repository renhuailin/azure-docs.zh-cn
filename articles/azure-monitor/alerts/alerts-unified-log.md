---
title: Azure Monitor 中的日志警报
description: 满足指定的日志查询条件时，触发电子邮件、通知、调用网站 URL (Webhook) 或自动执行。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 3eb0ab8ac6ca4c0ceddd9e3ebf84b8c2ddd5a9f4
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113301305"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor 中的日志警报

## <a name="overview"></a>概述

日志警报是 [Azure 警报](./alerts-overview.md)中支持的警报类型之一。 使用日志警报，用户可以通过 [Log Analytics](../logs/log-analytics-tutorial.md) 查询按每个设置的频率评估资源日志并基于结果触发警报。 规则可以使用[操作组](./action-groups.md)触发一个或多个操作。

> [!NOTE]
> 可以将 [Log Analytics 工作区](../logs/log-analytics-tutorial.md)中的日志数据发送到 Azure Monitor 指标存储。 指标警报具有[不同的行为](alerts-metric-overview.md)，该行为可能更可取，具体取决于你要使用的数据。 要了解如何将日志路由到指标，请参阅[日志的指标警报](alerts-metric-logs.md)。

> [!NOTE]
> 当前不对 API 版本 `2020-05-01-preview` 和以资源为中心的日志警报收取额外费用。  未来将公布预览版中的功能的定价以及开始计费之前提供的通知。 如果你选择在通知期过后继续使用新 API 版本和以资源为中心的日志警报，需要按适用的费率付费。

## <a name="prerequisites"></a>先决条件

日志警报运行对 Log Analytics 数据的查询。 首先，你应开始[收集日志数据](../essentials/resource-logs.md)，并查询日志数据以查找问题。 可以通过 Log Analytics 中的[警报查询示例文章](../logs/queries.md)来了解可发现的内容，也可以[开始编写你自己的查询](../logs/log-analytics-tutorial.md)。

[Azure 监视参与者](../roles-permissions-security.md)是创建、修改和更新日志警报所需的常见角色。 还需要具有对资源日志的访问和查询执行权限。 对资源日志具有部分访问权限可能会导致查询失败或返回部分结果。 [详细了解如何在 Azure 中配置日志警报](./alerts-log.md)。

> [!NOTE]
> 过去使用旧版 [Log Analytics 警报 API](./api-alerts.md) 管理 Log Analytics 的日志警报。 [了解有关切换到当前 ScheduledQueryRules API 的详细信息](../alerts/alerts-log-api-switch.md)。

## <a name="query-evaluation-definition"></a>查询评估定义

日志搜索规则条件定义开始于：

- 要运行什么查询？
- 如何使用结果？

以下各节介绍了可用于设置上述逻辑的不同参数。

### <a name="log-query"></a>日志查询
用来评估规则的 [Log Analytics](../logs/log-analytics-tutorial.md) 查询。 此查询返回的结果用来确定是否将触发某个警报。 查询的范围可以是：

- 特定资源，例如虚拟机。
- 大规模资源，例如订阅或资源组。
- 使用[跨资源查询](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)的多个资源。 
 
> [!IMPORTANT]
> 警报查询具有约束，可确保结果的最佳性能和相关性。 [在此处了解更多信息](./alerts-log-query.md)。

> [!IMPORTANT]
> 使用当前 scheduledQueryRules API 仅支持以资源为中心的查询和[跨资源查询](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)。 如果使用旧的 [Log Analytics 警报 API](./api-alerts.md)，则需要进行切换。 [了解有关切换的详细信息](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>查询时间范围

时间范围是在规则条件定义中设置的。 在工作区和 Application Insights 中，它被称为“期间”。 在所有其他资源类型中，它被称为“替代查询时间范围”。

与在日志分析中一样，时间范围将查询数据限制在指定的范围内。 即使在查询中使用了 ago 命令，时间范围也将适用。

例如，即使文本包含 ago(1d)，查询也会扫描 60 分钟（当时间范围为 60 分钟时）。 时间范围和查询时间筛选需要匹配。 在这个例子中，将“期间” / “替代查询时间范围”更改为一天就可以正常工作 。

### <a name="measure"></a>测量

日志警报将日志转换为可计算的数字值。 你可以度量两个不同的事项：

#### <a name="count-of-the-results-table-rows"></a>结果表行的计数

结果计数是默认度量值。 适用于处理 Windows 事件日志、Syslog 和应用程序异常等事件。 当评估的时间窗口中发生或未发生日志记录时触发。

当你尝试检测日志中的数据时，日志警报效果最佳。 当你试图检测日志中是否缺少数据时，它的效果就不太好。 例如，对虚拟机检测信号发出警报。

对于工作区和 Application Insights，它在选择“结果数”的情况下称为“基于”。  在所有其他资源类型中，它在选择“表行”的情况下称为“度量值”。

> [!NOTE]
> 由于日志是半结构化数据，它们本质上比指标更隐蔽，因此，尝试检测日志中是否缺少数据时可能会遇到误触发，你应考虑使用[指标警报](alerts-metric-overview.md)。 你可以使用[日志的指标警报](alerts-metric-logs.md)将数据从日志发送到指标存储。

##### <a name="example-of-results-table-rows-count-use-case"></a>结果表行计数用例示例

你希望知道你的应用程序何时以错误代码 500（内部服务器错误）做出响应。 可以创建一个警报规则，详情如下：

- **查询：** 

```Kusto
requests
| where resultCode == "500"
```

- **时间范围/聚合粒度：** 15 分钟
- **警报频率：** 15 分钟
- **阈值：** 大于 0

然后，警报规则监视所有以错误代码 500 结束的请求。 在过去的 15 分钟，该查询每 15 分钟运行一次。 即使找到一条记录，它也会引发警报并触发所配置的操作。

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>基于数值列计算度量值（例如 CPU 计数器值）

对于工作区和 Application Insights，它在选择“指标度量”的情况下称为“基于”。  在所有其他资源类型中，它在选择任何数字列名称的情况下称为“度量值”。

### <a name="aggregation-type"></a>聚合类型

对多条记录执行的用于将其聚合为一个数值的计算。 例如：
- **计数** 返回查询中的记录数
- **平均值** 返回所定义的度量值列 [**聚合粒度**](#aggregation-granularity)的平均值。

在工作区和 Application Insights 中，它仅在“指标度量”度量值类型中受支持。 查询结果必须包含一个名为 AggregatedValue 的列，该列在用户定义的聚合之后提供数值。 在所有其他资源类型中，“聚合类型”是从该名称的字段中选择的。

### <a name="aggregation-granularity"></a>聚合粒度

确定用于将多条记录聚合为一个数值的间隔。 例如，如果你指定了 **5 分钟**，则记录会使用指定的 **聚合类型** 按 5 分钟间隔进行分组。

在工作区和 Application Insights 中，它仅在“指标度量”度量值类型中受支持。 查询结果必须包含在查询结果中设置间隔的 [bin()](/azure/kusto/query/binfunction)。 在所有其他资源类型中，控制此设置的字段称为“聚合粒度”。

> [!NOTE]
> 由于 [bin()](/azure/kusto/query/binfunction) 可能导致不均匀的时间间隔，因此，警报服务会自动将 [bin()](/azure/kusto/query/binfunction) 函数转换为针对运行时的相应时间的 [bin_at()](/azure/kusto/query/binatfunction) 函数，以确保生成针对确定时间点的结果。

### <a name="split-by-alert-dimensions"></a>按警报维度拆分

通过将警报分组为唯一的组合，按数字或字符串列将警报拆分为单独的警报。 当大规模（在订阅或资源组范围内）创建以资源为中心的警报时，可以按 Azure 资源 ID 列进行拆分。 按 Azure 资源 ID 列进行拆分会将警报的目标更改为指定的资源。

如果想要在多个 Azure 资源上监视相同的条件，建议按 Azure 资源 ID 列进行拆分。 例如，监视所有虚拟机的 CPU 使用率超过 80%。 在需要范围内的多个资源的条件时，你也可能会决定不进行拆分。 例如，监视到在资源组范围内至少有五台计算机的 CPU 使用率超过 80%。

在工作区和 Application Insights 中，它仅在“指标度量”度量值类型中受支持。 此字段称为“聚合依据”。 它限制为三个列。 查询中的分组依据列超过三个可能会导致意外的结果。 在所有其他资源类型中，它是在条件的“拆分依据维度”部分中配置的（限制为六个拆分）。

#### <a name="example-of-splitting-by-alert-dimensions"></a>按警报维度拆分的示例

例如，你想要监视在特定资源组中运行你的网站/应用的多个虚拟机的错误。 可以如下所述使用日志警报规则实现此目的：

- **查询：** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    将工作区和 Application Insights 与 **指标度量** 警报逻辑一起使用时，需要将以下行添加到查询文本：

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **资源 ID 列：** _ResourceId（目前，按警报规则中的资源 ID 列拆分这一做法仅适用于订阅和资源组）
- **维度/聚合依据：**
  - Computer = VM1、VM2（在警报规则定义中筛选值这一做法目前不适用于工作区和 Application Insights。 请在查询文本中筛选。）
- **时间范围/聚合粒度：** 15 分钟
- **警报频率：** 15 分钟
- **阈值：** 大于 0

此规则监视在过去 15 分钟内是否有任何虚拟机出现错误事件。 每个虚拟机都会被单独监视，并且会分别触发操作。

> [!NOTE]
> 按警报维度拆分这一做法仅适用于当前的 scheduledQueryRules API。 如果使用旧的 [Log Analytics 警报 API](./api-alerts.md)，则需要进行切换。 [了解有关切换的详细信息](./alerts-log-api-switch.md)。 仅在 API `2020-05-01-preview` 及更高版本中支持大规模的以资源为中心的警报。

## <a name="alert-logic-definition"></a>警报逻辑定义

定义要运行的查询并对结果进行评估后，你需要定义警报逻辑以及何时触发操作。 以下各节介绍了你可以使用的各个参数：

### <a name="threshold-and-operator"></a>阈值和运算符

查询结果将转换为一个数字，该数字将依据阈值和运算符进行比较。

### <a name="frequency"></a>频率

> [!NOTE]
> 目前不对频率为 1 分钟 1 次的日志警报（预览版）收取额外费用。 未来将公布预览版中的功能的定价以及开始计费之前提供的通知。 如果在通知期后选择继续使用频率为 1 分钟的日志警报，则将按适当的费率付费。

运行查询的间隔。 可以设置为一分钟至一天。 必须等于或小于[查询时间范围](#query-time-range)才不会错过日志记录。

例如，如果你将时间段设置为 30 分钟，将频率设置为 1 小时 1 次。  如果查询在 00:00 运行，则会返回 23:30 到 00:00 之间的记录。 下次运行查询的时间将是 01:00，将返回 00:30 到 01:00 之间的记录。 从不会评估在 00:00 到 00:30 之间创建的任何记录。

### <a name="number-of-violations-to-trigger-alert"></a>触发警报的违规次数

你可以指定警报评估时段和触发警报所需的故障次数。 允许你更好地定义触发警报的影响时间。 

例如，如果你的 [**聚合粒度**](#aggregation-granularity)规则定义为“5 分钟”，则只有在过去一小时内发生三次故障（15 分钟）时，你才能触发警报。 此设置由你的应用程序业务策略定义。

## <a name="state-and-resolving-alerts"></a>状态和解决警报

日志警报可以是无状态的，也可以是有状态的（目前处于预览版阶段）。

每次满足条件时都会触发无状态警报，即使之前已触发过。 警报实例解除后，可以[将警报标记为已关闭](../alerts/alerts-managing-alert-states.md)。 你还可以对操作进行“静音”，以防它们在警报规则触发后的一段时间内触发。 在 Log Analytics 工作区和 Application Insights 中，这称为“抑制警报”。 在所有其他资源类型中，它称为“将操作‘静音’”。 

请参阅这个警报无状态评估示例：

| 时间    | 日志条件评估 | 结果 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | 警报不会触发。 没有调用任何操作。
| 00:10 | TRUE  | 警报触发，操作组被调用。 新警报处于活动状态。
| 00:15 | TRUE  | 警报触发，操作组被调用。 新警报处于活动状态。
| 00:20 | FALSE | 警报不会触发。 没有调用任何操作。 以前的警报保持活动状态。

有状态警报会在每次事件后触发一次并解决。 如果在特定评估期间出现 30 分钟都不符合警报条件的情况（考虑到日志引入存在延迟），或者出现连续三次评估都不符合警报条件的情况（这是为了减少不稳定情况下出现的干扰），则警报会解除。 例如，如果频率为 5 分钟/次，警报会在 40 分钟后解除；如果频率为 1 分钟/次，警报会在 32 分钟后解除。 通过 Webhook 或电子邮件发出“已解除”通知后，Azure 门户中警报实例的状态（称为“监视状态”）也会设置为“已解除”。

监控状态的警报功能目前在 Azure 公有云中处于预览版阶段。 可以使用警报详细信息部分的“自动解决警报”进行设置。

## <a name="location-selection-in-log-alerts"></a>日志警报中的位置选择

日志警报允许设置警报规则的位置。 在 Log Analytics 工作区中，规则位置必须与工作区位置匹配。 在所有其他资源中，可以选择任何受支持的位置，这些位置与 [Log Analytics 支持的区域列表](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)相符。

位置会对在哪个区域中评估警报规则造成影响。 查询会针对所选区域中的日志数据执行，但警报服务端到端是全局的。 这意味着警报规则定义、触发的警报、通知和操作不会绑定到警报规则中的位置。 数据会从设置的区域传输，因为 Azure Monitor 警报服务是[非区域性服务](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=non-regional)。

## <a name="pricing-and-billing-of-log-alerts"></a>日志警报的定价和计费

定价信息位于 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)中。 日志警报在资源提供程序 `microsoft.insights/scheduledqueryrules` 下列出，同时列出的有：

- Application Insights 上的日志警报，显示时带有确切的资源名称以及资源组和警报属性。
- 如果是使用 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) 创建的，则 Log Analytics 上的日志警报显示时带有确切的资源名称以及资源组和警报属性。
- 通过[旧式 Log Analytics API](./api-alerts.md) 创建的日志警报不是被跟踪的 [Azure资源](../../azure-resource-manager/management/overview.md)，没有强制使用的唯一资源名称。 这些警报仍作为隐藏资源在 `microsoft.insights/scheduledqueryrules` 上创建，这些资源的资源命名结构为 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`。 针对旧式 API 的日志警报显示时带有上述隐藏的资源名称以及资源组和警报属性。

> [!NOTE]
> 在隐藏的资源名称中，不受支持的资源字符（例如 `<, >, %, &, \, ?, /`）将被替换为 `_`，这也会在计费信息中反映出来。

> [!NOTE]
> 过去使用旧式 [Log Analytics 警报 API](./api-alerts.md) 以及 [Log Analytics 保存的搜索和警报](../insights/solutions.md)的旧式模板管理 Log Analytics 的日志警报。 [了解有关切换到当前 ScheduledQueryRules API 的详细信息](../alerts/alerts-log-api-switch.md)。 任何警报规则管理都应该使用[旧式 Log Analytics API](./api-alerts.md) 执行，直到你决定切换并且在这种情况下无法使用隐藏的资源。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 中创建日志警报](./alerts-log.md)。
* 了解 [Azure 日志警报中的 Webhook](../alerts/alerts-log-webhook.md)。
* 了解 [Azure 警报](./alerts-overview.md)。
* 详细了解 [Log Analytics](../logs/log-query-overview.md)。
