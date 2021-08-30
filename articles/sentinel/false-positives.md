---
title: 处理 Azure Sentinel 中的误报
description: 了解如何通过创建自动化规则或修改分析规则来指定例外，从而解决 Azure Sentinel 中的误报。
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 1b13d49707402143c59078aa3e73cd93db1b10de
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748078"
---
# <a name="handle-false-positives-in-azure-sentinel"></a>处理 Azure Sentinel 中的误报

[Azure Sentinel 分析规则](detect-threats-built-in.md)会在你的网络中出现可疑情况时通知你。 没有分析规则是完美的，总归会有一些误报需要你来处理。 本文介绍如何通过使用自动化或修改计划的分析规则来处理误报。

## <a name="false-positive-causes-and-prevention"></a>误报原因及预防

即使是在一个正确构建的分析规则中，误报也经常源自用户或 IP 地址等特定实体，这些实体应该被排除在规则之外。

常见方案包括：

- 某些用户（通常是服务主体）的正常活动显示出一种看似可疑的模式。
- 来自已知 IP 地址的有意安全扫描活动被检测为恶意活动。
- 排除专用 IP 地址的规则还应排除一些非专用的内部 IP 地址。

本文介绍两种避免误报的方法：

- 自动化规则，无需修改分析规则即可创建例外。
- 计划的分析规则修改，允许更详细和永久的例外。

下表介绍了每种方法的特征：


|方法|特征|
|-|-|
|自动化规则|<ul><li>可应用于多个分析规则。</li><li>保留审核线索。 例外会阻止事件创建，但仍会记录警报以供审核。</li><li>通常由分析人员生成。</li><li>允许在有限的时间内应用例外。 例如，维护工作可能会触发误报，认为超出了维护时间范围就是真实事件。</li></ul>|
|分析规则修改|<ul><li>允许高级布尔表达式和基于子网的例外。</li><li>允许使用监视列表实现集中例外管理。</li><li>通常需要由安全运营中心 (SOC) 工程师实施。</li><li>是最灵活和最完整的误报解决方案，但更为复杂。</li></ul>|

## <a name="add-exceptions-by-using-automation-rules"></a>使用自动化规则添加例外

添加例外的最简单方法是在看到误报事件时[添加自动化规则](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules)。

添加自动化规则以处理误报：

1. 在 Azure Sentinel 的“事件”下，选择要为其创建例外的事件。
1. 选择“创建自动化规则”。
1. 在“创建新的自动化规则”边栏中，可以选择修改新规则名称以标识例外，而不仅仅是警报规则名称。
1. 在“条件”下，可选择添加更多要应用例外的“分析规则名称” 。
1. 边栏显示了当前事件中可能导致误报的特定实体。 保留自动建议，或修改它们以微调例外。 例如，可以更改 IP 地址上的一个条件，以便应用于整个子网。

   :::image type="content" source="media/false-positives/create-rule.png" alt-text="显示如何在 Azure Sentinel 中为事件创建自动化规则的屏幕截图。":::

1. 定义触发器后，可以继续定义规则可执行的操作：

   :::image type="content" source="media/false-positives/apply-rule.png" alt-text="显示如何在 Azure Sentinel 中完成创建和应用自动化规则的屏幕截图。":::

   - 该规则已配置为关闭符合例外条件的事件。
   - 可以向解释例外的自动关闭事件添加注释。 例如，可以指定事件源自已知的管理活动。
   - 默认情况下，规则设置为在 24 小时后自动过期。 这可能是你需要的过期时间，并减少了误报错误的可能性。 如果想要时间更长的例外，请将“规则过期时间”设置为更晚的时间。

1. 选择“应用”以激活例外。

> [!TIP]
> 还可以从头开始创建自动化规则，而无需从事件开始。 从 Azure Sentinel 左侧导航菜单中选择“自动化”，然后选择“创建” > “添加新规则”  。

## <a name="add-exceptions-by-modifying-analytics-rules"></a>通过修改分析规则来添加例外

实现例外的另一个选项是修改分析规则查询。 你可以直接在规则中包含例外，或者在可能的情况下，最好使用对[监视列表](watchlists.md#use-watchlists-in-analytics-rules)的引用。 然后便可以管理监视列表中的例外列表。

### <a name="modify-the-query"></a>修改查询

要编辑现有分析规则，请从 Azure Sentinel 左侧导航菜单中选择“自动化”。 选择要编辑的规则，然后选择右下方的“编辑”以打开“分析规则向导” 。

有关使用“分析规则向导”创建和编辑分析规则的详细说明，请参阅[创建自定义分析规则以检测威胁](detect-threats-custom.md)。

要在典型的规则报头中实现例外，可以在规则查询的开头附近添加一个类似 `where IPAddress !in ('<ip addresses>')` 的条件。 此行从规则中排除特定的 IP 地址。

```kusto
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in ('10.0.0.8', '192.168.12.1')
...
```

这种类型的例外并不限于 IP 地址。 可以使用 `UserPrincipalName` 字段排除特定用户，或使用 `AppDisplayName` 排除特定应用。

还可以排除多个属性。 例如，要排除来自 IP 地址 `10.0.0.8` 或用户 `user@microsoft.com` 的警报，请使用：

```kusto
| where IPAddress !in ('10.0.0.8')
| where UserPrincipalName != 'user@microsoft.com'
```

若要在适用时实现更细粒度的例外，并减少误报的机会，可以组合属性。 仅当两个值出现在同一警报中时，以下例外才适用：

```kusto
| where IPAddress != '10.0.0.8' and UserPrincipalName != 'user@microsoft.com'
```

### <a name="exclude-subnets"></a>排除子网

要排除组织使用的 IP 范围，需要排除子网。 以下示例显示如何排除子网。

`ipv4_lookup` 运算符是扩充运算符，而不是筛选运算符。 `where isempty(network)` 行实际上通过检查那些未显示匹配项的事件来进行筛选。

```kusto
let subnets = datatable(network:string) [ "111.68.128.0/17", "5.8.0.0/19", ...];
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| evaluate ipv4_lookup(subnets, IPAddress, network, return_unmatched = true)
| where isempty(network)
...
```

### <a name="use-watchlists-to-manage-exceptions"></a>使用监视列表管理例外

可以使用监视列表来管理规则本身之外的例外列表。 如果适用，此解决方案具有以下优点：

- 分析人员可以在不编辑规则的情况下添加例外，这更符合 SOC 最佳做法。
- 同一个监视列表可以应用于多个规则，从而实现集中例外管理。

使用监视列表类似于使用直接例外。 使用 `_GetWatchlist('<watchlist name>')` 调用监视列表：

```kusto
let timeFrame = 1d;
let logonDiff = 10m;
let allowlist = (_GetWatchlist('ipallowlist') | project IPAddress);
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in (allowlist)
...
```

还可使用监视列表进行子网筛选。 例如，在前面的子网排除代码中，可以将子网 `datatable` 定义替换为监视列表：

```kusto
let subnets = _GetWatchlist('subnetallowlist');
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：
- [使用 UEBA 数据分析假正](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [在 Azure Sentinel 中使用自动化规则自动处理事件](automate-incident-handling-with-automation-rules.md)
- [创建自定义分析规则以检测威胁](detect-threats-custom.md)
- [使用 Azure Sentinel 播放列表](watchlists.md)
