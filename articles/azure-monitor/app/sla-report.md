---
title: 停机时间、SLA 和中断工作簿-Application Insights
description: 通过 Application Insights 资源和 Azure 订阅中的单个窗格，计算和报告 Web 测试的 SLA。
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714045"
---
# <a name="downtime-sla-and-outages-workbook"></a>停机时间、SLA 和中断工作簿

介绍一种简单的方法来计算和报告服务级别协议 (服务级别协议) ，通过跨 Application Insights 资源和 Azure 订阅的单个窗格来实现 Web 测试。 "停机和中断" 报告提供强大的预构建查询和数据可视化功能，可增强你对客户的连接、典型的应用程序响应时间和经历的停机时间。

可以通过 Application Insights 资源中的工作簿库或通过 "可用性" 选项卡（在顶部选择 " **Sla 报表** "）来访问 sla 工作簿模板。
:::image type="content" source="./media/sla-report/availability.png" alt-text="突出显示 SLA 报表的 &quot;可用性&quot; 选项卡的屏幕截图。" lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="突出显示了 &quot;停机和中断&quot; 工作簿的工作簿库屏幕截图。" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>参数灵活性

工作簿中设置的参数将影响报表的其余部分。

:::image type="content" source="./media/sla-report/outages.png" alt-text=" 停机时间和中断工作簿中的 &quot;中断/维护参数&quot; 选项卡的屏幕截图。" lightbox ="./media/sla-report/outages.png":::

`Subscriptions`、 `App Insights Resources` 和 `Web Test` 参数确定高级资源选项。 这些参数基于 log analytics 查询，并在每个报表查询中使用。

`Failure Threshold` 并且 `Outage Window` 允许你确定服务中断的你自己的条件，例如，基于失败位置的 App Insights 可用性警报的标准在选定时间段内计数器。 典型阈值为五分钟时间段内的三个位置。

`Maintenance Period` 使您能够选择您的典型维护频率， `Maintenance Window` 它是一个示例维护期间的日期时间选择器。 在您的结果中将忽略在确定的时间段内发生的所有数据。

`Availability Target 9s` 指定从两个9到五个9的目标9目标。

## <a name="overview-page"></a>概述页

"概述" 页包含有关总 SLA (的高级信息，如定义) 、端到端中断实例和应用程序停机时间之外的维护期。 中断实例是在测试开始失败时定义的，因为它会根据你的中断参数成功完成。 如果测试在凌晨8:00 开始失败，并在 10:00 am 再次成功，则会将整段数据视为相同的中断。

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" 按测试显示概述表的 &quot;概述&quot; 页的 GIF。" lightbox="./media/sla-report/overview.gif":::

你还可以调查报表期间发生的最长中断。

某些测试会可链接到其 Application Insights 资源进行进一步的调查，但这仅适用于 [基于工作区的 Application Insights 资源](create-workspace-resource.md)。

## <a name="downtime-outages-and-failures"></a>停机时间、中断和故障

" **中断和停机** " 选项卡包含有关中断实例总数和按测试划分的总停机时间的信息。 " **按位置失败** " 选项卡具有失败测试位置的地理位置图，以帮助确定潜在的问题连接区域。

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" &quot;停机时间和中断&quot; 工作簿中的 &quot;中断和停机&quot; 选项卡的 GIF。" lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>编辑报表

可以像对任何其他 [Azure Monitor 工作簿](../visualize/workbooks-overview.md)一样编辑报表。 您可以根据团队的需求自定义查询或可视化对象。

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" 选择 &quot;编辑&quot; 按钮以将可视化效果更改为饼图的 GIF。" lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

查询可以在 [Log Analytics](../logs/log-analytics-overview.md) 中运行，并在其他报表或仪表板中使用。 删除参数限制并重新使用核心查询。

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" 日志查询的 GIF。" lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>访问和共享

可以将报表与团队、领导团队共享，或将其固定到仪表板以便进一步使用。 用户需要对存储实际工作簿的 Application Insights 资源具有读取权限/访问权限。

:::image type="content" source="./media/sla-report/share.png" alt-text=" 共享此模板的屏幕截图。" lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>后续步骤

- [Log Analytics 查询优化提示](../logs/query-optimization.md)。
- 了解如何 [在工作簿中创建图表](../visualize/workbooks-chart-visualizations.md)。
- 了解如何通过 [可用性测试](monitor-web-app-availability.md)监视网站。