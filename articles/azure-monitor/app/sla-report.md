---
title: 停机时间、SLA 和中断工作簿 - Application Insights
description: 通过 Application Insights 资源和 Azure 订阅中的单一管理平台计算和报告 Web 测试的 SLA。
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714045"
---
# <a name="downtime-sla-and-outages-workbook"></a>停机时间、SLA 和中断工作簿

介绍一种简单方法，通过 Application Insights 资源和 Azure 订阅中的单一管理平台计算和报告 Web 测试的 SLA（服务级别协议）。 “停机时间和中断”报告提供了强大的预生成查询和数据可视化功能，使你能够更加深入地了解客户连接、典型应用程序响应时间以及经历的停机时间。

可以通过 Application Insights 资源中的工作簿库或者通过可用性选项卡（选择顶部的 **SLA 报告**）来访问 SLA 工作簿模板。
:::image type="content" source="./media/sla-report/availability.png" alt-text="可用性选项卡的屏幕截图，其中突出显示了 SLA 报告。" lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="工作簿库的屏幕截图，其中突出显示了停机时间和中断工作簿。" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>参数灵活性

工作簿中设置的参数会影响报告的其余部分。

:::image type="content" source="./media/sla-report/outages.png" alt-text="停机时间和中断工作簿中的“中断/维护参数”选项卡的屏幕截图。" lightbox ="./media/sla-report/outages.png":::

`Subscriptions`、`App Insights Resources` 和 `Web Test` 参数确定大致的资源选项。 这些参数基于 Log Analytics 查询，并用于每个报告查询。

`Failure Threshold` 和 `Outage Window` 使你能够确定自己的服务中断标准，例如，App Insights Availability 警报的标准基于选定时间段内的失败位置计数器。 典型阈值为五分钟时段内三个位置。

`Maintenance Period` 使你能够选择典型维护频率，`Maintenance Window` 是示例维护时段的日期/时间选择器。 结果将忽略在确定的时段内出现的所有数据。

`Availability Target 9s` 指定从两个 9s 到五个 9s 的 Target 9s 目标。

## <a name="overview-page"></a>概述页

概述页包含有关总 SLA（如果定义了维护时段，则排除该时段）、端到端中断实例和应用程序停机时间的更多信息。 中断实例定义为根据中断参数测试开始失败直至成功。 如果测试在上午 8:00 开始失败，并且在上午 10:00 再次成功，则这整个数据周期将被视为同一中断。

:::image type="content" source="./media/sla-report/overview.gif" alt-text="显示概述表（按测试）的概述页的 GIF。" lightbox="./media/sla-report/overview.gif":::

你还可以调查报告期出现的最长中断时间。

一些测试可以链接回其 Application Insights 资源以供进一步调查，但这仅适用于[基于工作区的 Application Insights 资源](create-workspace-resource.md)。

## <a name="downtime-outages-and-failures"></a>停机时间、中断和失败

“中断和停机时间”选项卡包含按测试细分的总停机实例和总停机时间的相关信息。 “按位置列出的失败”选项卡具有失败测试位置的地图，以帮助确定潜在的问题连接区域。

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text="停机时间和中断工作簿中的“中断和停机时间”选项卡和“按位置列出的失败”选项卡的 GIF。" lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>编辑报表

可以像任何其他 [Azure Monitor 工作簿](../visualize/workbooks-overview.md)一样编辑报告。 可以根据团队需求自定义查询或可视化效果。

:::image type="content" source="./media/sla-report/edit.gif" alt-text="选择“编辑”按钮以将可视化效果更改为饼图的 GIF。" lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

可以在 [Log Analytics](../logs/log-analytics-overview.md) 中运行所有查询，并在其他报表或仪表板中使用这些查询。 删除参数限制并重用核心查询。

:::image type="content" source="./media/sla-report/logs.gif" alt-text="日志查询的 GIF。" lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>访问和共享

可与你的团队、领导共享报告，也可以将其固定在仪表板上以供进一步使用。 用户需要对存储实际工作簿的 Application Insights 资源具有读取权限/访问权限。

:::image type="content" source="./media/sla-report/share.png" alt-text="共享此模板的屏幕截图。" lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>后续步骤

- [Log Analytics 查询优化提示](../logs/query-optimization.md)。
- 了解如何[在工作簿中创建图表](../visualize/workbooks-chart-visualizations.md)。
- 了解如何通过[可用性测试](monitor-web-app-availability.md)监视网站。