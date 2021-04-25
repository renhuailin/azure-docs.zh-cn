---
title: 使用数据收集规则在 VM 见解来宾运行状况中配置监视（预览）
description: 介绍如何使用资源管理器模板，在 VM 见解来宾运行状况中大规模修改默认监视。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 889a04d68de45a6270ae0c38615d841a526ad86a
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490686"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>使用数据收集规则在 VM 见解来宾运行状况中配置监视（预览）
利用 [VM 见解来宾运行状况](vminsights-health-overview.md)，可以查看虚拟机的运行状况，该运行状况是由一组定期取样的性能测量值定义。 本文介绍如何使用数据收集规则，修改多个虚拟机上的默认监视。


## <a name="monitors"></a>监视器
虚拟机的运行状况状态是由其每个监视器的[运行状况汇总](vminsights-health-overview.md#health-rollup-policy)决定。 在 VM 见解来宾运行状况中有两类监视器，如下表所示。

| 监视 | 说明 |
|:---|:---|
| 单元监视器 | 度量资源或应用程序的某个方面。 可能是检查性能计数器以确定资源的性能或其可用性。 |
| 聚合监视器 | 将多个监视器组合在一起，以提供单个聚合运行状况状态。 聚合监视器可以包含一个或多个单元监视器和其他聚合监视器。 |

无法直接更改 VM 见解来宾运行状况所使用的监视器集及其配置。 你可以创建[覆盖项](#overrides)，借此修改默认配置的行为。 覆盖项在数据收集规则中定义。 你可创建多条数据收集规则，每条规则都包含多个覆盖项，实现所需的监视配置。

## <a name="monitor-properties"></a>监视器属性
下表说明可以在每个监视器上配置的属性。

| 属性 | 监视器 | 说明 |
|:---|:---|:---|
| 已启用 | 聚合<br>计价单位 | 如果为 true，则会计算状态监视器并影响虚拟机的运行状况。 在警报已启用时，可以触发警报。 |
| 警报 | 聚合<br>计价单位 | 如果为 true，则监视器进入不正常状态时就会触发警报。 如果为 false，则该监视器的状态仍会影响虚拟机的运行状况，这可能会触发警报。 |
| 警告 | 计价单位 | 警告状态的条件。 如果没有条件，则该监视器永远不会进入警告状态。 |
| 严重 | 计价单位 | 严重状态的条件。 如果没有条件，则该监视器永远不会进入严重状态。 |
| 评估频率 | 计价单位 | 评估运行状况状态的频率。 |
| 回溯 | 计价单位 | 回溯窗口的大小（以秒为单位）。 有关详细说明，请参阅 [monitorConfiguration 元素](#monitorconfiguration-element)。 |
| 评估类型 | 计价单位 | 定义要使用样本集中的哪个值。 有关详细说明，请参阅 [monitorConfiguration 元素](#monitorconfiguration-element)。 |
| 最小样本数 | 计价单位 | 用于计算值的最小值数目。 |
| 最大样本数 | 计价单位 | 用于计算值的最大值数目。 |


## <a name="default-configuration"></a>默认配置
下表列出每个监视器的默认配置。 这些默认配置无法直接更改，但你可以定义[覆盖项](#overrides)，以修改某些虚拟机的监视配置。


| 监视 | 已启用 | 警报 | 警告 | 严重 | 评估频率 | 回溯 | 评估类型 | 最小样本数 | 最大样本数 |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU 使用率  | True | False | 无 | \> 90%    | 60 秒 | 240 秒 | Min | 2 | 3 |
| 可用内存 | True | False | 无 | \< 100 MB | 60 秒 | 240 秒 | 最大值 | 2 | 3 |
| 文件系统      | True | False | 无 | \< 100 MB | 60 秒 | 120 秒 | 最大值 | 1 | 1 |


## <a name="overrides"></a>替代
*覆盖项* 可更改监视器的一个或多个属性。 例如，覆盖项可以禁用默认情况下启用的监视器、定义监视器的警告标准，或修改监视器的严重阈值。 

覆盖项在[数据收集规则 (DCR)](../agents/data-collection-rule-overview.md) 中定义。 你可创建多个含不同覆盖项集的 DCR，并将其应用于多个虚拟机。 如[为 Azure Monitor 代理配置数据收集（预览）](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations)所述，可以通过创建关联将 DCR 应用于虚拟机。


## <a name="multiple-overrides"></a>多个覆盖项

单一监视器可以有多个覆盖项。 如果这些覆盖项定义了不同的属性，则生成的配置是所有覆盖项的组合。

例如，默认情况下，`memory|available` 监视器未指定警告阈值或启用警报。 假定将下列覆盖项应用于此监视器：

- 覆盖项 1 将 `alertConfiguration.isEnabled` 属性值定义为 `true`
- 覆盖项 2 将 `monitorConfiguration.warningCondition` 的阈值条件定义为 `< 250`。

由此产生的监视器配置如下所示：当可用内存量不足 250Mb 时，该监视器会进入警告运行状况状态并创建严重性为 2 的警报；当可用内存量不足 100Mb 时，该监视器还会进入严重运行状况状态并创建严重性为 1 的警报（如果已存在现有的警报，则将其严重性从 2 更改为 1）。

如果两个覆盖项在同一监视器上定义同一个属性，则其中一个值优先。 覆盖项会根据其[作用域](#scopes-element)（从最一般到最具体）进行应用。 这意味着，应用最具体覆盖项的机会最大。 具体顺序如下所示：

1. 全球 
2. 订阅
3. 资源组
4. 虚拟机。 

如果同一作用域级别的多个覆盖项在同一监视器上定义同一属性，则它们会按其在 DCR 中出现的顺序应用。 如果覆盖项在不同的 DCR 中，则会按 DCR 资源 ID 的字母顺序应用。


## <a name="data-collection-rule-configuration"></a>数据收集规则配置
下列部分介绍数据收集规则中定义覆盖项的 JSON 元素。 如需完整的示例，请参阅[示例数据收集规则](#sample-data-collection-rule)。

## <a name="extensions-structure"></a>扩展结构
来宾运行状况是以 Azure Monitor 代理的扩展形式实现，因此覆盖项在数据收集规则的 `extensions` 元素中定义。 

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
| `name` | 是 | 该扩展的用户定义字符串。 |
| `streams` | 是 | 来宾运行状况数据所要发送到的流列表。 这必须包括 Microsoft-HealthStateChange。  |
| `extensionName` | 是 | 扩展名。 这必须是 HealthExtension。 |
| `extensionSettings` | 是 | 要应用于默认配置的 `healthRuleOverride` 元素数组。 |


## <a name="extensionsettings-element"></a>extensionSettings 元素
包含扩展设置。

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| 元素 | 必选 | 说明 |
|:---|:---|:---|
| `schemaVersion` | 是 | 由 Microsoft 定义的字符串，用于表示元素的预期架构， 目前必须设置为 1.0 |
| `contentVersion` | 否 | 由用户定义的字符串，用于在有需要时跟踪运行状况配置的不同版本。 |
| `healthRuleOverrides` | 是 | 要应用于默认配置的 `healthRuleOverride` 元素数组。 |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides 元素
包含一个或多个 `healthRuleOverride` 元素，其中每个元素都定义一个覆盖项。

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
| `scopes` | 是 | 一个或多个作用域的列表，用于指定适用此覆盖项的虚拟机。 尽管 DCR 与虚拟机关联，但该虚拟机必须在作用域内，才会应用该覆盖项。 |
| `monitors` | 是 | 一个或多个字符串的列表，用于定义哪些监视器要接收此覆盖项。  |
| `monitorConfiguration` | 否 | 监视器的配置，其中包括运行状况状态及其计算方式。 |
| `alertConfiguration` | 否 | 监视器的警报配置。 |
| `isEnabled` | 否 | 控制是否启用监视器。 已禁用的监视器会切换到特殊的“已禁用”运行状况状态，并指出已禁用，除非重新启用。 如果省略此元素，则监视器会从层次结构中的父监视器继承其状态。 |


## <a name="scopes-element"></a>scopes 元素
每个覆盖项都具有一个或多个作用域，用于定义该覆盖项应该应用于哪些虚拟机。 作用域可以是订阅、资源组或单个虚拟机。 即使覆盖项在与特定虚拟机关联的 DCR 中，仅当该虚拟机在该覆盖项的其中一个作用域之内时，该覆盖项才会应用于该虚拟机。 这样一来，你就可以将更少数量的 DCR 与一组 VM 建立关联，但可以在 DCR 本身中精确控制每个覆盖项的分配。 你可以创建一小组 DCR，使用策略将它们与一组虚拟机建立关联，同时使用 scopes 元素为这些虚拟机的不同子集指定运行状况监视器覆盖项。

下表显示不同作用域的示例。

| 范围 | 示例 |
|:---|:---|
| 单个虚拟机 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| 资源组中的所有虚拟机 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| 订阅中的所有虚拟机 | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| 与数据收集规则关联的所有虚拟机 | `*` |


## <a name="monitors-element"></a>monitors 元素
一个或多个字符串的列表，用于定义健康状况层次结构中的哪些监视器要接收此覆盖项。 每个元素都可以是监视器名称或类型名称，与一个或多个要接收此覆盖项的监视器匹配。 

```json
"monitors": [
    "<monitor name>"
 ],
```



下表列出当前可用的监视器名称。

| 类型名称 | 名称 | 描述 |
|:----------|:-----|:------------|
| 根 | root | 代表虚拟机运行状况的顶层监视器。 |
| cpu-utilization | cpu-utilization | CPU 使用率监视器。 |
| logical-disks | logical-disks | Windows 虚拟机上所有受监视磁盘的运行状况状态聚合监视器。 |
| logical-disks\|\* | logical-disks\|C:<br>logical-disks\|D: | 跟踪 Windows 虚拟机上给定磁盘的运行状况的聚合监视器。 |
| logical-disks\|\*\|free-space | logical-disks\|C:\|free-space<br>logical-disks\|D:\|free-space | Windows 虚拟机上的磁盘可用空间监视器。 |
| filesystems | filesystems | Linux 虚拟机上所有文件系统的运行状况聚合监视器。 |
| filesystems\|\* | filesystems\|/<br>filesystems\|/mnt | 跟踪 Linux 虚拟机的文件系统运行状况的聚合监视器。 |
| filesystems\|\*\|free-space | filesystems\|/\|free-space<br>filesystems\|/mnt\|free-space | Linux 虚拟机文件系统上的磁盘可用空间监视器。 |
| 内存 | 内存 | 虚拟机内存运行状况的聚合监视器。 |
| memory\|available | memory\|available | 跟踪虚拟机上可用内存量的监视器。 |


## <a name="alertconfiguration-element"></a>alertConfiguration 元素
指定是否应从该监视器创建警报。

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| 元素 | 必需 | 说明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 如果设置为 true，则监视器会在切换到严重或警告状态时生成警报，并在回到正常状态时解决警报。 如果设置为 false 或省略，则不会生成警报。  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration 元素
仅适用于单元监视器。 定义监视器的配置，其中包括运行状况状态及其计算方式。

参数定义算法，以计算要与阈值比较的指标值。 该监视器不会对来自基础指标的一个数据样本执行操作，而是评估从评估时间到 `lookbackSec` 之前在窗口内收到的多个指标样本。 在该时间范围内收到的所有样本都纳入考量，如果样本数大于 `maxSamples`，则忽略达到 `maxSamples` 之后的旧样本。 

如果回溯时间间隔内的样本数少于 `minSamples`，则监视器会切换到“未知”运行状况状态，这表示没有足够的数据，无法做出有关基础指标运行状况的明智决策。 如果可用样本数大于 `minSamples`，则 evaluationType 参数所指定的聚合函数会在该集合上运行以计算单个值。


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
| `evaluationFrequencySecs` | 否 | 定义评估运行状况状态的频率。 每个监视器都会在代理启动时进行评估，然后按此参数所定义的时间间隔定期评估。 |
| `lookbackSecs`   | 否 | 回溯窗口的大小（以秒为单位）。 |
| `evaluationType` | 否 | `min` – 从整个样本集中获取最小值<br>`max` - 从整个样本集中获取最大值<br>`avg` – 获取样本集值的平均值<br>`all` – 将样本集中的每一个值与阈值进行比较。 当且仅当样本集中的所有样本都满足阈值条件时，监视器才会切换状态。 |
| `minSamples`     | 否 | 用于计算值的最小值数目。 |
| `maxSamples`     | 否 | 用于计算值的最大值数目。 |
| `warningCondition`  | 否 | 警告条件的阈值和比较逻辑。 |
| `criticalCondition` | 否 | 严重条件的阈值和比较逻辑。 |


## <a name="warningcondition-element"></a>warningCondition 元素
定义警告条件的阈值和比较逻辑。 如果未包括此元素，则监视器永不会切换到警告运行状况状态。

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 属性 | 必需 | 说明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 指定是否启用该条件。 如果设置为 false，则会禁用该条件，即使设置了 threshold 和 operator 属性也是如此。 |
| `threshold` | 否 | 定义要与所评估的值进行比较的阈值。 |
| `operator`  | 否 | 定义要在阈值表达式中使用的比较运算符。 可能的值：>、<、>=、<= 和 ==。 |


## <a name="criticalcondition-element"></a>criticalCondition 元素
定义严重条件的阈值和比较逻辑。 如果未包括此元素，则监视器永不会切换到严重运行状况状态。

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 属性 | 必需 | 说明 | 
|:---|:---|:---|
| `isEnabled` | 否 | 指定是否启用该条件。 如果设置为 false，则会禁用该条件，即使设置了 threshold 和 operator 属性也是如此。 |
| `threshold` | 否 | 定义要与所评估的值进行比较的阈值。 |
| `operator`  | 否 | 定义要在阈值表达式中使用的比较运算符。 可能的值：>、<、>=、<= 和 ==。 |

## <a name="sample-data-collection-rule"></a>示例数据收集规则
如需启用来宾监视的示例数据收集规则，请参阅[使用资源管理器模板启用虚拟机](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template)。


## <a name="next-steps"></a>后续步骤

- 阅读有关[数据收集规则](../agents/data-collection-rule-overview.md)的详细信息。
