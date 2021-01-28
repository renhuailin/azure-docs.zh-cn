---
title: 商业 marketplace 分析中的使用情况仪表板 |Azure Marketplace
description: 了解如何访问发布到 Azure Marketplace 的产品/服务的所有使用情况和计量计费指标。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 9f798751065d4889569f4b798ac864342f3e54ef
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934399"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的“使用情况”仪表板

本文提供了有关合作伙伴中心中的使用情况仪表板的信息。 此仪表板显示所有虚拟机 (VM) 在三个不同的选项卡中提供规范化使用情况、原始使用情况和计量计费指标： VM 标准化使用情况、VM 原始使用量和按流量计费的使用情况。

若要访问合作伙伴中心的 "使用情况" 仪表板，请在 "**商用 Marketplace**" 下选择 "**[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **使用量**"。

>[!NOTE]
> 有关分析术语的详细定义，请参阅 [商业市场分析术语和常见问题](./analytics-faq.md)。

## <a name="usage-dashboard"></a>使用情况仪表板

"**分析**" 菜单中的 "**使用情况**" 仪表板显示所有软件即服务 (SaaS) 产品的当前订单。 你可以查看以下各项的图形表示形式：

- 使用趋势
- 按产品/服务的规范化使用情况
- 按其他维度标准化使用： VM 大小、销售渠道和产品/服务类型
- 按地域的使用情况
- 详细使用情况表
- 订单页筛选器

> [!NOTE]
> 伙伴中心中的使用事件生成和报告之间的最大延迟为48小时。

## <a name="elements-of-the-usage-dashboard"></a>使用情况仪表板的元素

以下部分介绍如何使用 "使用情况" 仪表板以及如何读取数据。

### <a name="month-range"></a>月份范围

可以在每个页面的右上角找到 "月份范围" 选择。 通过选择基于过去6或12个月的月份范围或选择最长持续时间为12个月的自定义月份范围，自定义 **使用情况** 页面图形的输出。 默认月份范围 (计算期间) 为六个月。

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="说明使用情况仪表板上的月份筛选器。":::

### <a name="usage-trend"></a>使用趋势

在本部分中，你将找到你的客户在所选计算期间内使用的所有产品/服务的总使用时间和趋势。 指标和增长趋势由折线图表示。 通过将鼠标悬停在图表上的线条上，显示每个月的值。 小组件中使用指标下的百分比值表示所选计算期间的增长量或下降量。

使用时间有两种表示形式： VM 标准化使用量和 VM 原始使用量。

- 规范化使用小时数定义为按 VM 核心数规范化的使用小时数（[VM 核心数] x [原始使用小时数]）。 指定为“SHAREDCORE”的 VM 使用 1/6（或 0.1666）作为 [VM 核心数] 的乘数。
- 原始使用时间定义为 Vm 的运行时间（以小时为单位）。

[![说明使用情况仪表板上的规范化使用量和原始使用情况数据。](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>按产品/服务的规范化使用情况

本部分提供 Azure Marketplace 中基于使用情况的产品/服务的总使用时间和趋势。 下面介绍了 "产品/服务的规范化使用情况" 图表。

- " **按产品划分的规范化使用量** " 柱形图显示了前5个产品/服务根据所选计算期间的规范化使用时间的细分。 前五个产品/服务将显示在一个关系图中，其余部分将在 " **全部 rest** " 类别中进行分组。
- 堆积柱形图显示所选日期范围的每月增长趋势。 月份列表示套餐的使用小时数，以及相应月份的最高使用小时数。 折线图描绘在辅助 Y 轴上绘制的增长百分比趋势。
- 可以在图例中选择特定产品/服务以在图形中仅显示这些产品/服务。

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="阐释了使用情况仪表板上的规范化使用情况提供数据。":::

你可以选择任何产品/服务，并且最多可以选择该产品/服务的三个 Sku，以查看该产品/服务的每月使用趋势和所选 Sku。

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="说明使用情况仪表板上的规范化使用情况提供和 sku 数据。":::

### <a name="orders-by-offers-and-skus"></a>按产品/服务和 Sku 排序

" **按产品/服务** 列出的订单" 和 "SKU" 图表显示了所有产品的度量值和趋势。 注意以下事项：

- 最上面的产品/服务将显示在图中，其余的产品/服务将被分组为 **rest**。
- 可以在图例中选择特定产品/服务以在图形中仅显示这些产品/服务。
- 将鼠标悬停在图中的某一切片上将显示与所有产品/服务的订单总数相比，该产品/服务的订单数和百分比。
- “按产品/服务趋势统计的订单”显示逐月增长趋势。 月份列表示按产品/服务名称统计的订单数。 折线图显示在 z 轴上绘制的增长百分比趋势。

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="说明使用情况仪表板上的 &quot;订单&quot; 和 &quot;Sku&quot; 图表。":::

你可以选择任何产品/服务，并且最多可选择三个 Sku 来查看产品/服务、Sku 和座位的每月月趋势。

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="说明使用情况仪表板上的 &quot;订单&quot; 和 &quot;Sku&quot; 图表。显示产品/服务趋势、Sku 趋势和座位趋势。":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>按其他维度标准化的用量： VM 大小、销售渠道和产品/服务类型

维度有三个选项卡： VM 大小、销售渠道和产品/服务类型。 可以查看每个维度的使用情况指标和每月月趋势。

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="说明使用情况仪表板上的规范化使用量其他维度图表。":::

### <a name="usage-by-geography"></a>按地域的使用情况

对于所选的计算期间，热度地图显示对地理维度的总使用量。 地图上的由浅到深的颜色代表客户计数从低到高的值。 选择表中的一条记录以放大国家/地区。

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="说明使用情况仪表板上的规范化使用情况 &quot;国家/地区&quot; 图表。":::

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图有一个补充网格，用于查看特定位置中客户计数、订单计数和规范化使用时间的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 通过选择地图中的 " **主页** " 按钮，还原到原始视图。

### <a name="usage-details-table"></a>使用情况详细信息表

" **使用情况详细信息** " 表显示按使用情况排序的前1000个使用记录的编号列表。 注意以下事项：

- 网格中的每列都可进行排序。
- 数据可以提取到。TSV 或。如果记录计数小于1000，则为 CSV 文件。
- 如果记录计数超过1000，则导出数据将异步放置在将在未来30天内可用的下载页面。
- 将筛选器应用于 **详细使用情况数据** 以仅显示你感兴趣的数据。 按国家/地区、销售渠道、Marketplace 许可证类型、使用类型、产品名称、产品类型、免费试用版、Marketplace 订阅 ID、客户 ID 和公司名称筛选数据。

_**表1：数据字典**_

| 列名称 | 属性名称 | 定义 |
| ------------ | ------------- | ------------- |
| Marketplace 订阅 Id | 市场订阅 ID | 与客户用于购买商业 marketplace 产品/服务的 Azure 订阅相关联的唯一标识符。 该 ID 以前称为“Azure 订阅 GUID”。 |
| MonthStartDate | 月开始日期 | 月开始日期表示采购月份。 |
| 产品/服务类型 | 产品/服务类型 | 商业 marketplace 产品/服务的类型。 |
| Azure 许可证类型 | Azure 许可证类型 | 购买 Azure 的客户使用的许可协议类型。 也称为通道。 可能的值为：<ui><li>云解决方案提供商</li><li>企业</li><li>企业通过分销商</li><li>即付即用</li></ul> |
| 市场许可证类型 | 市场许可证类型 | 商业应用商店的计费方法。 可能的值为：<ul><li>通过 Azure 计费</li><li>自带许可</li><li>免费</li><li>Microsoft as 经销商</li></ul> |
| SKU | SKU | 与产品/服务关联的计划。 |
| 客户所在国家/地区 | 客户所在国家/地区 | 客户提供的国家/地区名称。 国家/地区可能不同于客户的 Azure 订阅中的国家/地区。 |
| 是预览版 SKU | 是预览版 SKU | 此值显示是否已将 SKU 标记为 "预览"。 如果已相应地标记了 SKU，则值为“是”，并且只有你授权的 Azure 订阅可以部署和使用此映像。 如果 SKU 未标识为“预览版”，则值为“否”。 |
| SKU 计费类型 | SKU 计费类型 | 与产品/服务中的每个 SKU 关联的计费类型。 可能的值为：<ul><li>免费</li><li>已付</li></ul> |
| IsInternal | 已放弃 | 已放弃 |
| VM 大小 | 虚拟机大小 | 对于基于 VM 的产品/服务类型，此实体表示与产品/服务的 SKU 关联的 VM 大小。 |
| 云实例名称 | 云实例名称 | 部署 VM 的 Microsoft 云。 |
| ServicePlanName | 已放弃 | 弃用 (与 SKU) 相同的定义 |
| 产品名称 | 产品名称 | 商业应用商店的名称。 |
| DeploymentMethod | 已放弃 | 弃用 (与产品/服务类型相同的定义) 
 |
| 客户公司名称 | 客户公司名称 | 客户提供的公司名称。 名称可能与客户的 Azure 订阅中的名称不同。 |
| 使用日期 | 使用日期 | 基于使用情况的资产的使用事件生成日期。 |
| IsMultisolution | 为 Multisolution | 表示产品/服务是否为 Multisolution 产品/服务类型。 |
| 是新客户 | 已放弃 | 已放弃 |
| 核心大小 | 核心大小 | 与基于 VM 的产品/服务关联的内核数。 |
| 使用情况类型 | 使用情况类型 | 指示与产品/服务关联的使用情况事件是否是以下项之一：<ul><li>规范化使用情况</li><li>原始使用情况</li><li>计量使用</li></ul> |
| 试用结束日期 | 试用结束日期 | 此订单的试用期将要结束或已结束的日期。 |
| 客户货币 (CC) | 客户币种 | 客户用于商业应用商店交易的货币。 |
| 价格 (CC) | 价格 | 以客户货币显示的 SKU 的单价。 |
| 付款货币 (PC) | 付出的比率 | 对于与资产相关联的使用情况事件，按发布者配置的货币来支付出版商。 |
| 估计价格 (PC) | 估计价格 | SKU 的单价，按发布者配置。 |
| 使用情况参考 | 使用情况参考 | 连接的 GUID，用于连接使用情况报表 (在商业 marketplace 分析) 与支出交易报表一起使用。 使用情况引用与 "支出交易记录" 报表中的 "订单 Id" 和 "LineItemId" 连接。 |
| 使用单位 | 使用单位 | 与 SKU 关联的消耗单位。 |
| Customer Id | 客户 ID | 分配给客户的唯一标识符。 一个客户可以有零个或多个 Azure Marketplace 订阅。 |
| 计费帐户 Id | 计费帐户 ID | 生成计费的帐户的标识符。 将 **计费帐户 ID** 映射到 **customerID** ，以将支出交易报告与客户、订单和使用情况报告连接起来。 |
| 使用数量 | 使用数量 | 由客户部署的资产使用的总使用量单位。<br>这基于使用类型项。 例如，如果使用类型为 "规范化使用"，则 "使用数量" 用于标准化使用量。 |
| NormalizedUsage | 规范化用量 | 由客户部署的资产消耗的规范化用量单位总数。<br>规范化使用小时数定义为按 VM 核心数规范化的使用小时数（[VM 核心数] x [原始使用小时数]）。 指定为“SHAREDCORE”的 VM 使用 1/6（或 0.1666）作为 [VM 核心数] 的乘数。 |
| MeteredUsage | 计量使用 | 使用由客户部署的产品/服务配置的计量所使用的总使用量单位。 |
| RawUsage | 原始用量 | 由客户部署的资产使用的总原始使用量单位。<br>原始使用时间定义为 Vm 在使用单位内的运行时间量。 |
| 估计的延伸费用 (CC) | 估计的按客户币种计费的费用 | 表示与使用关联的费用。 此列是价格 (CC) 和使用量数量的乘积。 |
| 估计的延伸费用 (PC) | 估计的按比率支付的费用 | 表示与使用关联的费用。 此列是 (PC) 和使用量数量的估计价格的产品。 |
||||

### <a name="usage-page-filters"></a>使用情况页面筛选器

" **使用情况** " 页筛选器将应用于 "订单" 页级别。 您可以选择一个或多个筛选器来呈现您选择的条件的图表，以及要在 "使用订单数据" 网格/导出中看到的数据。 筛选器将应用于在 "使用情况" 页右上角选择的月范围内提取的数据。

VM 原始使用情况的小组件和导出报告类似于 VM 标准化的使用情况，但有以下区别：

- 规范化使用小时数定义为按 VM 核心数规范化的使用小时数（[VM 核心数] x [原始使用小时数]）。 指定为“SHAREDCORE”的 VM 使用 1/6（或 0.1666）作为 [VM 核心数] 的乘数。
- 原始使用时间定义为 Vm 在使用单位内的运行时间量。

### <a name="metered-billing-usage"></a>计费使用情况

" **按流量计费** " 选项卡显示按计量维度度量使用量的产品/服务类型的使用情况信息。 当前提供 SaaS 套餐类型超额。 该选项卡显示 SaaS 按流量计费使用情况的超额趋势的图形表示形式：

- 按计量维度的超额趋势：显示套餐的所选计量维度的每月超额趋势。 X 轴表示月份，Y 轴表示超额使用数量。 自定义计量的度量单位也显示在 Y 轴上。

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="说明使用情况仪表板上的 VM 标准化使用情况图表。":::

- **按 sku 的超额趋势**：按 sku/计划表示所选计量维度的使用量趋势。 显示所选产品/服务使用情况最高的前5个计划。

- **客户的超额趋势**：客户领导者面板表示使用时间最长的客户的堆积列表，并在 _领导板_ 上显示，按自定义计量器的最高使用量进行排序。 在领导者面板中选择一个客户，以查看所选计量维度的超额使用趋势。

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="说明使用情况仪表板上的 &quot;客户的按流量计费&quot; 图表。":::

如果你有多个使用自定义计量的产品/服务，则按流量计费使用情况报表将根据其自定义计量维度显示你的所有产品/服务的使用情况信息。

> [!TIP]
> 您可以使用任何小组件右上角的下载图标来下载数据。 可以通过单击 "滚动更新" 或 "拇指向下" 图标来提供每个小组件的反馈。

## <a name="next-steps"></a>后续步骤

- 有关商业应用商店中提供的分析报表的概述，请参阅 [在合作伙伴中心访问商业市场的分析报表](./partner-center-portal/analytics.md)。
- 有关为产品/服务汇总市场活动的聚合数据的图表、趋势和值，请参阅[商业市场分析中的“摘要”仪表板](./summary-dashboard.md)。
- 有关以图形和可下载格式显示的订单的信息，请参阅 [商业 marketplace 分析中的订单仪表板](./orders-dashboard.md)
- 对于虚拟机 (VM) 提供使用情况和计量计费指标，请参阅 [商业 marketplace 分析中的使用情况仪表板](usage-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看有关 Azure Marketplace 中产品/服务的客户反馈和 Microsoft AppSource 的综合视图，请参阅 [合作伙伴中心的分级 & 审查分析仪表板](./partner-center-portal/ratings-reviews.md)。
- 有关商业 marketplace 分析的常见问题以及针对数据术语的综合性字典，请参阅 [商业市场分析术语和常见问题](./analytics-faq.md)。
