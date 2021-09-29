---
title: 在 Azure Monitor 中排查日志警报问题 | Microsoft Docs
description: Azure 中日志警报规则的常见问题、错误和解决方法。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aafd6f2e0d5c10b1e3e1698176734a83083ef72d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744500"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>在 Azure Monitor 中排查日志警报问题  

本文介绍如何解决 Azure Monitor 中日志警报的常见问题。 它还提供了有关日志警报功能和配置的常见问题的解决方法。

可以使用日志警报，通过使用 [Log Analytics](../logs/log-analytics-tutorial.md) 查询来按设定的频率评估资源日志，并触发基于结果的警报。 规则可以使用[操作组](./action-groups.md)触发一个或多个操作。 若要详细了解日志警报的功能和术语，请参阅 [Azure Monitor 中的日志警报](alerts-unified-log.md)。

> [!NOTE]
> 本文不考虑这样的情况：Azure 门户显示触发了警报规则但未收到通知。 对于这种情况，请参阅[我的警报上的操作或通知未按预期效果工作](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)。

## <a name="log-alert-didnt-fire"></a>日志警报未激发

### <a name="data-ingestion-time-for-logs"></a>日志的数据引入时间

Azure Monitor 处理来自世界各地的数 TB 的客户日志，这可能导致[日志引入延迟](../logs/data-ingestion-time.md)。

日志是半结构化的数据，本质上比指标更加隐性。 如果遇到在触发警报时延迟超过 4 分钟的情况，应考虑使用[指标警报](alerts-metric-overview.md)。 你可以使用[日志的指标警报](alerts-metric-logs.md)将数据从日志发送到指标存储。

为了缓解延迟，系统会多次重试警报评估。 在数据到达后，该警报会触发，这在大多数情况下并不等于日志记录时间。

### <a name="incorrect-query-time-range-configured"></a>配置的查询时间范围不正确

查询时间范围是在规则条件定义中设置的。 对于工作区和 Application Insights，此字段被称为“时间段”。 对于所有其他资源类型，此字段被称为“替代查询时间范围”。 与日志分析一样，时间范围将查询数据限制在指定的时间段内。 即使在查询中使用 ago 命令，也将会采用时间范围。 

例如，在时间范围为 60 分钟时，查询会扫描 60 分钟，即使文本包含“ago(1d)”也是如此。 时间范围和查询时间筛选需要匹配。 在此示例中，将“时间段” / “替代查询时间范围”更改为一天，会按预期方式工作 。

![时间段](media/alerts-troubleshoot-log/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>预警规则中的操作处于静音状态

日志警报提供了一个选项，可以在设定的时间内禁用已触发的警报操作。 在工作区和 Application Insights 中，此字段被称为“抑制警报”。 在所有其他资源类型中，它称为“静音操作”。 

一个常见问题是，你认为该警报未触发操作（即使规则配置已将该警报静音）。

![阻止警报](media/alerts-troubleshoot-log/LogAlertSuppress.png)

### <a name="alert-scope-resource-has-been-moved-renamed-or-deleted"></a>已移动、重命名或删除了警报范围资源

在创作警报规则时，Log Analytics 会为用户 ID 创建权限快照。 此快照保存在规则中并包含规则范围资源、Azure 资源管理器 ID。 如果移动、重命名或删除规则范围资源，则所有引用该资源的日志警报规则都会中断。 若要正常工作，需要使用新的 Azure 资源管理器 ID 来重新创建警报规则。

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>使用旧版 Log Analytics API 进行拆分的指标度量预警规则

[指标度量](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)是基于汇总的时序结果的日志警报类型。 可以使用这些规则按列分组，以[拆分警报](alerts-unified-log.md#split-by-alert-dimensions)。 如果使用旧版 Log Analytics API，则拆分不会按预期工作，因为它不支持分组。

可以使用当前的 ScheduledQueryRules API 在[指标度量](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)规则中设置“聚合打开”，这样就会按预期方式工作。 若要详细了解如何切换到当前 ScheduledQueryRules API，请参阅 [从旧版 Log Analytics 警报 API 升级到当前日志警报 API](../alerts/alerts-log-api-switch.md)。

## <a name="log-alert-fired-unnecessarily"></a>不必要地激发了日志警报

[Azure Monitor 中配置的日志预警规则](./alerts-log.md)可能会意外触发该规则。 以下部分描述了某些常见原因。

### <a name="alert-triggered-by-partial-data"></a>部分数据触发了警报

Azure Monitor 处理来自世界各地的数 TB 的客户日志，这可能导致[日志引入延迟](../logs/data-ingestion-time.md)。

日志是半结构化的数据，本质上比指标更加隐性。 如果触发警报出现很多误触发，则应考虑使用[指标警报](alerts-metric-overview.md)。 你可以使用[日志的指标警报](alerts-metric-logs.md)将数据从日志发送到指标存储。

当你尝试检测日志中的数据时，日志警报效果最佳。 它的效果在你尝试检测日志中缺少数据时不太好，例如对虚拟机检测信号发出警报。 

有内置功能可以防止错误警报，但它们仍然可以在高延迟数据（约 30 分钟以上）和延迟峰值数据上发生。

### <a name="query-optimization-issues"></a>查询优化问题

警报服务将优化你的查询，以降低负载和警报延迟。 生成警报流是为了将指示问题的结果转换为警报。 例如，在如下查询中：

``` Kusto
SecurityEvent
| where EventID == 4624
```

如果用户的意图是发出警报，那么当这个事件类型发生时，警报逻辑会将 `count` 追加到查询中。 将要运行的查询如下所示：

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

无需向该查询添加警报逻辑，那样甚至可能会导致出现问题。 在前面的示例中，如果在查询中包含 `count`，则它始终会生成值 1，因为警报服务会执行 `count` 中的 `count`。

日志警报服务运行经过优化的查询。 可以在 Log Analytics [门户](../logs/log-query-overview.md)或 [API](/rest/api/loganalytics/) 中运行经过修改的查询。

对于工作区和 Application Insights，它在“条件”窗格中被称为“要执行的查询”。 在所有其他资源类型中，请选择“条件”选项卡上的“查看最终警报查询” 。

![要执行的查询](media/alerts-troubleshoot-log/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>已禁用日志警报

以下部分列出了 Azure Monitor 禁用日志预警规则的一些原因。 在这些部分之后，有[在禁用规则时发送的活动日志的示例](#activity-log-example-when-rule-is-disabled)。

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>警报范围不再存在或已被移动

在警报规则的范围资源不再有效时，规则执行会失败，并且会停止计费。

如果日志警报在一周内持续失败，Azure Monitor 会将其禁用。

### <a name="query-used-in-a-log-alert-isnt-valid"></a>日志警报中使用的查询无效

创建日志预警规则后，将验证查询的语法是否正确。 但有时，日志预警规则中提供的查询可能会开始失败。 下面是一些常见原因：

- 规则是通过 API 创建的，并且用户跳过了验证。
- 查询[在多个资源上运行](../logs/cross-workspace-query.md)，并且已删除或移动了一个或多个资源。
- [查询失败](https://dev.loganalytics.io/documentation/Using-the-API/Errors)，因为：
    - 未将日志记录解决方案[部署到工作空间](../insights/solutions.md#install-a-monitoring-solution)，因此未创建表。
    - 数据停止流向查询中的表已超过 30 天。
    - [自定义日志表](../agents/data-sources-custom-logs.md)尚未创建，因为数据流尚未启动。
- [查询语言](/azure/kusto/query/)中的更改包括用于命令和函数的修改后的格式，因此，先前提供的查询不再有效。

[Azure 顾问](../../advisor/advisor-overview.md)会警告此类行为。 它添加了关于受影响的日志警报规则的建议。 使用的类别是“高可用性”，影响为“中等”，描述内容为“修复日志预警规则以确保监视”。

## <a name="alert-rule-quota-was-reached"></a>已达到警报规则配额

有关每个订阅的日志搜索警报规则数和资源的最大限制的详细信息，请参阅 [Azure Monitor 服务限制](../service-limits.md)。

### <a name="recommended-steps"></a>建议的步骤
    
如果已达到配额限制，以下步骤可能会有助于解决此问题。

1. 删除或禁用不再使用的日志搜索警报规则。
1. 使用[按维度拆分警报](alerts-unified-log.md#split-by-alert-dimensions)来减少规则计数。 这些规则可以监视许多资源和检测案例。
1. 如果需要提高配额限制，请继续创建支持请求，并提供以下信息：

    - 需要提高配额限制的订阅 ID 和资源 ID
    - 配额增加的原因
    - 配额增加的资源类型，例如 Log Analytics 或 Application Insights 
    - 请求的配额限制

### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>检查新日志预警规则的当前使用情况
    
#### <a name="from-the-azure-portal"></a>通过 Azure 门户

1. 在“警报”屏幕上，选择“管理警报规则”。
1. 在“订阅”下拉列表控件中，筛选到所需订阅。 （切勿筛选到某个特定资源组、资源类型或资源。）
1. 在“信号类型”下拉列表控件中，选择“日志搜索” 。
1. 验证“状态”下拉列表控件是否设置为“已启用” 。

日志搜索警报规则的总数会显示在规则列表上方。

#### <a name="from-api"></a>通过 API

- PowerShell - [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [按订阅列出](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>禁用规则时的活动日志示例

如果查询连续七天失败，Azure Monitor 会禁用日志警报并停止规则的计费。 在 [Azure 活动日志](../../azure-monitor/essentials/activity-log.md)中可以看到 Azure Monitor 禁用日志警报的确切时间。 

请参阅此示例：

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
