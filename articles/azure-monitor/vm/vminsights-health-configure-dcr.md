---
title: '使用数据收集规则在 VM insights 来宾运行状况中配置监视 (预览) '
description: 介绍如何使用资源管理器模板在大规模中修改 VM insights 来宾运行状况中的默认监视。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 907aea16b018fb5dd3846db546787d132f8f5a9f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731215"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>使用数据收集规则在 VM insights 来宾运行状况中配置监视 (预览) 
[VM insights 来宾运行状况](vminsights-health-overview.md) 允许你查看按固定时间间隔采样的一组性能度量定义的虚拟机的运行状况。 本文介绍如何使用数据收集规则来修改多个虚拟机上的默认监视。


## <a name="monitors"></a>监视器
虚拟机的运行状况状态由其每个监视器的 [运行状况汇总](vminsights-health-overview.md#health-rollup-policy) 决定。 VM insights 来宾运行状况中有两种类型的监视器，如下表所示。

| 监视 | 说明 |
|:---|:---|
| 单元监视器 | 度量资源或应用程序的某个方面。 可能是检查性能计数器以确定资源的性能或其可用性。 |
| 聚合监视器 | 将多个监视器组合在一起，以提供单个聚合运行状况状态。 聚合监视器可以包含一个或多个单元监视器和其他聚合监视器。 |

不能直接更改 VM insights 来宾运行状况和其配置所使用的监视器集。 你可以创建 [替代](#overrides) ，但会修改默认配置的行为。 重写在数据收集规则中定义。 可以创建多个数据收集规则，每个规则包含多个替代以实现所需的监视配置。

## <a name="monitor-properties"></a>监视器属性
下表描述了可在每个监视器上配置的属性。

| 属性 | 监视器 | 说明 |
|:---|:---|:---|
| 已启用 | 聚合<br>计价单位 | 如果为 true，则计算状态监视器并对虚拟机的运行状况进行贡献。 这会触发启用警报警报。 |
| 警报 | 聚合<br>计价单位 | 如果为 true，则当监视器进入不正常状态时，会触发警报。 如果为 false，则监视器的状态仍会影响虚拟机的运行状况，这可能会触发警报。 |
| 警告 | 计价单位 | 警告状态的条件。 如果没有，则监视器将永远不会进入警告状态。 |
| 严重 | 计价单位 | 严重状态的条件。 如果没有，则监视器将从不进入严重状态。 |
| 评估频率 | 计价单位 | 计算运行状况状态的频率。 |
| Lookback | 计价单位 | Lookback 窗口的大小（以秒为单位）。 有关详细说明，请参阅 [monitorConfiguration 元素](#monitorconfiguration-element) 。 |
| 评估类型 | 计价单位 | 定义要从示例集使用的值。 有关详细说明，请参阅 [monitorConfiguration 元素](#monitorconfiguration-element) 。 |
| 最小示例 | 计价单位 | 用于计算值的最小值数。 |
| 最大示例 | 计价单位 | 用于计算值的最大值数。 |


## <a name="default-configuration"></a>默认配置
下表列出了每个监视器的默认配置。 此默认配置无法直接更改，但你可以定义将修改某些虚拟机的监视配置的 [替代](#overrides) 。


| 监视 | 已启用 | 警报 | 警告 | 严重 | 评估频率 | Lookback | 计算类型 | 最小示例 | 最大示例 |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU 使用率  | True | False | None | \> 90%    | 60 秒 | 240秒 | Min | 2 | 3 |
| 可用内存 | True | False | None | \< 100 MB | 60 秒 | 240秒 | Max | 2 | 3 |
| 文件系统      | True | False | None | \< 100 MB | 60 秒 | 120秒 | Max | 1 | 1 |


## <a name="overrides"></a>替代
*重写* 将更改监视器的一个或多个属性。 例如，重写可以禁用默认情况下启用的监视器、定义监视器的警告标准或修改监视器的严重阈值。 

重写在 [数据收集规则 (DCR) ](../agents/data-collection-rule-overview.md)中定义。 可以使用不同的替代集创建多个 Dcr，并将其应用于多个虚拟机。 按照为 [Azure Monitor 代理 (预览版) 配置数据收集 ](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations)中所述，通过创建关联来将 DCR 应用到虚拟机。


## <a name="multiple-overrides"></a>多次替代

单个监视器可能具有多个替代。 如果重写定义了不同的属性，则生成的配置是所有替代的组合。

例如， `memory|available` 默认情况下，监视器不指定警告阈值或启用警报。 考虑将以下替代应用于此监视器：

- 重写1将 `alertConfiguration.isEnabled` 属性值定义为 `true`
- 重写2使用 `monitorConfiguration.warningCondition` 的阈值条件定义 `< 250` 。

如果内存不足，则生成的配置将为 "警告" 运行状况状态，并在小于 100 Mb 的可用内存可用时进入 "严重运行状况状态"，如果已存在) ，则创建警报严重性 1 (或将现有警报从严重性2更改为 "1"。

如果两个替代在同一监视器上定义相同的属性，一个值将优先。 将根据其作用域（从最一般到最具体的 [作用域](#scopes-element)）应用替代。 这意味着，最具体的替代将最大限度地应用。 具体顺序如下所示：

1. 全球 
2. 订阅
3. 资源组
4. 虚拟机。 

如果同一作用域级别的多个替代在同一监视器上定义相同的属性，则它们将按照它们在 DCR 中的显示顺序进行应用。 如果替代在不同的 Dcr 中，则按 DCR 资源 Id 的字母顺序应用替代。


## <a name="data-collection-rule-configuration"></a>数据收集规则配置
以下部分介绍了定义替代的数据收集规则中的 JSON 元素。 [示例数据收集规则](#sample-data-collection-rule)中提供了一个完整的示例。

## <a name="extensions-structure"></a>扩展结构
来宾运行状况作为 Azure Monitor 代理的扩展实现，因此重写是在 `extensions` 数据收集规则的元素中定义的。 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| 元素 | 必选 | 说明 |
|:---|:---|:---|
| `name` | 是 | 扩展的用户定义字符串。 |
| `streams` | 是 | 来宾运行状况数据将发送到的流的列表。 这必须包括 **HealthStateChange**。  |
| `extensionName` | 是 | 扩展的名称。 这必须是 **HealthExtension**。 |
| `extensionSettings` | 是 | `healthRuleOverride`要应用于默认配置的元素数组。 |


## <a name="extensionsettings-element"></a>extensionSettings 元素
包含扩展的设置。

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| 元素 | 必选 | 说明 |
|:---|:---|:---|
| `schemaVersion` | 是 | 由 Microsoft 定义的字符串，用于表示元素的预期架构。 当前必须设置为1。0 |
| `contentVersion` | 否 | 用户定义的字符串，用于跟踪不同版本的运行状况配置（如果需要）。 |
| `healthRuleOverrides` | 是 | `healthRuleOverride`要应用于默认配置的元素数组。 |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides 元素
包含一个或多个 `healthRuleOverride` 元素，每个元素都定义一个替代。

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| 元素 | 必选 | 说明 |
|:---|:---|:---|
| `scopes` | 是 | 指定此替代适用的虚拟机的一个或多个作用域的列表。 尽管 DCR 与虚拟机关联，但虚拟机必须在作用域内，才能应用替代。 |
| `monitors` | 是 | 定义将接收此重写的监视器的一个或多个字符串的列表。  |
| `monitorConfiguration` | 否 | 监视器的配置，包括运行状况状态及其计算方式。 |
| `alertConfiguration` | 否 | 监视器的警报配置。 |
| `isEnabled` | 否 | 控制是否启用监视。 禁用的监视器切换到 *禁用的特殊运行* 状况状态和状态，除非重新启用。 如果省略，则监视器将从层次结构中的父监视器继承其状态。 |


## <a name="scopes-element"></a>scope 元素
每个替代都具有一个或多个作用域，用于定义应将重写应用到的虚拟机。 作用域可以是订阅、资源组或单个虚拟机。 即使替代位于与特定虚拟机关联的 DCR 中，如果虚拟机在替代范围之一内，则它仅适用于该虚拟机。 这样一来，你就可以将更少数量的 Dcr 与一组 Vm 建立关联，但可以精确控制 DCR 内每个替代的分配。 你可能想要创建一组小型 Dcr，并使用策略将它们关联到一组虚拟机，同时使用 scope 元素为这些虚拟机的不同子集指定运行状况监视器替代。

下表显示了不同范围的示例。

| 范围 | 示例 |
|:---|:---|
| 单个虚拟机 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| 资源组中的所有虚拟机 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| 订阅中的所有虚拟机 | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| 数据收集规则关联的所有虚拟机 | `*` |


## <a name="monitors-element"></a>监视器元素
定义运行状况层次结构中的监视器将接收此重写的一个或多个字符串的列表。 每个元素可以是与将接收此重写的一个或多个监视器匹配的监视器名称或类型名称。 

```json
"monitors": [
    "<monitor name>"
 ],
```



下表列出了当前可用的监视器名称。

| 类型名称 | 名称 | 描述 |
|:---|:---|:---|
| 根 | root | 表示虚拟机运行状况的顶层监视器。 | |
| cpu 利用率 | cpu 利用率 | CPU 使用率监视器。 | |
| 逻辑磁盘 | 逻辑磁盘 | 对 Windows 虚拟机上所有监视磁盘的运行状况状态的聚合监视器。 | |
| 逻辑磁盘\|* | 逻辑磁盘 \| C：<br>逻辑磁盘 \| D： | Windows 虚拟机上给定磁盘的聚合监视器跟踪运行状况。 | 
| 逻辑磁盘 \| * \| 可用空间 | 逻辑磁盘 \| C： \| 可用空间<br>逻辑磁盘 \| D： \| 可用空间 | Windows 虚拟机上的磁盘可用空间监视器。 |
| filesystems | filesystems | 用于监视 Linux 虚拟机上所有文件系统的运行状况的聚合监视器。 |
| 文件系统\|* | 文件系统\|/<br>文件系统 \| /mnt | Linux 虚拟机的文件系统的聚合监视器跟踪运行状况。 | filesystems|/var/log |
| 文件系统 \| * \| 可用空间 | 文件系统 \| / \| 可用空间<br>文件系统 \| /mnt \| 可用空间 | Linux 虚拟机文件系统上的磁盘可用空间监视器。 | 
| 内存 | 内存 | 虚拟机内存运行状况的聚合监视器。 | |
| \|可用内存| \|可用内存 | 监视器跟踪虚拟机上的可用内存。 | |


## <a name="alertconfiguration-element"></a>alertConfiguration 元素
指定是否应该从监视器创建警报。

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| 元素 | 必需 | 说明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 如果设置为 true，则当切换到 "严重" 或 "警告" 状态并在返回到正常状态时解决警报时，监视器将生成警报。 如果为 false 或省略，则不生成警报。  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration 元素
仅适用于单元监视器。 定义监视器的配置，包括运行状况状态及其计算方式。

参数定义算法以计算要与阈值进行比较的指标值。 该监视器在计算时间和之前评估了在窗口内接收到的多个指标样本，而不是对基础指标中的一个示例进行操作 `lookbackSec` 。 将考虑在该时间范围内接收到的所有示例，如果样本计数大于 `maxSamples` ，则忽略以上较早的示例 `maxSamples` 。 

如果 lookback 时间间隔中的样本数少于 `minSamples` ，则监视器将切换到 " *未知* " 运行状况状态，指示没有足够的数据来做出有关基础指标的运行状况的明智决策。 如果可以使用更多的示例 `minSamples` ，由 evaluationType 参数指定的聚合函数将在该集上运行，以计算单个值。


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| 元素 | 必需 | 说明 | 
|:---|:---|:---|
| `evaluationFrequencySecs` | 否 | 定义运行状况评估的频率。 在代理启动时，将计算每个监视器，并按此参数之后的常规间隔进行计算。 |
| `lookbackSecs`   | 否 | Lookback 窗口的大小（以秒为单位）。 |
| `evaluationType` | 否 | `min` –从整个样本集中获取最小值<br>`max` -从整个示例集中获取最大值<br>`avg` –取样本集的平均值<br>`all` –将集内的每个单个值与阈值进行比较。 当且仅当设置满足阈值条件中的所有样本时，监视交换机状态。 |
| `minSamples`     | 否 | 用于计算值的最小值数。 |
| `maxSamples`     | 否 | 用于计算值的最大值数。 |
| `warningCondition`  | 否 | 警告条件的阈值和比较逻辑。 |
| `criticalCondition` | 否 | 临界条件的阈值和比较逻辑。 |


## <a name="warningcondition-element"></a>warningCondition 元素
定义警告条件的阈值和比较逻辑。 如果此元素不包括在内，则监视器将从不切换到警告运行状况状态。

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 属性 | 必需 | 说明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 指定是否启用条件。 如果设置为 **false**，则禁用条件，即使可以设置阈值和操作员属性。 |
| `threshold` | 否 | 定义用于比较计算值的阈值。 |
| `operator`  | 否 | 定义要在阈值表达式中使用的比较运算符。 可能的值： >、<、>=、<=、= =。 |


## <a name="criticalcondition-element"></a>criticalCondition 元素
为临界条件定义阈值和比较逻辑。 如果此元素不包括在内，则监视器将永远无法切换到关键运行状况状态。

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 属性 | 必需 | 说明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 指定是否启用条件。 如果设置为 **false**，则禁用条件，即使可以设置阈值和操作员属性。 |
| `threshold` | 否 | 定义用于比较计算值的阈值。 |
| `operator`  | 否 | 定义要在阈值表达式中使用的比较运算符。 可能的值： >、<、>=、<=、= =。 |

## <a name="sample-data-collection-rule"></a>示例数据收集规则
有关启用来宾监视的示例数据收集规则，请参阅 [使用资源管理器模板启用虚拟机](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template)。


## <a name="next-steps"></a>后续步骤

- 阅读有关 [数据收集规则](../agents/data-collection-rule-overview.md)的详细信息。