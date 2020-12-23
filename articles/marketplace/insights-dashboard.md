---
title: 商业市场分析中的“市场见解”仪表板
description: 访问合作伙伴中心的 marketplace web analytics 摘要，使你能够在 Microsoft AppSource 和 Azure Marketplace 中衡量客户参与情况。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 9089fee7565633b198043efeed929fc7446437d5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458289"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的“市场见解”仪表板

本文提供有关合作伙伴中心内的市场见解仪表板的信息。 此仪表板显示了商业 marketplace web analytics 的摘要，使发布者能够度量商业 marketplace 在线商店： Microsoft AppSource 和 Azure Marketplace 中列出的相应产品详细信息页的客户参与情况。

若要访问合作伙伴中心的 **marketplace insights** 仪表板，请在 "商用 marketplace" 下，选择 "**[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Marketplace Insights**"。

有关分析术语的详细定义，请参阅 [商业市场分析术语和常见问题](./analytics-faq.md)。

## <a name="marketplace-insights-dashboard"></a>市场见解仪表板

Marketplace Insights 仪表板概述了 Azure Marketplace 和 AppSource 的业务性能。 此仪表板提供了以下内容的全面概述：

- 页面访问趋势
- 调用操作趋势
- 针对产品/服务、引用域和活动 id 的页面访问和操作
- 地域见解
- 市场见解详细信息表

Marketplace Insights 仪表板提供点击流数据，不应与潜在客户目标终结点中生成的潜在顾客关联。

> [!NOTE]
> 用户访问 Azure Marketplace 上的产品/服务和合作伙伴中心的 AppSource 和报表之间的最大延迟为48小时。

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Marketplace Insights 仪表板的元素

Marketplace Insights 仪表板在两个单独的选项卡中显示 Azure Marketplace 和 AppSource 的 web 遥测详细信息。 以下部分介绍如何使用 Marketplace Insights 仪表板以及如何读取数据。

### <a name="month-range"></a>月份范围

可以在每个页面的右上角找到 "月份范围" 选择。 通过选择基于过去6个月或12个月的月份范围，或选择最长持续时间为12个月的自定义月份范围，自定义 **Marketplace Insights** 页面的输出。 默认月份范围 (计算期间) 为六个月。

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="说明 Marketplace Insights 仪表板上的月份筛选器。":::

> [!NOTE]
> 可视化小组件中的所有度量值和导出报表都服从用户选择的计算时间段。

### <a name="page-visits-trends"></a>页面访问趋势

Marketplace Insights **访问者** 图表显示所选计算期间的 _页面访问_ 数和 _唯一访问者_ 数。

**页面访问**：此数字表示产品/服务列表页上不同用户会话的计数 (产品详细信息页) 用于所选计算期间。 红色和绿色百分比指标表示页面访问的增长百分比。 趋势图表示逐月的页面访问次数。

**唯一访问者**：此数字表示在 Azure Marketplace 和 AppSource 中的产品/服务 () 的所选计算期间内的不同访问者计数。 访问过一个或多个产品详细信息页面的访问者将被视为一名唯一访问者。

[![说明 Marketplace Insights 仪表板上的 "访问者" 图表。](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>调用操作趋势

此数字表示 "产品详细信息" 页面)  (产品详细信息页的 "产品/服务列表" 页上的 "操作" 按钮的 **调用** 计数。 当用户选择 "**立即获取**"、"**免费试用**"、"**与我联系**" 或 "**测试驱动器**" 按钮时，将对 _操作_ 进行计数。

[![说明如何调用 Marketplace Insights 仪表板上的操作图。](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>针对产品/服务、引用域和活动 Id 的页面访问和操作

**引用域**：选择特定的引用域将显示每月的页面访问趋势，并在右侧的图表上调用操作。

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="说明 Marketplace Insights 仪表板上的参考域图表。":::

**产品/服务**：选择特定产品/服务，查看每月的页面访问趋势和右侧图表上的操作调用。

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="说明 Marketplace Insights 仪表板上的产品/服务别名图表。":::

**市场活动 id**：通过选择特定的市场活动 id，你应该能够理解市场活动的成功率。 对于每个市场活动，您应该能够看到页面访问的每月趋势和右侧图表上的操作调用。

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="说明 Marketplace Insights 仪表板上的活动图。":::

### <a name="marketplace-insights-by-geography"></a>地域见解

对于所选的计算期间，热度地图会显示页面访问数、唯一访问者，以及)  (的操作调用。 地图上的亮到暗色表示唯一访问者的最小值。 选择表中的一条记录以放大国家/地区。

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="说明 Marketplace Insights 仪表板上的地理分散图。":::

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图有一个补充网格，用于查看特定位置中客户计数、订单计数和规范化使用时间的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 通过选择地图中的 " **主页** " 按钮，还原到原始视图。

### <a name="marketplace-insights-details-table"></a>市场见解详细信息表

此表提供了页面访问的列表视图，以及按日期排序的所选产品页面的操作调用。

- 数据可以提取到。TSV 或。如果记录计数小于1000，则为 CSV 文件。
- 如果记录计数超过1000，则导出的数据将在接下来的30天内异步置于下载页中。
- 按产品名称和活动名称筛选数据，以显示你感兴趣的数据。

> [!TIP]
> 您可以使用任何小组件右上角的下载图标来下载数据。 可以通过单击 "滚动更新" 或 "拇指向下" 图标来提供每个小组件的反馈。

## <a name="next-steps"></a>后续步骤

- 有关商业应用商店中提供的分析报表的概述，请参阅 [在合作伙伴中心访问商业市场的分析报表](./partner-center-portal/analytics.md)。
- 有关以图形和可下载格式显示的订单的信息，请参阅 [商业 marketplace 分析中的 "订单" 仪表板](./orders-dashboard.md)。
- 对于虚拟机 (VM) 提供使用情况和计量计费指标，请参阅 [商业 marketplace 分析中的使用情况仪表板](./usage-dashboard.md)。
- 有关客户的详细信息（包括增长趋势），请参阅[商业市场分析中的“客户”仪表板](./customer-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看有关 Azure Marketplace 和 AppSource 产品/服务的客户反馈的合并视图，请参阅 [合作伙伴中心的分级 & 审核分析仪表板](./partner-center-portal/ratings-reviews.md)。
- 有关商业 marketplace 分析的常见问题以及针对数据术语的综合性字典，请参阅 [商业市场分析术语和常见问题](./analytics-faq.md)。