---
title: 在单个指标警报规则中监视多个时序
description: 针对多个时序使用单个警报规则进行大规模报警
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.subservice: alerts
ms.openlocfilehash: a2ea41b6165e077d6376f9da162058df1196405c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100606199"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>在单个指标警报规则中监视多个时序

可以使用单个指标警报规则监视一个或多个指标时序，从而更容易地大规模监视资源。

## <a name="metric-time-series"></a>指标时序

指标时序是在一段时间内捕获的一系列度量（或“指标值”）。 

例如：

- 虚拟机的 CPU 使用率
- 到存储帐户的传入字节数（流入量）
- Web 应用程序的失败请求数



## <a name="alert-rule-on-a-single-time-series"></a>针对单个时序的警报规则
当警报规则满足以下所有条件时，它会监视单个时序：
-   该规则监视单个目标资源 
-   包含单个条件
-   对指标进行评估而不选择维度（假定指标支持维度）

此类警报规则的示例（仅显示了相关属性）：
-   目标资源：myVM1
-   指标：CPU 百分比
-   运算符：大于
-   阈值：*70*


对于此警报规则，将监视单个指标时序：
-   资源=“myVM1”时的 CPU 百分比 > 70%

![针对单个时序的警报规则](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>针对多个时序的警报规则
如果警报规则至少使用以下功能之一，则会监视多个时序： 
-   多个资源
-   多个条件 
-   多个维度


## <a name="multiple-resources-multi-resource"></a>多个资源（多资源）

单个指标警报规则可监视多个资源，前提是资源属于同一类型并且存在于同一 Azure 区域中。 使用这种类型的规则可降低复杂性和必须维护的警报规则总数。 

此类警报规则的一个示例：
-   目标资源：myVM1、myVM2
-   指标：CPU 百分比
-   运算符：大于
-   阈值：*70*

对于此警报规则，将分别监视两个指标时序：
-   资源=“myVM1”时的 CPU 百分比 > 70%
-   资源=“myVM2”时的 CPU 百分比 > 70%

![多资源警报规则](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
在多资源警报规则中，将分别针对每个资源（或者更准确地说，将分别针对与每个资源对应的每个指标时序）来评估条件。 这意味着，还会为每个资源分别触发警报。

例如，假设我们已将上面的警报规则设置为监视 CPU 使用率高于 70% 的情况。 在评估的时间段内（即过去的 5 分钟）
-   myVM1 的 CPU 百分比大于 70% 
-   myVM2 的 CPU 百分比为 50%

警报规则在 myVM1 上触发，但不在 myVM2 上触发。 这些触发的警报是独立的。 它们还可以根据每个虚拟机的个体行为在不同的时间解决。

有关多资源警报规则以及此功能支持的资源类型的详细信息，请参阅[使用 Azure Monitor 中的指标警报进行大规模监视](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

> [!NOTE] 
> 在监视多个资源的指标警报规则中，仅允许包含单个条件。

## <a name="multiple-conditions-multi-condition"></a>多个条件（多条件）

使用单个指标警报规则时，每个警报规则还可以监视最多五个条件。 

例如：

- 目标资源：myVM1
- Condition1
  - 指标：CPU 百分比
  - 运算符：大于
  - 阈值：*70*
- Condition2
  - 指标：网络传入流量总计
  - 运算符：大于
  - 阈值：20 MB

对于此警报规则，将监视两个指标时序：

- 资源=“myVM1”时的 CPU 百分比 > 70%
- 资源=“myVM1”时的网络传入流量总计 > 20 MB

![多条件警报规则](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
条件之间使用“AND”运算符。 当满足所有条件时，警报规则触发警报。 如果至少有一个条件不再满足，则触发的警报得以解决。 

> [!NOTE]
> 在具有多个条件的警报规则中使用维度时存在限制。 有关详细信息，请参阅[在具有多个条件的指标警报规则中使用维度时的限制](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions)。


## <a name="multiple-dimensions-multi-dimension"></a>多个维度（多维度）

单个指标警报规则还可以监视指标的多个维度值。 指标的维度是携带附加数据来描述指标值的名称/值对。 例如，存储帐户的“事务”指标有一个名为“API 名称”的维度，它描述每个事务调用的 API 的名称（例如，GetBlob、DeleteBlob、PutPage）。 维度的使用是可选的，但可用来对指标进行筛选并仅监视特定的时序，而不是将指标作为放置在一起的所有维度值的聚合进行监视。 

例如，你可以选择在所有 API 名称中的事务数（这是聚合数据）较高的情况下触发警报，或进一步将其分解为仅在特定 API 名称的事务数较高时发出警报。 

监视多个维度的警报规则的一个示例是：

- 目标资源：myStorage1
- 指标：*Transactions*
- 维度
  * API 名称 = GetBlob、DeleteBlob、PutPage
- 运算符：大于
- 阈值：*70*

对于此警报规则，将监视三个指标时序：

- 资源 =“myStorage1”且 API 名称 =“GetBlob”时的事务数 > 70 
- 资源 =“myStorage1”且 API 名称 =“DeleteBlob”时的事务数 > 70 
- 资源 =“myStorage1”且 API 名称 =“PutPage”时的事务数 > 70 

![包含一个维度中的值的多维度警报规则](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

多维度指标警报规则还可以监视指标的不同维度中的多个维度值。 在此情况下，警报规则分别监视所选维度值的所有维度值组合。

此类警报规则的一个示例：

- 目标资源：myStorage1
- 指标：*Transactions*
- 维度
  * API 名称 = GetBlob、DeleteBlob、PutPage
  * 身份验证 = SAS、AccountKey
- 运算符：大于
- 阈值：*70*

对于此警报规则，将分别监视六个指标时序：

- 资源 =“myStorage1”且 API 名称 =“GetBlob”且身份验证=“SAS”时的事务数 > 70  
- 资源 =“myStorage1”且 API 名称 =“GetBlob”且身份验证=“AccountKey”时的事务数 > 70  
- 资源 =“myStorage1”且 API 名称 =“DeleteBlob”且身份验证=“SAS”时的事务数 > 70  
- 资源 =“myStorage1”且 API 名称 =“DeleteBlob”且身份验证=“AccountKey”时的事务数 > 70  
- 资源 =“myStorage1”且 API 名称 =“PutPage”且身份验证=“SAS”时的事务数 > 70  
- 资源 =“myStorage1”且 API 名称 =“PutPage”且身份验证=“AccountKey”时的事务数 > 70  

![包含多个维度中的值的多维度警报规则](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>高级多维度功能

1.  **选择所有当前和未来的维度** -可以选择监视维度的所有可能值，包括未来值。 此类警报规则会自动缩放以监视维度的所有值，不需要你在每次添加或删除维度值时修改警报规则。
2.  **排除维度** –为维度值选择 "≠" ("排除") 运算符等效于选择该维度的所有其他值，包括未来值。
3.  **新维度和自定义维度** -Azure 门户中显示的维度值基于在上一天收集的指标数据。 如果你要查找的维度值尚未发出，则可以添加自定义维度值。
4. 与前缀匹配的维度 - 通过选择“开头为”运算符并输入自定义前缀，你可以选择监视以特定模式开头的所有维度值。

![高级多维度功能](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>指标警报定价

指标警报规则的定价位于 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上。

创建指标警报规则时，提供的价格估算基于所选功能和监视的时序数，时序数是根据规则配置和当前指标值确定的。 不过，每月费用取决于时序的实际评估，因此，如果某个时序没有要评估的数据，或者警报规则使用可能会导致其动态缩放的功能，则每月费用可能不同于原始估算值。

例如，如果某个警报规则利用了多维度功能，并且选择了大量维度值组合（这将导致监视许多时序），该警报规则可能会显示较高的价格估算值。 但是，如果实际上并非维度值组合生成的所有时序都有要评估的数据，则该警报规则的实际费用可能较低。

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>单个警报规则监视的时序数

为避免额外的成本，默认情况下每个警报规则最多可监视 5000 个时序。 若要提升你的订阅中的此限制，请创建一个支持票证。


## <a name="next-steps"></a>后续步骤

详细了解如何使用指标警报和[动态阈值](../alerts/alerts-dynamic-thresholds.md)进行大规模监视。
