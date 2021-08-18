---
title: 向指标顾问服务提供异常反馈
titleSuffix: Azure Cognitive Services
description: 了解如何就指标顾问实例发现的异常发送反馈，并优化结果。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: de42785cc2b28e6512db70267961db0b44cf5019
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341160"
---
# <a name="provide-anomaly-feedback"></a>提供异常反馈

用户反馈是异常检测系统中发现缺陷的重要方法之一。 在这里，我们为用户提供了一种直接在时序上标记错误检测结果的方法，并立即应用反馈。 这样，用户可以指示异常检测系统如何通过主动交互对特定时序执行异常检测。 

> [!NOTE]
> 目前，反馈只会通过“智能检测”来影响异常检测结果，而不会通过“硬阈值”和“变化阈值”来影响异常检测结果  。

## <a name="how-to-give-time-series-feedback"></a>如何提供时序反馈

可以在任何序列上的指标详细信息页提供反馈。 只需选择任意点，你就会看到以下反馈对话框。 此对话框将显示所选序列的维度。 你可以重新选择维度值，甚至可以删除其中一些维度值来获取一批时序数据。 选择时序后，可选择“添加”按钮添加反馈，你可以提供四种反馈。 若要追加多个反馈项，请在完成批注后，选择“保存”按钮。

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="具有蓝线和在不同点具有红点的时序数据关系图。红色框围绕一个点，并包含文本：选择任意点":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="“添加反馈”对话框，其中包含两个维度以及“选择或删除维度”和“添加反馈”选项。":::

### <a name="mark-the-anomaly-point-type"></a>标记异常点类型

如下图所示，“反馈”对话框将自动填充所选点的时间戳，但你可以编辑此值。 然后选择是否要将此项标识为 `Anomaly`、`NotAnomaly` 或 `AutoDetect`。

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="具有“Anomaly”、“NotAnomaly”和“AutoDetect”选项的下拉菜单":::

该选择会将你的反馈应用于同一序列的未来异常情况检测处理。 已处理的点将不会重新计算。 这意味着，如果你将 Anomaly 标记为 NotAnomaly，未来我们将取消类似的异常，如果你将 `NotAnomaly` 点标记为 `Anomaly`，未来我们会将类似点检测为 `Anomaly`。 如果选择 `AutoDetect`，则未来将忽略同一点上以前的任何反馈。

## <a name="provide-feedback-for-multiple-continuous-points"></a>为多个连续点提供反馈 

如果希望同时为多个连续点提供异常反馈，请选择要为其添加批注的点组。 当你提供异常反馈时，你将看到所选的时间范围已自动填充。

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="异常反馈菜单，其中选择了异常和特定的时间范围":::

若要查看个别点是否受异常反馈的影响，请在浏览时序时选择单个点。 如果该点的异常情况检测结果已根据反馈更改，工具提示将显示“受反馈影响: true”。 如果显示“受反馈影响: false”，这意味着已对此点执行异常反馈计算，但不应更改异常情况检测结果。

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="工具提示显示，其中文本：“受反馈影响: true”已用红框突出显示":::

在以下情况下，我们不建议提供反馈：

- 异常是由假日导致的。 建议使用预设事件来解决此类误报，这样会更精确。
- 异常是由已知数据源更改导致的。 例如，当时上游系统发生更改。 在这种情况下，预期会发出异常警报，因为系统不知道导致值更改的原因以及何时会再次发生类似的值更改。 因此，建议不要将此类问题批注为 `NotAnomaly`。

## <a name="change-points"></a>更改点

有时，数据的趋势变化会影响异常情况检测结果。 在决定某点是否为异常点时，应将历史数据的最新窗口纳入考虑范围。 当时序发生趋势变化时，可以标记确切的更改点，这将有助于我们以后分析异常探测器。

如下图所示，你可以选择 `ChangePoint` 作为反馈类型，然后从下拉列表中选择 `ChangePoint`、`NotChangePoint` 或 `AutoDetect`。

:::image type="content" source="../media/feedback/changepoint.png" alt-text="“更改点”菜单，其中具有包含 ChangePoint、NotChangePoint 和 AutoDetect 选项的下拉列表":::

> [!NOTE]
> 如果数据不断变化，只需将一个点标记为 `ChangePoint`，因此，如果你标记了 `timerange`，我们将自动填充最后一个点的时间戳和时间。 在这种情况下，批注只会影响 12 个点后的异常情况检测结果。

## <a name="seasonality"></a>季节性

对于季节性数据，执行异常情况检测时，需要估算时序的周期（季节性），并将其应用于异常情况检测阶段。 有时，很难确定精确的周期，而且周期也可能发生变化。 定义错误的周期可能会对异常情况检测结果产生副作用。 你可以在工具提示中查找当前周期，其名称为 `Min Period`。

:::image type="content" source="../media/feedback/min-period.png" alt-text="工具提示被“周期”一词覆盖，并用红框红色突出显示了数字 7。":::

你可以提供周期反馈来修复这种异常情况检测错误。 如图所示，你可以设置一个周期值。 单位 `interval` 表示一个粒度。 在这里，零间隔表示数据是非季节性的。 如果你想取消之前的反馈，并让管道自动检测周期，也可以选择 `AutoDetect`。 
 
> [!NOTE]
> 设置周期时，无需指定时间戳或时间范围，该周期将影响从你提供反馈的那一刻起整个时序上的未来异常情况检测。


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="“自动检测”周期设置为 28 且间隔设置为 0 以指示非季节性的菜单。":::

## <a name="provide-comment-feedback"></a>提供注释反馈

还可添加注释以批注数据，并为数据提供上下文。 若要添加注释，请选择时间范围并添加注释文本。

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="可以设置时间范围和框以添加基于文本的注释的菜单":::

## <a name="time-series-batch-feedback"></a>时序批反馈

如前文所述，使用反馈模式，你可以重新选择或删除维度值，以获取由维度筛选器定义的一批时序。 还可以通过单击左侧面板中反馈的“+”按钮，然后选择维度和维度值来打开此模式。

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="在“反馈”一词旁用红框突出显示蓝色加号的菜单":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="具有 Dim1 和 Dim2 指示的两个维度的“添加反馈”菜单":::

## <a name="how-to-view-feedback-history"></a>如何查看反馈历史记录

可通过两种方式查看反馈历史记录。 可在左侧面板中选择“反馈历史记录”按钮，随后你就将看到反馈列表模式。 该列表将列出你之前为单个序列或维度筛选器提供的所有反馈。

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="反馈列表菜单":::

查看反馈历史记录的另一种方法是从序列进行查看。 每个序列的右上角显示多个按钮。 选择“显示反馈”按钮，该行将从显示异常点切换为显示反馈条目。 绿色标志表示更改点，蓝色点为其他反馈点。 你还可以选择它们，并将获得一个反馈列表模式，其中将列出对这些点提供的反馈的详细信息。

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="反馈历史记录图":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="具有两个维度的反馈列表菜单":::

> [!NOTE]
> 任何有权访问该指标的人都可以提供反馈，因此你可能会看到其他数据源所有者提供的反馈。 如果你和其他人编辑了同一个点，你的反馈将覆盖以前的反馈条目。       

## <a name="next-steps"></a>后续步骤
- [诊断事件](diagnose-an-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)
- [配置警报并使用挂钩获取通知](../how-tos/alerts.md)