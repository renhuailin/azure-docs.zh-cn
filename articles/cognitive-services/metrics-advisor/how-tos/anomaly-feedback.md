---
title: 向指标顾问服务提供异常反馈
titleSuffix: Azure Cognitive Services
description: 了解如何就指标顾问实例发现的异常发送反馈，并优化结果。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184795"
---
# <a name="provide-anomaly-feedback"></a>提供异常反馈

若要发现异常检测系统中的缺陷，用户反馈是最重要的一种方法。 在这里，我们提供了一种方法，使用户可以直接在时序上标记错误检测结果，并立即应用反馈。 通过这种方式，用户可以讲授异常检测系统如何通过活动交互为特定时序执行异常检测。 

> [!NOTE]
> 当前反馈只会影响 **智能检测** 的异常情况检测结果，但不会影响 **硬性阈值** 和 **更改阈值**。

## <a name="how-to-give-time-series-feedback"></a>如何提供时序反馈

可以从任何序列上的指标详细信息页提供反馈。 只需选择任意点，你会看到以下反馈对话框。 其中显示了所选系列的尺寸。 您可以重新选择维度值，甚至可以删除其中一些维度值才能获得一批时序数据。 选择时序后，选择 " **添加** " 按钮添加反馈，可以提供四种类型的反馈。 若要附加多个反馈项，请在完成批注后选择 " **保存** " 按钮。

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="在不同点使用带有蓝线和红点的时间序列数据的关系图。带有文本的一个点周围的红色方框：选择任意点":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="添加具有两个维度的反馈对话框，以及选择或删除维度和添加反馈的选项。":::

### <a name="mark-the-anomaly-point-type"></a>标记异常点类型

如下图所示，"反馈" 对话框将自动填充所选点的时间戳，但你可以编辑此值。 然后，选择是要将此项目标识为 `Anomaly` 、 `NotAnomaly` 还是 `AutoDetect` 。

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="具有异常、NotAnomaly 和自动检测选项的下拉菜单":::

选择会将你的反馈应用于同一序列的未来异常情况检测处理。 处理的点将不会重新计算。 这意味着，如果你将异常标记为 NotAnomaly，我们将在将来取消类似的异常，如果你将一个 `NotAnomaly` 点标记为 `Anomaly` ，我们往往会检测到类似的点，如 `Anomaly` 未来所示。 如果 `AutoDetect` 选择了，则在同一点上的任何以前的反馈都将在将来被忽略。

## <a name="provide-feedback-for-multiple-continuous-points"></a>为多个连续点提供反馈 

如果希望同时为多个连续点提供异常反馈，请选择要为其添加批注的点组。 当您提供异常反馈时，您将看到所选的时间范围已自动填充。

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="异常反馈菜单，其中选择了异常和特定的时间范围":::

若要查看个别点是否受异常反馈影响，请在浏览时序时选择一个点。 如果其异常情况检测结果已被反馈更改，则工具提示会显示 " **反馈： true**"。 如果其显示 **受反馈影响： false**，这意味着对此点执行异常反馈计算，但不应更改异常情况检测结果。

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="工具提示显示单词：受反馈影响： true，以红色突出显示":::

在某些情况下，我们不建议提供反馈：

- 异常是由假日导致的。 建议使用预设事件来解决这种虚假警报，因为它会更精确。
- 异常是由已知数据源更改引起的。 例如，上游系统发生更改。 在这种情况下，预期会发出异常警报，因为我们的系统不知道是什么导致了值更改，而类似的值更改将再次发生。 因此，建议不要将此类问题作为注释 `NotAnomaly` 。

## <a name="change-points"></a>更改点

有时，数据的趋势变化会影响异常情况检测结果。 当决定某个点是否异常情况时，将考虑历史记录数据的最新窗口。 当时序发生趋势变化时，您可以标记确切的更改点，这将有助于我们以后分析异常探测器。

如下图所示，你可以选择 `ChangePoint` 作为反馈类型，然后从下拉列表中选择 `ChangePoint` 、 `NotChangePoint` 或 `AutoDetect` 。

:::image type="content" source="../media/feedback/changepoint.png" alt-text="带有包含 ChangePoint、NotChangePoint 和自动检测选项的下拉列表的更改点菜单":::

> [!NOTE]
> 如果数据保持更改，只需将一个点标记为 `ChangePoint` ，因此，如果标记为 `timerange` ，则将自动填充最后一个点的时间戳和时间。 在这种情况下，批注仅会在12个点后影响异常情况检测结果。

## <a name="seasonality"></a>季节性

对于季节性数据，当我们执行异常检测时，一步是估算时序 (季节性) 的时间段，并将其应用于异常检测阶段。 有时，很难确定精确的时间段，也可能会发生变化。 错误定义的时间段可能对异常情况检测结果有副作用。 您可以从工具提示中查找当前时间段，其名称为 `Min Period` 。

:::image type="content" source="../media/feedback/min-period.png" alt-text="工具提示覆盖了单词 Period，以红色突出显示了7。":::

你可以为时间段提供反馈来修复这种异常情况检测错误。 如图所示，您可以设置一个期间值。 单位 `interval` 表示一次粒度。 此处零间隔表示数据是非季节性。 你还可以选择 `AutoDetect` 是否要取消以前的反馈，并使管道自动检测时间。 
 
> [!NOTE]
> 当设置时间段时，无需分配时间戳或 timerange，该时间段将影响在提供反馈的那一刻整个 timeseries 上的未来异常检测。


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="自动检测周期设置为28且间隔设置为零的菜单，指示非季节性。":::

## <a name="provide-comment-feedback"></a>提供注释反馈

你还可以添加注释以批注，并为你的数据提供上下文。 若要添加注释，请选择时间范围并添加注释文本。

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="可以设置时间范围和框以添加基于文本的注释的菜单":::

## <a name="time-series-batch-feedback"></a>时序批处理反馈

如前文所述，使用反馈模式，您可以重新选择或删除维度值，以获取维度筛选器定义的一批时序。 还可以通过单击左侧面板中的反馈的 "+" 按钮，然后选择 "维度" 和 "维度值" 来打开此模式。

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="带有蓝色加号的菜单在 &quot;word 反馈&quot; 旁突出显示为红色":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="添加 Dim1 和 Dim2 指示的两个维度的反馈菜单":::

## <a name="how-to-view-feedback-history"></a>如何查看反馈历史记录

可以通过两种方法查看反馈历史记录。 你可以从左侧面板中选择 "反馈历史记录" 按钮，并将看到反馈列表模式。 它列出了你之前为单个序列或维度筛选器提供的所有反馈。

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="反馈列表菜单":::

查看反馈历史记录的另一种方法是使用系列。 每个序列的右上角将显示多个按钮。 选择 "显示反馈" 按钮，该行将从显示异常点切换到显示反馈条目。 绿色标志表示更改点，蓝色点为其他反馈点。 你还可以选择它们，并将获取一个反馈列表模式，其中列出了为此点提供的反馈的详细信息。

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="反馈历史记录图":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="具有两个维度的 &quot;反馈列表&quot; 菜单":::

> [!NOTE]
> 允许有权访问该指标的任何人都可以提供反馈，因此你可能会看到其他 datafeed 所有者提供的反馈。 如果你编辑的点与其他人相同，你的反馈将覆盖以前的反馈条目。       

## <a name="next-steps"></a>后续步骤
- [诊断事件](diagnose-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)
- [配置警报并使用挂钩获取通知](../how-tos/alerts.md)