---
title: 商业市场中合作伙伴中心分析的“摘要”仪表板
description: 了解如何访问聚合数据的图表、趋势和值，这些内容将合作伙伴中心的“摘要”仪表板中的活动汇总在一起。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 08/06/2021
ms.openlocfilehash: b9b3a1e651e6207cc037ab8e4c70f81a176c3d82
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747265"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的“摘要”仪表板

本文提供合作伙伴中心的“摘要”仪表板的相关信息。 此仪表板显示了聚合值的图表、趋势和值，这些内容汇总了你的产品/服务的市场活动。

>[!NOTE]
> 有关分析术语的详细定义，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。

## <a name="summary-dashboard"></a>“摘要”仪表板

[“摘要”仪表板](https://go.microsoft.com/fwlink/?linkid=2165765)展现了 Azure 市场和 Microsoft AppSource 产品/服务的业务绩效。 此仪表板对以下内容进行了全面的概述：

- 客户订单
- 客户
- 客户对产品/服务的使用情况
- 客户在 Azure 市场和 AppSource 中的页面访问量

## <a name="elements-of-the-summary-dashboard"></a>“摘要”仪表板的元素

下面各部分介绍了如何使用“摘要”仪表板以及如何读取数据。

若要访问合作伙伴中心的“摘要”仪表板，请在“商业市场”下选择[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)  > “摘要”。

### <a name="month-range"></a>月份范围

可在每页右上角找到月份范围选择。 基于过去 3 个月、6 个月或 12 个月选择一个月份范围，或者选择最长时段为 12 个月的自定义月份范围，以便自定义“摘要”页面图形的输出。 默认月份范围（计算期间）为 6 个月。

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="说明“摘要”仪表板中的月份范围选项。":::

> [!NOTE]
> 可视化小组件和导出报表中的所有指标遵循用户选择的计算周期。

### <a name="orders-widget"></a>“订单”小组件

“摘要”仪表板上的“订单”小组件显示了所有基于事务处理的产品/服务的当前订单。 “订单”小组件显示了所选计算周期内所有已购订单（不包括已取消的订单）的计数和趋势。 “订单”百分比值表示所选计算周期内的增长量。

[![说明“摘要”仪表板中的“订单”小组件。](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


你也可以选择小组件左下角的“订单仪表板”链接，转到“订单”报表。

### <a name="customers-widget"></a>“客户”小组件

“摘要”仪表板的“客户”小组件显示了所选计算周期内已获得产品/服务的客户总数。 “客户”小组件显示了所选计算周期内活跃客户（包括新客户和现有客户，不包括流失的客户）总数的计数和趋势。 “客户”百分比值表示所选计算周期内的增长量。

[![说明“摘要”仪表板中的“客户”小组件。](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

你也可以选择小组件左下角的“客户仪表板”链接，转到详细的“客户”报表。

### <a name="usage-widget"></a>“使用情况”小组件

“摘要”仪表板的“使用情况”小组件表示所有 Azure 虚拟机 (VM) 产品/服务的规范化使用和原始使用总小时数。 “使用情况”小组件显示所选计算周期内总使用时间的计数和趋势。

使用情况摘要表显示客户购买的所有套餐的客户使用小时数。

- 规范化使用小时数定义为按 VM 核心数规范化的使用小时数（[VM 核心数] x [原始使用小时数]）。 指定为“SHAREDCORE”的 VM 使用 1/6（或 0.1666）作为 [VM 核心数] 的乘数。
- 原始使用时间定义为 VM 已运行的时间量（以小时为单位）。

总使用小时下面的百分比值表示所选计算周期内使用小时数的增长量。

[![说明“摘要”仪表板中的“使用情况”小组件。](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

你也可以选择小组件左下角的“使用情况仪表板”链接，转到“使用情况”报表。

### <a name="marketplace-insights"></a>市场见解

“市场见解”显示访问 Azure 市场和 AppSource 中的产品/服务页的用户数量。 “页访问量计数”显示商业市场 Web 分析摘要，它可以让发布者衡量商业市场在线商店 Microsoft AppSource 和 Azure 市场中列出的相应产品详细信息页面的客户参与度。 此小组件显示所选计算周期内页面总访问量的计数和趋势。

[![说明“摘要”仪表板中的“页访问量计数”小组件。](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

你也可以选择小组件左下角的“市场见解仪表板”链接，转到“市场见解”报表。

### <a name="geographical-spread"></a>地理分布

对于所选的计算周期，热度地图会按地理维度显示客户总数、订单总数以及规范化使用总小时数。

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="说明“摘要”仪表板中的“国家/地区分布”小组件。":::

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图提供一个补充性的网格，用于查看特定位置的客户计数、订单计数和规范化使用时间的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 选择地图上的“主页”按钮可还原为原始视图。

> [!TIP]
> 可以使用任意小组件右上角的下载图标来下载数据。 可以通过选择“赞成”或“反对”图标来提供对每个小组件的反馈。

## <a name="next-steps"></a>后续步骤

- 若要大致了解商业市场内可用的分析报表，请参阅[在合作伙伴中心访问商业市场的分析报表](analytics.md)。
- 有关采用图形和可下载格式显示的订单的信息，请参阅[商业市场分析中的“订单”仪表板](orders-dashboard.md)。
- 有关虚拟机 (VM) 产品/服务使用情况和按流量计费指标的信息，请参阅[商业市场分析中的“使用情况”仪表板](usage-dashboard.md)。
- 有关客户的详细信息（包括增长趋势），请参阅[商业市场分析中的“客户”仪表板](customer-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](downloads-dashboard.md)。
- 若要查看客户对 Azure 市场和 AppSource 中产品/服务的反馈的综合视图，请参阅[合作伙伴中心的评分和评价分析仪表板](ratings-reviews.md)。
- 有关商业市场分析的常见问题解答以及数据术语的综合词典，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。
