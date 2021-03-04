---
title: 在 Azure Monitor 中排查日志警报问题 | Microsoft Docs
description: Azure 中日志警报规则的常见问题、错误和解决方法。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6b495e8e700f146f5e6c117413456311ac0cdd5a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037874"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>在 Azure Monitor 中排查日志警报问题  

本文介绍如何解决 Azure Monitor 中日志警报的常见问题。 它还提供了有关日志警报功能和配置的常见问题的解决方法。

通过日志警报，用户可以使用 [Log Analytics](../logs/log-analytics-tutorial.md) 查询按每个设置的频率评估资源日志，并根据结果触发警报。 规则可以使用[操作组](./action-groups.md)触发一个或多个操作。 [详细了解日志警报的功能和术语](alerts-unified-log.md)。

> [!NOTE]
> 本文不考虑 Azure 门户中显示警报规则已触发以及不是通过关联的操作组执行通知的情况。 对于这类情况，请参阅[此处](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)，了解故障排除的详细信息。

## <a name="log-alert-didnt-fire"></a>日志警报未激发

### <a name="data-ingestion-time-for-logs"></a>日志的数据引入时间

Azure Monitor 处理来自世界各地的数 TB 的客户日志，这可能导致[日志引入延迟](../logs/data-ingestion-time.md)。

日志是半结构化数据，本质上比指标的延迟更大。 如果触发警报的延迟超过 4 分钟，则应考虑使用[指标警报](alerts-metric-overview.md)。 你可以使用[日志的指标警报](alerts-metric-logs.md)将数据从日志发送到指标存储。

系统会多次重试警报评估以减少延迟。 数据到达后，警报就会触发，这在大多数情况下并不等于日志记录时间。

### <a name="incorrect-query-time-range-configured"></a>配置的查询时间范围不正确

查询时间范围是在规则条件定义中设置的。 此字段在工作区和 Application Insights 中称为“期间”，在所有其他资源类型中则称为“替代查询时间范围” 。 与日志分析一样，时间范围将查询数据限制在指定的时间段内。 即使在查询中使用了 ago 命令，时间范围也将适用。 

例如，即使文本包含 ago(1d)，查询也会扫描 60 分钟（当时间范围为 60 分钟时）。 时间范围和查询时间筛选需要匹配。 在这个例子中，将“期间” / “替代查询时间范围”更改为一天，将可以正常工作 。

![时间段](media/alerts-troubleshoot-log/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>预警规则中的操作处于静音状态

日志警报提供了一个选项，可以在设定的时间内禁用已触发的警报操作。 在工作区和 Application Insights 中，此字段称为“阻止警报”。 在所有其他资源类型中，它称为“静音操作”。 

一个常见问题是，你认为警报是由于服务问题而没有触发操作。 甚至还被规则配置静音了。

![阻止警报](media/alerts-troubleshoot-log/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>使用旧版 Log Analytics API 进行拆分的指标度量预警规则

[指标度量](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)是一种基于汇总时序结果的日志警报类型。 这些规则允许按列分组[拆分警报](alerts-unified-log.md#split-by-alert-dimensions)。 如果你使用的是旧版 Log Analytics API，则拆分将无法按预期工作。 不支持在旧版 API 中选择分组。

当前的 ScheduledQueryRules API 允许你在[指标度量](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)规则中设置“聚合依据”，这将按预期工作。 [详细了解如何切换到当前的 SCHEDULEDQUERYRULES API](../alerts/alerts-log-api-switch.md)。

## <a name="log-alert-fired-unnecessarily"></a>不必要地激发了日志警报

[Azure Monitor 中配置的日志预警规则](./alerts-log.md)可能会意外触发该规则。 以下部分描述了某些常见原因。

### <a name="alert-triggered-by-partial-data"></a>部分数据触发了警报

Azure Monitor 处理来自世界各地的数 TB 的客户日志，这可能导致[日志引入延迟](../logs/data-ingestion-time.md)。

日志是半结构化数据，本质上比指标的延迟更大。 如果触发警报出现很多误触发，则应考虑使用[指标警报](alerts-metric-overview.md)。 你可以使用[日志的指标警报](alerts-metric-logs.md)将数据从日志发送到指标存储。

当你尝试检测日志中的数据时，日志警报效果最佳。 当你试图检测日志中缺少数据时，它的效果就不太好。 例如，对虚拟机检测信号发出警报。 

尽管有内置的功能可以防止错误警报，但它们仍然可以在高延迟数据（约 30 分钟以上）和延迟峰值数据上发生。

### <a name="query-optimization-issues"></a>查询优化问题

警报服务将优化你的查询，以降低负载和警报延迟。 生成警报流是为了将指示问题的结果转换为警报。 例如，在如下查询中：

``` Kusto
SecurityEvent
| where EventID == 4624
```

如果用户的意图是发出警报，那么当这个事件类型发生时，警报逻辑会将 `count` 追加到查询中。 要运行的查询将如下所示：

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

无需向查询添加警报逻辑，这样做甚至可能导致问题。 在上面的示例中，如果你在查询中包括 `count`，它将始终得到值 1，因为警报服务将执行 `count` 的 `count`。

优化的查询是日志警报服务运行的内容。 你可以在 Log Analytics [门户](../logs/log-query-overview.md)或 [API](/rest/api/loganalytics/) 中运行修改后的查询。

在工作区和 Application Insights 中，它在条件窗格中称为“要执行的查询”。 在所有其他资源类型中，在条件选项卡中选择“查看最终警报查询”。

![要执行的查询](media/alerts-troubleshoot-log/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>已禁用日志警报

以下部分列出了 Azure Monitor 禁用日志预警规则的一些原因。 我们还提供了[禁用规则时发送的活动日志示例](#activity-log-example-when-rule-is-disabled)。

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>警报范围不再存在或已被移动

当预警规则的范围资源不再有效时，该规则的执行将失败。 在这种情况下，还会停止计费。

如果连续一周失败，Azure Monitor 将在一周后禁用日志警报。

### <a name="query-used-in-a-log-alert-isnt-valid"></a>日志警报中使用的查询无效

创建日志预警规则后，将验证查询的语法是否正确。 但有时，日志预警规则中提供的查询可能会开始失败。 下面是一些常见原因：

- 规则是通过 API 创建的，并且用户跳过了验证。
- 查询[在多个资源上运行](../logs/cross-workspace-query.md)，并且已删除或移动一个或多个资源。
- [查询失败](https://dev.loganalytics.io/documentation/Using-the-API/Errors)，因为：
    - 未将日志记录解决方案[部署到工作空间](../insights/solutions.md#install-a-monitoring-solution)，因此未创建表。
    - 数据在查询中停止流向表超过 30 天。
    - 由于未启动数据流，因此尚未创建[自定义日志表](../agents/data-sources-custom-logs.md)。
- [查询语言](/azure/kusto/query/)的更改包含命令和函数的已修改格式。 因此，以前提供的查询不再有效。

[Azure 顾问](../../advisor/advisor-overview.md)会警告此类行为。 它添加了关于受影响的日志预警规则的建议。 使用的类别是“高可用性”，影响为“中等”，描述内容为“修复日志预警规则以确保监视”。

## <a name="alert-rule-quota-was-reached"></a>已达到警报规则配额

每个订阅和资源的日志搜索预警规则数目受[此处](../service-limits.md)所述的配额限制约束。

### <a name="recommended-steps"></a>建议的步骤
    
如果已达到配额限制，请执行以下步骤以帮助解决此问题。

1. 尝试删除或禁用不再使用的日志搜索预警规则。
1. 尝试使用[按维度拆分警报](alerts-unified-log.md#split-by-alert-dimensions)以减少规则计数。 这些规则可以监视许多资源和检测案例。
1. 如果需要提高配额限制，请继续创建支持请求，并提供以下信息：

    - 需要提高配额限制的订阅 ID 和资源 ID。
    - 配额增加的原因。
    - 增大配额的资源类型：Log Analytics 和 Application Insights 等 。
    - 请求的配额限制。


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>检查新日志预警规则的当前使用情况
    
#### <a name="from-the-azure-portal"></a>通过 Azure 门户

1. 打开“警报”屏幕，然后选择“管理预警规则” 
2. 使用“订阅”下拉列表控件筛选到相关订阅
3. 请勿筛选到特定的资源组、资源类型或资源
4. 在“信号类型”下拉列表控件中，选择“日志搜索”
5. 验证“状态”下拉列表控件是否设置为“已启用”
6. 日志搜索预警规则总数将显示在规则列表上方

#### <a name="from-api"></a>通过 API

- PowerShell - [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [按订阅列出](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>禁用规则时的活动日志示例

如果查询连续七天失败，Azure Monitor 将禁用日志警报并停止规则的计费。 可以通过 [Azure 活动日志](../../azure-resource-manager/management/view-activity-logs.md)查看 Azure Monitor 禁用日志警报的确切时间。 请参阅此示例：

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 中的日志警报](./alerts-unified-log.md)。
- 了解有关[配置日志警报](../logs/log-query-overview.md)的详细信息。
- 了解有关[日志查询](../logs/log-query-overview.md)的详细信息。