---
title: 商业市场中合作伙伴中心分析的“摘要”仪表板
description: 了解如何访问聚合数据的图表、趋势和值，这些内容将合作伙伴中心的“摘要”仪表板中的活动汇总在一起。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462978"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的“摘要”仪表板

本文提供合作伙伴中心的“摘要”仪表板的相关信息。 此仪表板显示了聚合值的图表、趋势和值，这些内容汇总了你的产品/服务的市场活动。

若要访问合作伙伴中心的 "摘要" 仪表板，请在 "**商用 Marketplace** " 下选择 "**[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **摘要**"。

>[!NOTE]
> 有关分析术语的详细定义，请参阅 [商业市场分析术语和常见问题](./analytics-faq.md)。

## <a name="summary-dashboard"></a>“摘要”仪表板

"摘要" 仪表板概述了 Azure Marketplace 和 Microsoft AppSource 提供的业务性能。 该仪表板提供了以下内容的全面概述：

- 客户的订单
- 客户
- 客户对产品/服务的使用情况
- Azure Marketplace 和 AppSource 中客户的页面访问

## <a name="elements-of-the-summary-dashboard"></a>“摘要”仪表板的元素

下面各部分介绍了如何使用“摘要”仪表板以及如何读取数据。

### <a name="month-range"></a>月份范围

可以在每个页面的右上角找到 "月份范围" 选择。 通过选择基于过去3、6或12个月的月份范围或选择最长持续时间为12个月的自定义月份范围，自定义 **摘要** 页图形的输出。 默认月份范围 (计算期间) 为六个月。

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="说明 &quot;摘要&quot; 仪表板上的月度范围选项。":::

> [!NOTE]
> 可视化小组件中的所有度量值和导出报表都服从用户选择的计算时间段。

### <a name="orders-widget"></a>订单小组件

"摘要" 仪表板上的 "订单" 小组件显示了所有基于 transact-sql 的产品/服务的当前订单。 "订单" 小组件显示所选计算期间内 (排除 "取消" 订单) 的所有采购订单的计数和趋势。 百分比值 **订单** 表示所选计算期间的增长量。

[![说明 "摘要" 仪表板上的 "订单" 小组件。](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


还可以通过选择小组件左下角的 "订单" " **仪表板** " 链接来跳到 "订单" 报表。

### <a name="customers-widget"></a>客户小组件

"**摘要**" 仪表板的 "**客户**" 小组件显示在所选计算期间内已获得产品/服务的客户总数。 "客户" 小组件显示活动 (总数的计数和趋势，包括在所选计算期间内 (排除改动 customers) 的新的和现有的) 客户。 " **客户** " 下的百分比值表示所选计算期间的增长量。

[![说明 "摘要" 仪表板上的 "客户" 小组件。](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

还可以通过选择小组件左下角的 "客户" " **仪表板** " 链接来切换到 "详细客户" 报表。

### <a name="usage-widget"></a>使用小组件

"**摘要**" 仪表板的 "**使用情况**" 小组件表示 (VM) 产品/服务的所有 Azure 虚拟机的总规范化和原始使用小时数。 "使用情况" 小组件显示所选计算期间的总使用时间的计数和趋势。

使用情况摘要表显示客户购买的所有套餐的客户使用小时数。

- 规范化使用小时数定义为按 VM 核心数规范化的使用小时数（[VM 核心数] x [原始使用小时数]）。 指定为“SHAREDCORE”的 VM 使用 1/6（或 0.1666）作为 [VM 核心数] 的乘数。
- 原始使用时间定义为 Vm 的运行时间（以小时为单位）。

低于总使用时间的百分比值表示所选计算期间的使用时间的增长量。

[![说明 "摘要" 仪表板上的使用小组件。](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

还可以通过选择小组件左下角的 " **使用情况仪表板** " 链接来跳到使用情况报告。

### <a name="marketplace-insights"></a>Marketplace 见解

Marketplace Insights 显示在 Azure Marketplace 和 AppSource 中访问你的产品/服务的用户的数量。 **页面访问计数** 显示了商业 marketplace web analytics 的摘要，使发布者能够度量商业 marketplace 在线商店（Microsoft AppSource 和 Azure marketplace）上列出的相应产品详细信息页的客户参与情况。 此小组件显示在所选计算期间内总页面访问量的计数和趋势。

[![说明 "摘要" 仪表板上的 "页面访问计数" 小组件。](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

你还可以通过选择小组件左下角的 " **marketplace insights 仪表板** " 链接来前往 marketplace insights 报表。

### <a name="geographical-spread"></a>地理分布

对于选定的计算期间，热度地图显示了 "地域" 维度的 "客户总数"、"订单" 和 "已规范化使用小时数"。

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="说明 &quot;摘要&quot; 仪表板上的 &quot;国家/地区&quot; 小组件。":::

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图有一个补充网格，用于查看特定位置的客户计数、订单计数和规范化使用时间的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 通过选择地图中的 " **主页** " 按钮，还原到原始视图。

> [!TIP]
> 您可以使用任何小组件右上角的下载图标来下载数据。 您可以通过选择 "拇指 up" 或 "大拇指向下" 图标来提供每个小组件的反馈。

## <a name="next-steps"></a>后续步骤

- 有关商业应用商店中提供的分析报表的概述，请参阅 [在合作伙伴中心访问商业市场的分析报表](./partner-center-portal/analytics.md)。
- 有关采用图形和可下载格式显示的订单的信息，请参阅[商业市场分析中的“订单”仪表板](orders-dashboard.md)。
- 有关虚拟机 (VM) 产品/服务使用情况和按流量计费指标的信息，请参阅[商业市场分析中的“使用情况”仪表板](usage-dashboard.md)。
- 有关客户的详细信息（包括增长趋势），请参阅[商业市场分析中的“客户”仪表板](customer-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看有关 Azure Marketplace 和 AppSource 产品/服务的客户反馈的合并视图，请参阅 [合作伙伴中心的分级 & 审核分析仪表板](./partner-center-portal/ratings-reviews.md)。
- 有关商业 marketplace 分析的常见问题以及针对数据术语的综合性字典，请参阅 [商业市场分析术语和常见问题](./analytics-faq.md)。