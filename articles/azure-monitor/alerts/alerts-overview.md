---
title: Azure 中的警报和通知监视概述
description: Azure Monitor 中的警报概述
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: ee0d6cd4c895bbcd78767776a86bd63cfa4f5242
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747716"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警报概述 

本文介绍什么是警报及其优点，以及如何开始使用警报。  

## <a name="what-are-alerts-in-microsoft-azure"></a>什么是 Microsoft Azure 中的警报？

在使用 Azure Monitor 中的监视数据发现基础结构或应用程序方面的问题时，警报会主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 

## <a name="overview"></a>概述

下图表示了警报流。 

![警报流示意图](media/alerts-overview/Azure-Monitor-Alerts.svg)

警报规则独立于警报，也独立于警报触发时采取的操作。 警报规则捕获警报的目标和条件。 警报规则可以是“已启用”或“已禁用”状态。 警报只有在启用后才会触发。 

下面是警报规则的关键属性：

**目标资源** - 定义适用于警报的范围和信号。 目标可以是任何 Azure 资源。 示例目标：

- 虚拟机。
- 存储帐户。
- Log Analytics 工作区。
- Application Insights。 

对于某些资源（例如虚拟机）来说，可以将多个资源指定为警报规则的目标。

信号 - 由目标资源发出。 信号可以是以下类型：指标、活动日志、Application Insights 和日志。

条件 - 应用于目标资源的信号和逻辑的组合。 示例: 

- CPU 百分比 > 70%
- 服务器响应时间 > 4 毫秒 
- 日志查询的结果计数 > 100

警报名称 - 用户配置的预警规则的具体名称。

警报说明 - 用户配置的预警规则的说明。

严重性 - 预警规则中指定的条件符合后确定的警报严重性。 严重性的范围为 0 到 4。

- 严重性为 0 = 严重
- 严重性为 1 = 错误
- 严重性为 2 = 警告
- 严重性为 3 = 信息
- 严重性为 4 = 详细 

**操作** - 触发警报时执行的特定操作。 有关详细信息，请参阅[操作组](../alerts/action-groups.md)。

## <a name="what-you-can-alert-on"></a>可以报警的内容

可以按照[监视数据源](./../agents/data-sources.md)中的说明，针对指标和日志发出警报。 信号包括但不限于：

- 指标值
- 日志搜索查询
- 活动日志事件
- 基础 Azure 平台的运行状况
- 网站可用性测试

## <a name="manage-alerts"></a>管理警报

可以设置警报状态来指定它在解决过程中所处的阶段。 符合警报规则中指定的条件以后，就会创建或触发警报，其状态为“新”。 可以在确认警报和关闭警报时更改状态。 所有状态更改都存储在警报历史记录中。

支持以下警报状态。

| 状态 | 说明 |
|:---|:---|
| 新建 | 检测到了问题，但尚未审查问题。 |
| 已确认 | 管理员已审查警报，并已开始进行处理。 |
| 已关闭 | 问题已解决。 关闭某个警报后，可通过将其更改为另一种状态来重新打开它。 |

*警报状态* 不同于且独立于 *监视条件*。 警报状态是由用户设置的。 监视条件是由系统设置的。 当触发警报时，警报的监视条件设置为“已触发”，当导致警报触发的基础条件解除后，监视条件会设置为“已解决” 。 

在用户更改警报状态之前，警报状态不会改变。 了解[如何更改警报和智能组的状态](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)。

## <a name="alerts-experience"></a>警报体验 
默认的“警报”页提供特定时间范围内创建的警报的摘要。 该页显示每种严重性的警报总数，列中会标识处于每种状态的、具有每种严重性的警报总数。 选择任一严重性可打开按该严重性筛选的“[所有警报](#all-alerts-page)”页。

你可以转而[使用 REST API 以编程方式枚举在订阅上生成的警报实例](#manage-your-alert-instances-programmatically)。

> [!NOTE]
   >  只能访问过去 30 天内生成的警报。

可以通过更改订阅或筛选器参数来更新页面。

![“警报”页的屏幕截图](media/alerts-overview/alerts-page.png)

可以通过选择页面顶部的下拉菜单中的值，来对此视图进行筛选。

| 列 | 说明 |
|:---|:---|
| 订阅 | 选择要查看其警报的 Azure 订阅。 （可选）可以选择你的所有订阅。 视图中仅包含你在所选订阅中有权访问的警报。 |
| 资源组 | 选择单个资源组。 只有包含选定资源组中的目标的警报才会包含在视图中。 |
| 时间范围 | 只有在选定时间范围内触发的警报才会包含在该视图中。 支持的值为过去 1 小时、过去 24 小时、过去 7 天和过去 30 天。 |

选择“警报”页面顶部的以下值打开另一个页面：

| Value | 说明 |
|:---|:---|
| 警报总数 | 符合选定条件的警报总数。 选择此值会打开未经筛选的“所有警报”视图。 |
| 智能组 | 从符合选定条件的警报创建的智能组总数。 选择此值会在“所有警报”视图中打开智能组列表。
| 警报规则总数 | 选定订阅和资源组中的警报规则总数。 选择此值会打开根据选定订阅和资源组筛选的“规则”视图。


## <a name="manage-alert-rules"></a>管理警报规则
若要显示“规则”页，请选择“管理警报规则”。  “规则”页是用于跨 Azure 订阅管理所有警报规则的一个地方。 此页列出所有警报规则，这些规则可以根据目标资源、资源组、规则名称或状态排序。 还可以在此页中编辑、启用或禁用警报规则。  

 ![“规则”页的屏幕截图](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>创建警报规则
可以通过一致的方式创作预警规则，而不管监视服务或信号类型是什么。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
下面介绍如何创建新警报规则：
1. 选取警报的目标。
1. 从目标的可用信号中选择信号。
1. 指定要应用到信号中数据的逻辑。

这个创作过程经过了简化，用户在选择 Azure 资源之前，不再需要知道受支持的监视源或信号。 可用信号列表会根据你选择的目标资源自动筛选。 另外，还将根据该目标引导你自动定义预警规则的逻辑。  

可以在[使用 Azure Monitor 创建、查看和管理警报](../alerts/alerts-metric.md)中详细了解如何创建警报规则。

警报可在多个 Azure 监视服务中使用。 有关如何以及何时使用其中每种服务的信息，请参阅[监视 Azure 应用程序和资源](../overview.md)。 


## <a name="all-alerts-page"></a>“所有警报”页 
若要查看“所有警报”页，请选择“警报总数”。  在这里，可以查看在选定时间内创建的警报列表。 可以查看各个警报的列表，或包含这些警报的智能组列表。 选择页面顶部的标题可在视图之间进行切换。

![“所有警报”页的屏幕截图](media/alerts-overview/all-alerts-page.png)

可以选择页面顶部下拉菜单中的以下值，对该视图进行筛选：

| 列 | 说明 |
|:---|:---|
| 订阅 | 选择要查看其警报的 Azure 订阅。 （可选）可以选择你的所有订阅。 视图中仅包含你在所选订阅中有权访问的警报。 |
| 资源组 | 选择单个资源组。 只有包含选定资源组中的目标的警报才会包含在视图中。 |
| 资源类型 | 选择一个或多个资源类型。 只有包含选定类型中的目标的警报才会包含在视图中。 仅在指定资源组后，才显示此列。 |
| 资源 | 选择资源。 只有包含该资源（作为目标）的警报才会包含在视图中。 仅在指定资源类型后，才显示此列。 |
| severity | 选择警报严重性，或选择“所有”以包含所有严重性的警报。 |
| 监视条件 | 选择一个监视条件，或选择“所有”以包括所有条件的警报。 |
| 警报状态 | 选择一个警报状态，或选择“所有”以包括所有状态的警报。 |
| 监视服务 | 选择一个服务，或选择“所有”以包含所有服务。 只会包含使用该服务（作为目标）的规则创建的警报。 |
| 时间范围 | 只有在选定时间范围内触发的警报才会包含在该视图中。 支持的值为过去 1 小时、过去 24 小时、过去 7 天和过去 30 天。 |

选择页面顶部的“列”即可选择要显示的列。 

## <a name="alert-details-page"></a>“警报详细信息”页
当你选择某个警报时，此页会提供该警报的详细信息，并允许你更改其状态。

![“警报详细信息”页的屏幕截图](media/alerts-overview/alert-detail2.png)

“警报详细信息”页包括以下部分：

| 部分 | 说明 |
|:---|:---|
| 摘要 | 显示警报的属性和其他重要信息。 |
| 历史记录 | 列出警报执行的每个操作，以及对警报进行的任何更改。 目前仅限状态更改。 |
| 诊断 | 有关包含警报的智能组的信息。 “警报计数”表示包含在智能组中的警报数量。 包括同一智能组中在过去 30 天内创建的其他警报，无论警报列表页面中的时间筛选器是什么。 选择某个警报以查看其详细信息。 |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>适用于警报实例的 Azure 基于角色的访问控制 (Azure RBAC)

使用和管理警报实例需要用户具有[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)或[监视读取者](../../role-based-access-control/built-in-roles.md#monitoring-reader)的 Azure 内置角色。 在任何 Azure 资源管理器范围（从订阅级别到资源级别的粒度分配）内都支持这些角色。 例如，如果用户只具有虚拟机 `ContosoVM1` 的“监视参与者”访问权限，则该用户只能使用和管理 `ContosoVM1` 上生成的警报。

## <a name="manage-your-alert-instances-programmatically"></a>以编程方式管理警报实例

你可能希望以编程方式查询针对订阅生成的警报。 查询可以是在 Azure 门户之外创建自定义视图，也可以是分析警报以确定模式和趋势。

建议你通过 `AlertsManagementResources` 架构使用 [Azure Resource Graph](../../governance/resource-graph/overview.md) 来查询触发的警报。 如果必须跨多个订阅管理生成的警报，建议使用 Resource Graph。

以下对 Resource Graph REST API 的示例请求返回一个订阅中最后一天的警报：

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "alertsmanagementresources | where properties.essentials.lastModifiedDateTime > ago(1d) | project alertInstanceId = id, parentRuleId = tolower(tostring(properties['essentials']['alertRule'])), sourceId = properties['essentials']['sourceCreatedId'], alertName = name, severity = properties.essentials.severity, status = properties.essentials.monitorCondition, state = properties.essentials.alertState, affectedResource = properties.essentials.targetResourceName, monitorService = properties.essentials.monitorService, signalType = properties.essentials.signalType, firedTime = properties['essentials']['startDateTime'], lastModifiedDate = properties.essentials.lastModifiedDateTime, lastModifiedBy = properties.essentials.lastModifiedUserName"
}
```

也可以使用 Azure Resource Graph 浏览器在门户中查看此 Resource Graph 查询的结果：[portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/alertsmanagementresources%0A%7C%20where%20properties.essentials.lastModifiedDateTime%20%3E%20ago(1d)%0A%7C%20project%20alertInstanceId%20%3D%20id%2C%20parentRuleId%20%3D%20tolower(tostring(properties%5B 'essentials'%5D%5B'alertRule'%5D))%2C%20sourceId%20%3D%20properties%5B'essentials'%5D%5B'sourceCreatedId'%5D%2C%20alertName%20%3D%20name%2C%20severity%20%3D%20properties.essentials.severity%2C%20status%20%3D%20properties.essentials.monitorCondition%2C%20state%20%3D%20properties.essentials.alertState%2C%20affectedResource%20%3D%20properties.essentials.targetResourceName%2C%20monitorService%20%3D%20properties.essentials.monitorService%2C%20signalType%20%3D%20properties.essentials.signalType%2C%20firedTime%20%3D%20properties%5B'essentials'%5D%5B'startDateTime'%5D%2C%20lastModifiedDate%20%3D%20properties.essentials.lastModifiedDateTime%2C%20lastModifiedBy%20%3D%20properties.essentials.lastModifiedUserName)

如果查询场景的规模较小，或者要更新触发的警报，也可以使用[警报管理 REST API](/rest/api/monitor/alertsmanagement/alerts)。

## <a name="smart-groups"></a>智能组

智能组是根据机器学习算法对警报进行的聚合。这些算法有助于降低警报噪音，对故障排除也有帮助。 [详细了解智能组](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)和[如何管理智能组](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

- [详细了解智能组](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [了解操作组](../alerts/action-groups.md)
- [在 Azure 中管理警报实例](./alerts-managing-alert-instances.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Managing Smart Groups](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)（管理智能组）
- [详细了解 Azure 警报定价](https://azure.microsoft.com/pricing/details/monitor/)
