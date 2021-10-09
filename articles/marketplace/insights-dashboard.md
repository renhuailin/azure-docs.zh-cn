---
title: 商业市场分析中的“市场见解”仪表板
description: 在合作伙伴中心访问市场 Web 分析摘要，它可以让你衡量客户在 Microsoft AppSource 和 Azure 市场中的参与度。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: a8ee69e0c9c196f42a733c4cb6c3079228238b21
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082911"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的“市场见解”仪表板

本文提供有关合作伙伴中心内的市场见解仪表板的信息。 此仪表板显示商业市场 Web 分析摘要，它可以让发布者衡量商业市场在线商店 Microsoft AppSource 和 Azure 市场中列出的相应产品详细信息页面的客户参与度。

有关分析术语的详细定义，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。

## <a name="marketplace-insights-dashboard"></a>市场见解仪表板

[“市场见解”仪表板](https://go.microsoft.com/fwlink/?linkid=2165936)概述了 Azure 市场和 AppSource 产品/服务的业务性能。 此仪表板对以下内容进行了全面的概述：

- 页面访问次数趋势
- 行动号召次数趋势
- 针对产品/服务、推荐域和市场活动 ID 的页面访问次数和行动号召次数
- 按地域分类的市场见解
- 市场见解详细信息表

“市场见解”仪表板提供点击流数据，不应将其与潜在顾客目标终结点中生成的潜在顾客关联。

> [!NOTE]
> 用户访问 Azure 市场或 AppSource 上的产品/服务和报告出现在合作伙伴中心这两者之间的最大延迟为 48 小时。

## <a name="access-the-marketplace-insights-dashboard"></a>访问“市场见解”仪表板

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在“主页”上，选择“见解”磁贴。

    [ ![说明了合作伙伴中心主页上的“见解”磁贴。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 在左侧菜单中，选择“市场见解”。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航栏中，选择“商业市场” > “分析” > “市场见解”  。

---

## <a name="elements-of-the-marketplace-insights-dashboard"></a>“市场见解”仪表板的元素

“市场见解”仪表板在两个单独的选项卡中显示 Azure 市场和 AppSource 的 Web 遥测详细信息。 以下部分介绍了如何使用“市场见解”仪表板以及如何读取数据。

### <a name="month-range"></a>月份范围

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

可在每页右上角找到月份范围选择。 通过基于过去 6 个或 12 个月选择一个月份范围，或者选择最长时段为 12 个月的自定义月份范围，自定义“市场见解”页面图的输出内容。 默认月份范围（计算期间）为 6 个月。

[ ![展示“市场见解”仪表板上的月份筛选器。](./media/insights-dashboard/marketplace-insights-filters.png) ](./media/insights-dashboard/marketplace-insights-filters.png#lightbox)

#### <a name="current-view"></a>[当前视图](#tab/current-view)

可在每页右上角找到月份范围选择。 通过基于过去 6 个或 12 个月选择一个月份范围，或者选择最长时段为 12 个月的自定义月份范围，自定义“市场见解”页面图的输出内容。 默认月份范围（计算期间）为 6 个月。

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="展示“市场见解”仪表板上的月份筛选器。":::

---

> [!NOTE]
> 可视化小组件和导出报表中的所有指标都使用用户选择的计算期间。

### <a name="page-visits-trends"></a>页面访问次数趋势

市场见解“访问者数量”图表显示所选计算期间的“页面访问次数”和“唯一访问者数量” 。

**页面访问次数**：此数字表示所选计算期间内产品/服务列表页（产品详细信息页）上不同用户会话的计数。 红色和绿色百分比指标表示页面访问次数的增长百分比。 趋势图表示逐月的页面访问次数。

**唯一访问者数量**：此数字表示 Azure 市场和 AppSource 中的产品/服务在所选计算期间的不同访问者计数。 访问过一个或多个产品详细信息页面的访问者将被视为一名唯一访问者。

[![展示“市场见解”仪表板上的“访问者数量”图表。](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>行动号召次数趋势

此数字表示在产品/服务列表页（产品详细信息页）上完成的“行动号召”按钮单击计数。 当用户选择“立即获取”、“免费试用”、“与我联系”或“体验版”按钮时，其操作会计入“行动号召单击次数”   。 “同意”表示客户提供给 Microsoft 或合作伙伴的同意的总点击次数，等于为产品/服务获取的客户数。 以下两个示例显示了“同意”点击的显示位置：

:::image type="content" source="./media/insights-dashboard/consent-screen.png" alt-text="说明选择同意按钮的位置。":::

下图显示了“CTA”与“同意”指标： 

:::image type="content" source="./media/insights-dashboard/consent-given-graph.png" alt-text="显示包含“行动号召”与“同意”的示例图。":::

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>针对产品/服务、推荐域和市场活动 ID 的页面访问次数和行动号召次数

#### <a name="offers"></a>产品

选择特定的产品/服务即可在图表上查看页面访问次数、行动号召和同意点击次数的每月趋势。

:::image type="content" source="./media/insights-dashboard/offers-funnel-graph.png" alt-text="显示产品/服务的示例漏斗图。":::

#### <a name="referral-domains"></a>推荐域

选择特定的推荐域会在图表右侧显示页面访问次数、行动号召和同意点击次数的每月趋势。 此外，还有一个“平台 - 网站和客户端”列，仅为 AppSource 产品/服务显示。 漏斗图视图描述了图表上页面视图、行动号召和同意点击次数之间的转换率。

:::image type="content" source="./media/insights-dashboard/referral-domains-funnel-graph.png" alt-text="显示推荐域的示例漏斗图。":::

#### <a name="campaign-ids"></a>市场活动 ID

应该可以通过选择特定的市场活动 ID 来了解市场活动的成功率。 对于每个市场活动，你应该可以在图表上查看页面访问次数、行动号召和同意点击次数的每月趋势。

:::image type="content" source="./media/insights-dashboard/campaign-id-funnel-graph.png" alt-text="展示“市场见解”仪表板上的“市场活动”图表。":::

### <a name="marketplace-insights-by-geography"></a>按地域分类的市场见解

对于所选的计算期间，热度地图显示页面访问次数、唯一访问者数量和行动号召单击次数 (CTA)。 地图上的由浅到深的颜色代表唯一访问者数量从低到高的值。 选择表中的记录以放大国家/地区。

[ ![展示“市场见解”仪表板上的地理分布图。](./media/insights-dashboard/geographical-spread.png) ](./media/insights-dashboard/geographical-spread.png#lightbox)

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图有一个补充性的网格，可显示特定位置的客户计数、订单计数和规范化使用时间的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 选择地图上的“主页”按钮可还原为原始视图。

### <a name="marketplace-insights-details-table"></a>市场见解详细信息表

此表提供页面访问次数以及所选产品/服务的页面按日期排序的行动号召单击次数的列表视图。

- 如果记录计数小于 1000，则可以将数据提取到 TSV 或 .CSV 文件中。
- 如果记录的条数超过 1000，则导出的数据在接下来的 30 天会异步放置在下载页面中。
- 按产品/服务名称和市场活动名称筛选数据，以显示你感兴趣的数据。

> [!TIP]
> 你可以使用任意小组件右上角的下载图标来下载数据。 可以通过单击“赞成”或“反对”图标来提供每个小组件的反馈。

| 用户界面中的<br>用户界面 | 属性名称 | 定义 | 编程式访问报表中的<br>列名称 |
| ------------ | ------------- | ------------- | ------------- |
| Date | 访问日期 | 在 Azure 市场和/或 AppSource 的产品/服务页上进行页面访问和/或生成 CTA 单击事件的日期。 | Date |
| 产品名称 | 产品名称 | 商业市场产品/服务的名称。 | OfferName |
| 推荐域 | 推荐域 | 从其进行了页面访问的推荐域的名称。 如果没有针对页面访问捕获任何推荐域，则对应的条目为“推荐域不存在”。 |  ReferralDomain |
| 国家/地区名称 | 国家/地区名称 | 从其进行了页面访问的国家/地区的名称。 | 国家/地区名称 |
| 页面访问次数 | 页面访问次数 | 与特定日期的产品/服务名称关联的页面访问次数。 | PageVisits |
| 立即获取 | 立即获取 | 特定日期的产品/服务页面上“立即获取”CTA 的单击次数。 | GetItNow |
| 与我联系 | 与我联系 | 特定日期的产品/服务页面上“与我联系”CTA 的单击次数。 | ContactMe |
| 体验版 | 体验版 | 特定日期的产品/服务页面上“体验版”CTA 的单击次数。 | TestDrive |
| 免费试用版 | 免费试用版 | 特定日期的产品/服务页面上“免费试用版”CTA 的单击次数。 | FreeTrial |
| 活动 | 营销活动的名称 | 能够理解 Web 遥测（页面访问和 CTA 点击量）。 | 活动 |
| 同意 | 同意 | 客户提供给 Microsoft 或合作伙伴的同意的总点击次数 | consentGivenCount |
| 平台 | 平台 | 指示网站或客户端（产品内存储），作为页面视图、CTA 或同意点击次数的源 | platforms |
| 不适用 | 站点 | 进行了页面访问或 CTA 点击的店面的名称。 可能的值包括：<br><ul><li>AZUREMARKETPLACE<li>APPSOURCE</ul> | 站点 |
|

## <a name="next-steps"></a>后续步骤

- 若要大致了解商业市场内可用的分析报表，请参阅[在合作伙伴中心访问商业市场的分析报表](analytics.md)。
- 若要了解以图形和可下载格式提供的订单，请参阅[商业市场分析中的“订单”仪表板](orders-dashboard.md)。
- 有关虚拟机 (VM) 产品/服务使用情况和按流量计费指标的信息，请参阅[商业市场分析中的“使用情况”仪表板](usage-dashboard.md)。
- 有关客户的详细信息（包括增长趋势），请参阅[商业市场分析中的“客户”仪表板](customer-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](downloads-dashboard.md)。
- 若要查看客户对 Azure 市场和 AppSource 中产品/服务的反馈的综合视图，请参阅[合作伙伴中心的评分和评价分析仪表板](ratings-reviews.md)。
- 有关商业市场分析的常见问题解答以及数据术语的综合词典，请参阅[商业市场分析术语和常见问题](analytics-faq.yml)。
