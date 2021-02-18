---
title: 用于 VM 的 Azure Monitor 来宾运行状况警报（预览）
description: 描述用于 VM 的 Azure Monitor 来宾健康状况创建的警报，包括如何启用和配置通知。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 30025f387768aaf1e4d642292c21d5b15ccc7451
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608835"
---
# <a name="azure-monitor-for-vms-guest-health-alerts-preview"></a>用于 VM 的 Azure Monitor 来宾运行状况警报（预览）
用于 VM 的 Azure Monitor 来宾健康状况允许你查看按固定时间间隔采样的一组性能度量定义的虚拟机的运行状况。 当虚拟机或监视器更改为不正常状态时，可以创建警报。 你可以使用 [Azure Monitor 中的警报规则创建](../platform/alerts-overview.md) 的警报来查看和管理这些警报，并选择在创建新警报时主动收到通知。

## <a name="configure-alerts"></a>配置警报
此功能处于预览状态时，不能为用于 VM 的 Azure Monitor 来宾健康状况创建明确的警报规则。 默认情况下，将为每个虚拟机创建警报，但不会为每个监视器创建警报。  这意味着，如果监视器更改为不影响虚拟机当前状态的状态，则不会创建任何警报，因为虚拟机状态不会更改。 

你可以通过 Azure 门户中虚拟机配置的 " **警报状态** " 设置，为虚拟机上的特定虚拟机或特定监视器禁用警报。 有关在 Azure 门户中配置监视器的详细信息，请参阅 [用于 VM 的 Azure Monitor 来宾健康状况 () 预览版中的配置监视 ](vminsights-health-configure.md) 。 有关在一组虚拟机中配置监视器的详细信息，请参阅 [使用数据收集规则在用于 VM 的 Azure Monitor 来宾健康状况中配置监视 (预览版) ](vminsights-health-configure-dcr.md) 。

## <a name="alert-severity"></a>警报严重性
来宾运行状况创建的警报的严重性直接映射到触发警报的虚拟机或监视器的严重性。

| 监视状态 | 警报严重性 |
|:---|:---|
| 严重 | Sev1 |
| 警告  | Sev2 |
| 正常  | Sev4 |

## <a name="alert-lifecycle"></a>警报生命周期
每当每个虚拟机更改为 **警告** 或 **严重** 状态时，将为每个虚拟机创建一个 [Azure 警报](../platform/alerts-overview.md)。 从 " **Azure Monitor** " 菜单中的 **警报** 或 Azure 门户中的虚拟机菜单中查看警报。

如果在虚拟机状态发生更改时警报已处于已 **激发** 状态，则不会创建第二个警报，但会更改相同警报的严重性，使其与虚拟机的状态相匹配。 例如，如果在已 **激发****警告** 警报时虚拟机的状态更改为 "**严重**"，则该警报的严重性将更改为 **Sev1**。 如果在已 **激发** **Sev1** 警报时虚拟机的状态更改为 **警告** 状态，则该警报的严重性将变为 **Sev2**。 如果虚拟机恢复 **正常** 状态，则会解决警报，并将严重性更改为 **Sev4**。

## <a name="viewing-alerts"></a>查看警报
查看用于 VM 的 Azure Monitor 来宾健康状况创建的警报，以及 [Azure 门户中](../platform/alerts-overview.md#alerts-experience)的其他警报。 你可以从 " **Azure Monitor** " 菜单中选择 "**警报**" 以查看所有受监视资源的警报，或从虚拟机的菜单中选择 "**警报**"，以仅查看该虚拟机的警报。

## <a name="alert-properties"></a>警报属性

### <a name="properties-in-the-azure-portal"></a>Azure 门户中的属性
下表描述了在 Azure 门户中查看警报的属性。

| 属性 | 说明 |
|:---|:---|
| 创建警报之前的监视器状态 | 第一次触发此警报之前监视器或虚拟机的状态。 |
| 创建警报时的监视器状态 | 第一次触发警报时监视器或虚拟机的状态。 这是导致触发警报的状态。 |
| 了解有关在创建警报时的状态转换的详细信息 | "VM 运行状况" 页的链接，你可以在其中查看完全状态转换。 此状态转换表示当监视器第一次从 **正常** 状态变为不正常状态时的实例。 |

例如，在时，监视器的 **状态从正常** 变为 **严重** ，而 **Sev1** 则会触发新警报。 然后，在时间 t1 从 **严重** 到 **警告** ，警报严重性将更新为 **Sev2**。 它在时间 t2 处于 **正常状态** ，并且会解决警报。

警报属性将在整个序列中包含这些值。

- 创建警报之前的监视器状态：正常
- 创建警报时的监视器状态：严重
- 若要在创建警报时了解有关状态转换的详细信息，请参阅 time t0 处发生的状态转换导航链接。


### <a name="properties-in-notifications"></a>通知中的属性
下表描述了通知中包含的警报的属性。

| 属性 | 说明 |
|:---|:---|
| 以前的监视器状态 | 监视器或虚拟机状态，然后再更改状态。 如果警报严重性更新触发了此通知，则此属性表示在更新严重性之前的状态。 |
| 当前监视器状态 | 监视器或虚拟机状态更改的状态。 如果警报严重性更新触发了此通知，则此属性表示新状态。 |
| 了解有关此状态转换的详细信息 | "VM 运行状况" 页的链接，你可以在其中查看完全状态转换。 此状态转换表示监视触发此通知的状态发生更改时的实例。 |

使用上面的示例，每次通知都将具有以下属性。

在时间 t0 收到的通知
- 以前的监视器状态：正常
- 当前监视器状态：严重
- 若要了解有关此状态转换的详细信息，请参阅 time t0 处发生的状态转换导航链接。

在时间 t1 收到的通知
- 以前的监视器状态：严重
- 当前监视器状态：警告
- 若要了解有关此状态转换的详细信息，请参阅在时间 t1 发生的状态转换导航链接。

在时间 t2 收到的通知
- 以前的监视器状态：警告
- 当前监视器状态：正常
- 若要了解有关此状态转换的详细信息：在时间 t2 发生指向状态转换的导航链接。

## <a name="configure-notifications"></a>配置通知
若要主动收到来宾健康状况触发的警报的通知，请创建 [操作组](../alerts/action-groups.md) 以定义要执行的不同操作，如发送 SMS 消息或启动逻辑应用。 然后创建一个 [操作规则](../alerts/alerts-action-rules.md) ，该规则指定监视器和虚拟机的作用域，并使用该操作组。

在 Azure 门户的 " **监视** " 菜单中，选择 " **警报**"。  选择 " **管理操作** "，然后选择 " **操作规则" (预览 ")**。 

![新建操作规则](media/vminsights-health-alerts/action-rule-new.png)

单击 " **新建操作规则** " 以创建新规则。 单击 "作用域" 旁边的 " **选择** "，并选择 "订阅"、"资源组" 或 "一个或多个特定虚拟机"。 仅对处于作用域内的虚拟机触发通知。

![操作规则范围](media/vminsights-health-alerts/action-rule-scope.png)

单击 "**筛选**" 旁边的 "**添加**"。 创建一个筛选器，其中的 **监视服务等于 VM Insights-运行状况**。 添加其他筛选器，以指定应触发通知的特定警报。 例如，你可以使用 **严重性** 来匹配与特定严重性匹配的所有监视器的警报。

![操作规则筛选器](media/vminsights-health-alerts/action-rule-filter.png)

在 "在 **此范围中定义**" 中，选择 " **操作组** "，然后选择要与监视器关联的操作组。 为规则命名，并选择应将其保存到其中的资源组。 单击 " **创建** " 以创建规则。

![操作规则](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>后续步骤

- [在用于 VM 的 Azure Monitor 和载入代理中启用来宾运行状况。](vminsights-health-enable.md)
- [使用 Azure 门户配置监视器。](vminsights-health-configure.md)
- [使用数据收集规则配置监视器。](vminsights-health-configure-dcr.md)