---
title: 商业市场分析中的合作伙伴中心订单仪表板 | Microsoft AppSource 和 Azure 市场
description: 了解如何访问有关商业市场产品/服务订单的分析报表（以图形格式和可下载格式提供）。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 08/06/2021
ms.openlocfilehash: 1cc7e76833ac710ae4bdd511efa3033f8cf8fed7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724298"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的“订单”仪表板

本文提供有关合作伙伴中心中的订单仪表板的信息。 此仪表板显示有关订单的信息，包括以图形格式和可下载格式提供的增长趋势。

>[!NOTE]
> 有关分析术语的详细定义，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。

## <a name="orders-dashboard"></a>订单仪表板

[“订单”仪表板](https://go.microsoft.com/fwlink/?linkid=2165914)显示所有服务型软件 (SaaS) 产品/服务的当前订单。 你可以查看以下各项的图形表示形式：

- 订单趋势
- 每席位和每站点的订单趋势
- 按产品/服务和 SKU 统计的订单
- 按地域统计的订单
- 详细订单表
- “订单页面”筛选器

> [!NOTE]
> 客户获取产品/服务与合作伙伴中心内进行报告之间的最大延迟为 48 小时。

## <a name="elements-of-the-orders-dashboard"></a>“订单”仪表板的元素

以下部分介绍如何使用“订单”仪表板以及如何读取数据。

若要访问合作伙伴中心的“订单”仪表板，请在“商业市场”下选择[“分析”](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) > “订单”。

### <a name="month-range"></a>月份范围

可在每页右上角找到月份范围选择。 基于过去 6 个或 12 个月选择一个月份范围，或者选择最长时段为 12 个月的自定义月份范围，以便自定义“订单”页面图形的输出。 默认月份范围（计算期间）为 6 个月。

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="展示“订单”仪表板上的月份筛选器。":::

> [!NOTE]
> 可视化小组件和导出报表中的所有指标遵循用户选择的计算周期。

### <a name="orders-trend"></a>订单趋势

在此部分可以找到“订单”图表，其中显示了在所选计算周期内活动订单和已取消订单的趋势。 指标和增长趋势以折线图表示，可以通过将鼠标悬停在图表的折线上方来显示每个月的值。 小组件中“订单”指标下面的百分比值表示所选计算周期内的增长量或下降量。

有两个“订单”计数器：“活动”和“已取消”。 

- “活动”等于所选日期范围内客户当前正在使用的订单数。
- “已取消”等于以前已购买的，但后来在所选日期范围内取消的订单数。

[![展示“订单”仪表板上的“订单”小组件，其中显示了活动订单和已取消订单的趋势。](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>每席位和每站点的订单趋势

“每席位和基于站点的订单”折线图表示客户购买的每站点 SaaS 和每席位 SaaS 订单的指标与趋势（此图表包括已取消的订单）。

[![展示“订单”仪表板上的“订单”小组件，其中显示了每席位和每站点的订单趋势。](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

SaaS 产品/服务可对每个计划使用两种定价模型中的一种：统一费率（基于站点）或按用户（基于席位）。

- **统一费率** - 支持按照一种月度或年度统一费率价格访问产品/服务。 这有时称为基于站点的定价。
- **按用户** - 支持按照一种根据可访问产品/服务或占用席位的用户数确定的价格访问产品/服务。 使用这种基于使用量的模型，可以设置计划支持的最小和最大用户数。 可以根据用户数创建多个计划来配置不同的价格点。 这些字段是可选的。 如果保持未选中状态，则用户数将被解释为无限制（最小值为 1，最大值为服务可支持的数量）。 可将这些字段作为计划更新的一部分进行编辑。
- **计量式计费**：对统一费率定价的补充。 使用此定价模型可以选择性地定义计量计划，这些计划使用市场计量服务 API 向客户收取统一费率不涵盖的使用量的费用。

有关席位、站点和计量式计费的更多详细信息，请参阅[如何为商业市场规划 SaaS 产品/服务](plan-saas-offer.md)。

### <a name="orders-by-offers-and-skus"></a>按产品/服务和 SKU 统计的订单

“按产品/服务和 SKU 统计的订单”图表显示所有产品/服务的度量值和趋势：

- 排名靠前的产品/服务会显示在图中，而其余产品/服务会分组为“其余全部”。
- 可以选择图例中的特定产品/服务，以便仅在图中显示该产品/服务和关联的 SKU。
- 将鼠标悬停在图中的切片上将显示与所有产品/服务的总订单数相比该产品/服务的订单数和百分比。
- “按产品/服务趋势统计的订单”显示逐月增长趋势。 月份列表示按产品/服务名称统计的订单数。 折线图显示在 z 轴上绘制的增长百分比趋势。

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="展示“订单”仪表板上的“按产品/服务统计的订单”图表。":::

你可以选择任何产品/服务，最多可以选择该产品/服务的三个 SKU，以查看产品/服务、SKU 和席位的每月趋势。

### <a name="orders-by-geography"></a>按地域统计的订单

对于所选的计算周期，热度地图会显示订单总数，以及某个地理位置新增订单的增长率百分比。  地图上的由浅到深的颜色代表客户计数从低到高的值。 在表中选择一条记录可以放大相应的国家或地区。

[![展示“订单”仪表板上的“地理分布”图表。](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图提供一个补充性的网格，用于查看特定位置的客户计数、订单计数和规范化使用时间的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 选择地图上的“主页”按钮可还原为原始视图。

### <a name="orders-details-table"></a>订单详细信息表

“订单详细信息”表显示按购置日期排序的前 1,000 份订单的编号列表。

- 网格中的每列都可进行排序。
- 如果记录计数小于 1,000，可将数据提取到 .CSV 或 .TSV 文件。
- 如果记录数超过 1,000，则接下来的 30 天，导出的数据都会异步放置到下载页中。
- 对“订单详细信息”表应用筛选器可以仅显示你感兴趣的数据。 可按国家/地区、Azure 许可证类型、商业市场许可证类型、产品/服务类型、订单状态、免费试用版、商业市场订阅 ID、客户 ID 和公司名称进行筛选。
- 当受保护的客户下单时，“订单详细数据”中的信息将会掩码 (************)。

***表 1：数据术语字典***

| 用户界面中的<br>用户界面 | 属性名称 | 定义 | 编程式访问报表中的<br>列名称 |
| ------------ | ------------- | ------------- | ------------- |
| 市场订阅 ID | 市场订阅 ID | 与客户用来购买商业市场产品/服务的 Azure 订阅关联的唯一标识符。 对于基础结构产品/服务，这是客户的 Azure 订阅 GUID。 对于 SaaS 产品/服务，此项显示为零，因为 SaaS 购买不需要 Azure 订阅。 | 市场订阅 ID |
| MonthStartDate | MonthStartDate | 月份开始日期表示购买月份。 格式为 yyyy-mm-dd。 | MonthStartDate |
| 产品/服务类型 | 产品/服务类型 | 商业市场产品/服务的类型。 | OfferType |
| Azure 许可证类型 | Azure 许可证类型 | 购买 Azure 的客户使用的许可协议类型。 也称为“渠道”。 可能的值包括：<ul><li>[云解决方案提供商](cloud-solution-providers.md)</li><li>Enterprise</li><li>通过经销商购买产品/服务的企业</li><li>即用即付</li><li>GTM</li></ul> | AzureLicenseType |
| 市场许可证类型 | 市场许可证类型 | 商业市场产品/服务的计费方式。 不同的值包括：<ul><li>通过 Azure 计费</li><li>自带许可</li><li>免费</li><li>Microsoft 充当经销商</li></ul> | MarketplaceLicenseType |
| SKU | SKU | 与产品/服务关联的计划 | SKU |
| 客户所在国家/地区 | 客户所在国家/地区 | 客户提供的国家/地区名称。 此处的国家/地区可能不同于客户的 Azure 订阅中的国家/地区。 | CustomerCountry |
| 是预览版 SKU | 是预览版 SKU | 此值告知是否你已将 SKU 标记为“预览版”。 如果已相应地标记了 SKU，则值为“是”，并且只有你授权的 Azure 订阅可以部署和使用此映像。 如果 SKU 未标识为“预览版”，则值为“否”。 | IsPreviewSKU |
| 订单 ID | 订单 ID | 商业市场服务客户订单的唯一标识符。 基于虚拟机用量的产品/服务不会与订单相关联。 | OrderId |
| Order Quantity | Order Quantity | 与活动订单的订单 ID 关联的资产数 | OrderQuantity |
| 云实例名称 | 云实例名称 | 部署 VM 的 Microsoft 云。 | CloudInstanceName |
| 是新客户 | 是新客户 | 该值标识新客户是否为第一次购买你的一个或多个产品/服务。 如果在“获取日期”的相同日历月份内，则值为“是”。 如果客户在报告的日历月份之前购买了你的任何产品/服务，则值为“否”。 | IsNewCustomer |
| 订单状态 | 订单状态 | 上次刷新数据时商业市场订单的状态。 | OrderStatus |
| 订单取消日期 | 订单取消日期 | 取消商业市场订单的日期。 | OrderCancelDate |
| 客户公司名称 | 客户公司名称 | 客户提供的公司名称。 此处的名称可能不同于客户的 Azure 订阅中的名称。 | CustomerCompanyName |
| 订单购买日期 | 订单购买日期 | 创建商业市场订单的日期。 格式为 yyyy-mm-dd。 | OrderPurchaseDate |
| 产品名称 | 产品名称 | 商业市场产品/服务的名称。 | OfferName |
| 试用结束日期 | 试用结束日期 | 此订单的试用期将要结束或已结束的日期。 | TrialEndDate |
| 客户 ID | 客户 ID | 分配给客户的唯一标识符。 一个客户可以有零个或零个以上的 Azure 市场订阅。 | CustomerId |
| 计费帐户 ID | 计费帐户 ID | 针对其生成账单的帐户的标识符。 将“计费帐户 ID”映射到 customerID，以将“付款交易报表”与“客户”、“订单”和“使用情况报表”关联起来。 | BillingAccountId |
| AssetCount | 资产计数 | 与订单 ID 关联的资产数。 | 不推荐使用 |
| 不可用 * | TermStartDate | 指示订单有效期的开始日期。 | TermStartDate |
| 不可用 * | TermEndDate | 指示订单有效期的结束日期。 | TermEndDate |
| 不可用 * | purchaseRecordId | 与订单相关的购买记录的标识符。 | purchaseRecordId |
| 不可用 * | purchaseRecordLineItemId | 与此订单相关的购买记录行项的标识符。 | purchaseRecordLineItemId |
| 不可用 * | EstimatedCharges | 在纳税之前，将向客户收取的所有订单单元的总价。 在含税的国家/地区，此价格包含税款，在不含税的国家/地区，此价格不含税款。 | EstimatedCharges |
| 不可用 * | 货币 | 产品/服务的计费货币 | 货币 |
| 不可用 * | HasTrial | 表示产品/服务是否有试用期。 | HasTrial |
|||||

`*` 这些新字段当前在 ISVOrderV2 数据集中提供，可通过编程方式进行访问。

### <a name="orders-page-filters"></a>“订单页面”筛选器

“订单”页筛选器在“订单”页级别应用。 可以选择一个或多个筛选器根据所选查看条件以及要在“详细订单数据”网格/导出结果中看到的数据来呈现图表。 筛选器将应用于根据你在订单页右上角选择的月份范围提取的数据。

> [!TIP]
> 可以使用任意小组件右上角的下载图标来下载数据。 可以通过单击“赞成”或“反对”图标来提供每个小组件的反馈。

## <a name="next-steps"></a>后续步骤

- 若要大致了解商业市场内可用的分析报表，请参阅[在合作伙伴中心访问商业市场的分析报表](analytics.md)。
- 有关为产品/服务汇总市场活动的聚合数据的图表、趋势和值，请参阅[商业市场分析中的“摘要”仪表板](./summary-dashboard.md)。
- 有关采用图形和可下载格式显示的订单的信息，请参阅[商业市场分析中的“订单”仪表板](orders-dashboard.md)。
- 有关虚拟机 (VM) 产品/服务使用情况和按流量计费指标的信息，请参阅[商业市场分析中的“使用情况”仪表板](./usage-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](downloads-dashboard.md)。
- 若要查看客户对 Azure 市场和 AppSource 中产品/服务的反馈的综合视图，请参阅[合作伙伴中心的评分和评价分析仪表板](ratings-reviews.md)。
- 有关商业市场分析的常见问题解答以及数据术语的综合词典，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。
