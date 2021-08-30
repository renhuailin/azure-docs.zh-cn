---
title: 使用 Web 门户配置指标顾问实例
titleSuffix: Azure Cognitive Services
description: 如何配置指标顾问实例并微调异常检测结果。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 3190976b5b5261331d2b0adec333b3530ec45ea7
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341156"
---
# <a name="how-to-configure-metrics-and-fine-tune-detection-configuration"></a>操作指南：配置指标并优化检测配置

利用本文开始使用 Web 门户配置指标顾问实例。 若要浏览特定数据馈送的指标，请转到“数据馈送”页，然后选择其中一个馈送。 这将显示与之关联的指标列表。

:::image type="content" source="../media/metrics/select-metric.png" alt-text="选择指标" lightbox="../media/metrics/select-metric.png":::

选择其中一个指标名称可查看其详细信息。 在此详细视图中，可以使用屏幕右上角的下拉列表切换到同一数据馈送中的其他指标。

第一次查看指标的详细信息时，可以加载时序，方法是让指标顾问选择一个时序或为每个维度指定要包含的值。 

还可以选择时间范围，并更改页面的布局。

> [!NOTE]
> - 开始时间为包含时间。
> - 结束时间为非包含时间。 

可以单击“事件”选项卡查看异常，并找到[事件中心](diagnose-an-incident.md)的链接。

## <a name="tune-the-detection-configuration"></a>优化检测配置

指标可以应用一个或多个检测配置。 每个指标都有一个默认配置，可以根据监视需求对其进行编辑或添加。

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>优化当前指标中所有序列的配置

此配置将应用于此指标中的所有序列，具有单独配置的序列除外。 默认情况下，在载入数据时应用指标级别配置，并显示在左侧面板上。 用户可以直接在指标页上编辑指标级别配置。 

还有一些其他参数（如“方向”和“有效异常”）可以用于进一步优化配置 。 你也可以组合不同的检测方法。 

:::image type="content" source="../media/configuration-combination.png" alt-text="配置组合" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>优化特定系列或组的配置

单击指标级别配置选项下方的“高级配置”，查看组级别配置。可以通过单击此窗口的 + 图标为各序列或序列组添加配置。 参数与指标级别配置参数类似，但你可能需要为组级别配置指定至少一个维度值才能标识一组序列。 指定序列级别配置的所有维度值以标识特定序列。 

此配置将应用于序列组或特定序列，而不是指标级别配置。 设置该组的条件后，将其保存。

:::image type="content" source="../media/advanced-configuration.png" alt-text="高级配置" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>异常情况检测方法

指标顾问提供多种异常检测方法：硬阈值、智能检测、更改阈值。 通过单击“+”按钮，可以使用其中一种方法或将其与逻辑运算符组合使用。 

硬阈值

 硬阈值是异常检测的基本方法。 可以设置上限和/或下限来确定预期的值范围。 边界外的任何点将被标识为异常。 

**智能检测** 

智能检测由机器学习提供支持，它通过历史数据学习模式，并将其用于将来的检测。 使用此方法时，“敏感度”是优化检测结果的最重要的参数。 可以将其拖动为更小或更大的值，以影响页面右侧的可视化效果。 选择一个适合你的数据的值并将其保存。 


在智能检测模式下，敏感度和边界版本参数用于优化异常情况检测结果。

敏感度可以影响每个点的预期值范围的宽度。 当敏感度增加时，预期的值范围将变窄，报告的异常情况将变多：

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="具有高敏感度的智能检测":::

关闭敏感度后，预期的值范围将变宽，报告的异常情况将变少：

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="具有低敏感度的智能检测":::

变化阈值 

当指标数据一般保持在某个范围时，我们通常使用变化阈值。 阈值根据“变化百分比”设置。 “变化阈值”模式能够检测以下场景中的异常：

* 数据通常稳定平滑。 你需要在波动发生时获得通知。
* 数据通常非常不稳定，波动很大。 你希望在数据变得过于平稳时收到通知。

使用以下步骤来使用此模式：

1. 为指标或时序设置异常检测配置时，请选择“变化阈值”作为异常检测方法。
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="变化阈值":::

2. 基于场景选择“超出范围”或“范围内”参数 。

    如果要检测波动，请选择“超出范围”。 例如，使用下面的设置时，与前一个数据点相比，变化超过 10% 的任何数据点将被检测为离群值。
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="“超出范围”参数":::

    如果要检测数据中的平整线，选择“范围内”。 例如，使用下面的设置时，与前一个数据点相比，变化在 0.01% 内的任何数据点将被检测为离群值。 由于阈值太小 (0.01%)，因此它将数据中的平整线检测为离群值。

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="“范围内”参数":::

3. 设置将计为异常的变化的百分比，并设置将哪些以前捕获的数据点用于比较。 这种比较总是在当前数据点和在它之前 N 点的数据点之间进行。
    
    “方向”仅在使用“超出范围”模式时才有效 ：
    
    * **Up** 用于以下检测配置：仅当（当前数据点）-（比较数据点）> + 阈值百分比时检测异常。
    * **Down** 用于以下检测配置：仅当（当前数据点）-（比较数据点）< - 阈值百分比时检测异常。



## <a name="preset-events"></a>预设事件

有时，预期的事件（如假日）可能生成异常数据。 使用预设事件，可以在指定的时间将标志添加到异常情况检测输出。 应在载入数据馈送后配置此功能。 每个指标只能有一个预设的事件配置。

> [!Note]
> 预设事件配置将在异常检测期间考虑假日，并可能更改结果。 保存配置后，它将应用于引入的数据点。 

单击每个指标详细信息页面上指标下拉列表旁边的“配置预设事件”按钮。
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="预设事件按钮":::

在显示的窗口中，根据使用情况配置选项。 确保选择“启用假日事件”以使用此配置。 

“假日事件”部分帮助你取消在假日期间检测到的不必要的异常。 可以应用“策略”选项的两个选项：

* 取消假日：取消假日期间异常情况检测结果中的所有异常和警报。
* 假日作为周末：计算假日前几个相应周末的平均预期值，并基于这些值确定异常状态。

可以配置一些其他值：

|选项  |说明  |
|---------|---------|
|选择一个维度作为国家/地区     | 选择一个包含国家/地区信息的维度。 例如，国家/地区代码。         |
|国家/地区代码映射     | 标准[国家/地区代码](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)与所选维度的国家/地区数据之间的映射。        |
|假日选项    | 考虑所有假日、只考虑 PTO（带薪休假）假日或只考虑非 PTO 假日。         |
|扩展的天数    |  假期前后受影响的天数。        |


在某些情况下，可以使用“循环事件”部分，通过使用数据中的循环模式来帮助减少不必要的警报。 例如： 。 

- 具有多个模式或循环的指标，如每周和每月模式。 
- 没有清晰模式但数据可以进行年同比 (YoY)、月同比 (MoM)、周同比 (WoW) 或日同比 (DoD) 的指标。
 
并非所有选项都可选择用于每个粒度。 每个粒度的可用选项如下（✔ 表示可用，X 表示不可用）：

| 粒度 | YoY | MoM | WoW | DoD |
|:-|:-|:-|:-|:-|
| 每年 | X | X | X | X |
| 每月 | X | X | X | X |
| 每周 | ✔ | X | X | X |
| 每天 | ✔ | ✔ | ✔ | X |
| 每小时 | ✔ | ✔ | ✔ | ✔ |
| 每分钟 | X | X | X | X |
| 每秒 | X | X | X | X |
| 自定义* | ✔ | ✔ | ✔ | ✔ |

  
 如果使用自定义粒度（以秒为单位），则仅当指标超过一小时且小于一天时才可用。

循环事件用于减少遵循循环模式的异常，但如果多个数据点不遵循模式，则将报告异常。 严格模式用于在即使只有一个数据点不遵循模式时启用异常报告。 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="预设事件配置":::

## <a name="view-recent-incidents"></a>查看最近事件

指标顾问在所有时序数据引入时检测异常情况。 但是，并不是所有异常都需要上报，因为它们可能不会产生很大的影响。 聚合将针对异常执行，将相关事件分组为事件。 可以在指标详细信息页的“事件”选项卡中查看这些事件。 

单击事件可转到“事件分析”页，你可在其中查看更多相关详细信息。 单击“在新事件中心中管理事件”，找到[事件中心](diagnose-an-incident.md)页，你可在其中找到特定指标下的所有事件。 

## <a name="subscribe-anomalies-for-notification"></a>订阅异常以接收通知

如果希望在检测到异常时收到通知，可以使用挂钩订阅指标的警报。 如需了解详细信息，请参阅[使用挂钩配置警报并获得通知](alerts.md)。


## <a name="next-steps"></a>后续步骤 
- [配置警报并使用挂钩获取通知](alerts.md)
- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-an-incident.md)。

