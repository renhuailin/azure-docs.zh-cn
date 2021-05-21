---
title: 升级到当前 Azure Monitor 日志警报 API
description: 了解如何切换到日志警报 ScheduledQueryRules API
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b91560f09b30cc5d3089e5beca4d670f94f33402
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031304"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>从旧版 Log Analytics 警报 API 升级到当前日志警报 API

> [!NOTE]
> 本文仅与 Azure 公用相关（与 Azure 政府或Azure 中国云无关）。

> [!NOTE]
> 用户选择将首选项切换为当前 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) 后，就无法还原回较旧的[旧版 Log Analytics 警报 API](./api-alerts.md)。

过去，用户使用[旧版 Log Analytics 警报 API](./api-alerts.md) 来管理日志警报规则。 当前工作区使用 [ScheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)。 本文介绍从旧版 API 切换到当前 API 的好处和过程。

## <a name="benefits"></a>好处

- 用于创建警报规则的单个模板（以前需要三个单独的模板）。
- 同时适用于 Log Analytics 工作区或 Application Insights 资源的单个 API。
- [PowerShell cmdlet 支持](./alerts-log.md#managing-log-alerts-using-powershell)。
- 严重性与所有其他警报类型的对齐。
- 能够创建跨多个外部资源（例如 Log Analytics 工作区或 Application Insights 资源）的[跨工作区日志警报](../logs/cross-workspace-query.md)。
- 用户可以使用“Aggregate On”参数指定用于拆分警报的维度。
- 日志警报数据期限延长至两天（以前限制为一天）。

## <a name="impact"></a>影响

- 必须使用当前 API 创建/编辑所有新规则。 请参阅[通过 Azure 资源模板的示例用法](alerts-log-create-templates.md)和[通过 PowerShell 的示例用法](./alerts-log.md#managing-log-alerts-using-powershell)。
- 随着规则成为当前 API 中 Azure 资源管理器跟踪的资源，并且必须是唯一的，规则资源 ID 将更改为以下结构：`<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`。 警报规则的显示名称将保持不变。

## <a name="process"></a>进程

在大多数情况下，切换过程不是交互式的，不需要手动步骤。 在切换期间或之后，警报规则不会停止或停滞。
执行以下调用切换与特定 Log Analytics 工作区关联的所有警报规则：

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

使用包含以下 JSON 的请求正文：

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

下面是使用 [ARMClient](https://github.com/projectkudu/ARMClient)（一种开源命令行工具）的示例，它简化了调用上述 API 调用的过程：

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

如果切换成功，则响应为：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>检查工作区的切换状态

你还可以使用此 API 调用来检查切换状态：

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

也可以使用 [ARMClient](https://github.com/projectkudu/ARMClient) 工具：

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果 Log Analytics 工作区已切换为 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)，则响应为：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
如果 Log Analytics 工作区未切换，则响应为：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Monitor - 日志警报](./alerts-unified-log.md)。
- 了解如何[使用 API 管理日志警报](alerts-log-create-templates.md)。
- 了解如何[使用 PowerShell 管理日志警报](./alerts-log.md#managing-log-alerts-using-powershell)。
- 详细了解 [Azure 警报体验](./alerts-overview.md)。