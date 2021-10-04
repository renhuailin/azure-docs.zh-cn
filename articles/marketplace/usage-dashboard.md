---
title: 了解商业市场分析中的“使用情况”仪表板 | Azure 市场
description: 了解如何访问发布到 Azure 市场的产品/服务的所有使用情况和按流量计费指标。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: c50a36c521848956577fd0e8f53bdb2dceaaee8f
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084033"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的“使用情况”仪表板

本文提供有关合作伙伴中心中的“使用情况”仪表板的信息。 此仪表板在以下三个不同的选项卡中显示所有虚拟机 (VM) 产品/服务的规范化使用情况、原始使用情况和按流量计费指标：VM 规范化使用情况、VM 原始使用情况和按流量计费的使用情况。

>[!NOTE]
> 有关分析术语的详细定义，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。

## <a name="usage-dashboard"></a>使用情况仪表板

[“使用情况”仪表板](https://go.microsoft.com/fwlink/?linkid=2166106)显示所有软件即服务 (SaaS) 产品/服务的当前订单。 你可以查看以下各项的图形表示形式：

- 使用趋势
- 按产品/服务划分的规范化使用情况
- 按其他维度划分的规范化使用情况：VM 大小、销售渠道和产品/服务类型
- 按地域的使用情况
- 详细使用情况表
- “订单页面”筛选器

> [!NOTE]
> 合作伙伴中心的使用事件生成和报告之间的最大延迟为 48 小时。

## <a name="access-the-usage-dashboard"></a>访问“使用情况”仪表板

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在“主页”上，选择“见解”磁贴。

    [ ![说明了合作伙伴中心主页上的“见解”磁贴。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 在左侧菜单中，选择“使用情况”。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航栏中，选择“商业市场” > “分析” > “使用情况”  。

---

## <a name="elements-of-the-usage-dashboard"></a>“使用情况”仪表板的元素

下面各部分介绍了如何使用“使用情况”仪表板以及如何读取数据。

### <a name="month-range"></a>月份范围

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

可在每页右上角找到月份范围选择。 通过基于过去 6 个或 12 个月选择一个月份范围，或者选择最长时段为 12 个月的自定义月份范围，自定义“使用情况”页面图的输出内容。 默认月份范围（计算期间）为 6 个月。

[ ![说明了“使用情况”仪表板上的“月份”筛选器。](./media/usage-dashboard/usage-dashboard-filters.png) ](./media/usage-dashboard/usage-dashboard-filters.png#lightbox)

#### <a name="current-view"></a>[当前视图](#tab/current-view)

可在每页右上角找到月份范围选择。 通过基于过去 6 个或 12 个月选择一个月份范围，或者选择最长时段为 12 个月的自定义月份范围，自定义“使用情况”页面图的输出内容。 默认月份范围（计算期间）为 6 个月。

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="使用情况仪表板上的月筛选器图解。":::

---

### <a name="usage-trend"></a>使用趋势

在本部分中，你将找到客户在所选计算期间使用的所有产品/服务的总使用时间和趋势。 指标和增长趋势由折线图表示。 通过将鼠标悬停在图表中的行上方来显示每个月的值。 小组件中“使用指标”下面的百分比值表示所选计算期间的增长量或下降量。

使用时间有两种表示形式：VM 规范化使用和 VM 原始使用。

- 规范化使用小时数定义为按 VM 核心数规范化的使用小时数（[VM 核心数] x [原始使用小时数]）。 指定为“SHAREDCORE”的 VM 使用 1/6（或 0.1666）作为 [VM 核心数] 的乘数。
- 原始使用时间定义为 VM 已运行的时间量（以小时为单位）。

[![使用情况仪表板上的规范化使用情况和原始使用情况数据图解。](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>按产品/服务划分的规范化使用情况

本部分提供 Azure 市场中基于使用情况的产品/服务的总使用时间和趋势。 下面介绍了“按产品/服务划分的规范化使用情况”图表。

- “按产品/服务划分的规范化使用情况”堆积柱形图根据所选的计算期间，显示前五个产品/服务的规范化使用时间细分。 前五个产品/服务显示在图中，而其余产品/服务会分组为“其余全部”类别。
- 堆积柱形图描绘所选日期范围的逐月增长趋势。 月份列表示套餐的使用小时数，以及相应月份的最高使用小时数。 折线图描绘在辅助 Y 轴上绘制的增长百分比趋势。
- 可以在图例中选择特定产品/服务以在图形中仅显示这些产品/服务。

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="使用情况仪表板上的产品/服务规范化使用情况数据图解。":::

你可以选择任何产品/服务，最多可以选择该产品/服务的三个 SKU，以查看产品/服务和所选 SKU 的每月使用趋势。

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="使用情况仪表板上的产品/服务规范化使用情况和 SKU 数据图解。":::

### <a name="orders-by-offers-and-skus"></a>按产品/服务和 SKU 统计的订单

“按产品/服务和 SKU 统计的订单”图表显示了所有产品/服务的度量值和趋势。 注意以下事项：

- 排名靠前的产品/服务会显示在图中，而其余产品/服务会分组为“其余全部”。
- 可以在图例中选择特定产品/服务以在图形中仅显示这些产品/服务。
- 将鼠标悬停在图中的切片上将显示与所有产品/服务的总订单数相比该产品/服务的订单数和百分比。
- “按产品/服务趋势统计的订单”显示逐月增长趋势。 月份列表示按产品/服务名称统计的订单数。 折线图显示在 z 轴上绘制的增长百分比趋势。

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="使用情况仪表板上的“按产品/服务和 SKU 统计的订单”图表图解。":::

你可以选择任何产品/服务，最多可以选择该产品/服务的三个 SKU，以查看产品/服务、SKU 和席位的每月趋势。

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="使用情况仪表板上的“按产品/服务和 SKU 统计的订单”图表图解。显示了产品/服务趋势、SKU 趋势和席位趋势。":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>按其他维度划分的规范化使用情况：VM 大小、销售渠道和产品/服务类型

有三个维度选项卡：VM 大小、销售渠道和产品/服务类型。 可以查看每个维度的使用指标和每月趋势。

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="使用情况仪表板上的规范化使用情况其他维度图表图解。":::

### <a name="usage-by-geography"></a>按地域的使用情况

对于所选计算期间，热度地图显示地域维度的总使用量。 地图上的由浅到深的颜色代表客户计数从低到高的值。 选择表中的记录以放大国家/地区。

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="使用情况仪表板上的规范化使用情况国家/地区图表图解。":::

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图有一个补充性的网格，可显示特定位置的客户计数、订单计数和规范化使用时间的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 选择地图上的“主页”按钮可还原为原始视图。

### <a name="usage-details-table"></a>使用情况详细信息表

“使用情况详细信息”表显示按使用量排序的前 1000 个使用情况记录的编号列表。 注意以下事项：

- 网格中的每列都可进行排序。
- 如果记录计数小于 1000，则可以将数据提取到 TSV 或 .CSV 文件中。
- 如果记录计数超过 1000，则导出数据会异步放置在将在接下来 30 天内可用的下载页面中。
- 向“详细使用情况数据”应用筛选器以仅显示感兴趣的数据。 数据可以按国家/地区、销售渠道、市场许可证类型、使用类型、产品/服务名称、产品/服务类型、免费试用版、市场订阅 ID、客户 ID 和公司名称进行筛选。

表 1：数据术语字典

| 列名称<br>用户界面 | 属性名称 | 定义 | 编程式访问报表中的<br>列名称 |
| ------------ | ------------- | ------------- | ------------- |
| 市场订阅 ID | 市场订阅 ID | 与客户用来购买商业市场产品/服务的 Azure 订阅关联的唯一标识符。 该 ID 以前称为“Azure 订阅 GUID”。 | MarketplaceSubscriptionId |
| MonthStartDate | MonthStartDate | 月开始日期表示采购月份。 | MonthStartDate |
| 产品/服务类型 | 产品/服务类型 | 商业市场产品/服务的类型。 | OfferType |
| Azure 许可证类型 | Azure 许可证类型 | 购买 Azure 的客户使用的许可协议类型。 也称为“渠道”。 可能的值包括：<ul><li>云解决方案提供商</li><li>Enterprise</li><li>通过经销商购买产品/服务的企业</li><li>即用即付</li></ul> | AzureLicenseType |
| 市场许可证类型 | 市场许可证类型 | 商业市场产品/服务的计费方式。 可能的值包括：<ul><li>通过 Azure 计费</li><li>自带许可</li><li>免费</li><li>Microsoft 充当经销商</li></ul> | MarketplaceLicenseType |
| SKU | SKU | 与产品/服务关联的计划。 | SKU |
| 客户所在国家/地区 | 客户所在国家/地区 | 客户提供的国家/地区名称。 此处的国家/地区可能不同于客户的 Azure 订阅中的国家/地区。 | CustomerCountry |
| 是预览版 SKU | 是预览版 SKU | 此值显示你是否已将 SKU 标记为“预览版”。 如果已相应地标记了 SKU，则值为“是”，并且只有你授权的 Azure 订阅可以部署和使用此映像。 如果 SKU 未标识为“预览版”，则值为“否”。 | IsPreviewSKU |
| SKU 计费类型 | SKU 计费类型 | 与产品/服务中的每个 SKU 关联的计费类型。 可能的值包括：<ul><li>免费</li><li>已付</li></ul> | SKUBillingType |
| IsInternal | 不推荐使用 | 不推荐使用 | 不推荐使用 |
| VM 大小 | 虚拟机大小 | 对于基于 VM 的产品/服务类型，此实体表示与产品/服务的 SKU 关联的 VM 大小。 | VMSize |
| 云实例名称 | 云实例名称 | 部署 VM 的 Microsoft 云。 | CloudInstanceName |
| ServicePlanName | 不推荐使用 | 已弃用（与 SKU 定义相同） | ServicePlanName |
| 产品名称 | 产品名称 | 商业市场产品/服务的名称。 | OfferName |
| DeploymentMethod | 不推荐使用 | 已弃用（与产品/服务类型的定义相同） | DeploymentMethod |
| 客户公司名称 | 客户公司名称 | 客户提供的公司名称。 此处的名称可能不同于客户的 Azure 订阅中的名称。 | CustomerCompanyName |
| 使用日期 | 使用日期 | 基于使用情况的资产的使用事件生成日期。 | UsageDate |
| IsMultisolution | Is Multisolution | 表示产品/服务是否为 Multisolution 产品/服务类型。 | IsMultisolution |
| 是新客户 | 不推荐使用 | 不推荐使用 | IsNewCustomer |
| 内核大小 | 内核大小 | 与基于 VM 的产品/服务关联的内核数。 | CoreSize |
| 使用情况类型 | 使用情况类型 | 指示与产品/服务关联的使用事件是否为以下事件之一：<ul><li>规范化使用情况</li><li>原始使用情况</li><li>计量使用</li></ul> | UsageType |
| 试用结束日期 | 试用结束日期 | 此订单的试用期将要结束或已结束的日期。 | TrialEndDate |
| 客户货币 (CC) | 客户币种 | 客户用于商业市场交易的币种。 | CustomerCurrencyCC |
| 价格 (CC) | 价格 | 按客户币种显示的 SKU 单价。 | PriceCC |
| 付款货币 (PC) | 付款币种 | 对于与资产相关联的使用事件，按发布者配置的币种来支付发布者。 | PayoutCurrencyPC |
| 估计价格 (PC) | 估计价格 | 以发布者配置的币种计费的 SKU 单价。 | EstimatedPricePC |
| 使用情况参考 | 使用情况参考 | 连接的 GUID，用于关联使用情况报表（在商业市场分析中）和付款交易报表。 使用情况参考与付款交易报表中的 OrderId 和 LineItemId 关联。 | UsageReference |
| 使用单位 | 使用单位 | 与 SKU 关联的使用单位。 | UsageUnit |
| 客户 ID | 客户 ID | 分配给客户的唯一标识符。 一个客户可以有零个或零个以上的 Azure 市场订阅。 | CustomerId |
| 计费帐户 ID | 计费帐户 ID | 针对其生成账单的帐户的标识符。 将“计费帐户 ID”映射到 customerID，以将“付款交易报表”与“客户”、“订单”和“使用情况报表”关联起来。 | BillingAccountId |
| 使用数量 | 使用数量 | 客户部署的资产所消耗的总使用单位。<br>这基于使用类型项。 例如，如果使用类型为“规范化使用”，则“使用数量”对应“规范化使用”。 | UsageQuantity |
| NormalizedUsage | 规范化用量 | 客户部署的资产所消耗的总规范化使用单位。<br>规范化使用小时数定义为按 VM 核心数规范化的使用小时数（[VM 核心数] x [原始使用小时数]）。 指定为“SHAREDCORE”的 VM 使用 1/6（或 0.1666）作为 [VM 核心数] 的乘数。 | NormalizedUsage |
| MeteredUsage | 计量使用 | 配置了由客户部署的产品/服务的计量所消耗的总使用单位。 | MeteredUsage |
| RawUsage | 原始用量 | 客户部署的资产所消耗的总原始使用单位。<br>原始使用时间定义为 VM 已运行的时间量（以使用单位表示）。 | RawUsage |
| 估计的延伸费用 (CC) | 估计的延伸费用（按客户币种计费） | 表示与使用量相关的费用。 此列是价格 (CC) 与使用数量的乘积。 | EstimatedExtendedChargeCC |
| 估计的延伸费用 (PC) | 估计的延伸费用（按付款币种计费） | 表示与使用量相关的费用。 此列是估计价格 (PC) 与使用数量的乘积。 | EstimatedExtended ChargePC |
| 计量 ID | 计量 ID | 表示产品/服务的计量 ID。 | 计量 ID |
| 合作伙伴中心检测到异常 | 合作伙伴中心检测到异常 | **适用于具有自定义计量维度的产品/服务**。<br>指示发布者是否报告了产品/服务的自定义计量维度的超额使用情况（合作伙伴中心将该维度由标记为异常）。可能的值为： <ul><li>0（非异常）</li><li>1（异常）</li></ul>如果发布者没有含自定义计量维度的产品/服务，并且通过编程访问导出此列，则值将为 null。 | PartnerCenterDetectedAnomaly |
| 发布者标记为异常 | 发布者标记为异常 | **适用于具有自定义计量维度的产品/服务**。<br>指示发布者是否确认客户对产品/服务的自定义计量维度的超额使用情况是真实的或虚假的。 可能的值包括：<ul><li>0（发布者已将其标记为非异常）</li><li>1（发布者已将其标记为异常）</li></ul>如果发布者没有含自定义计量维度的产品/服务，并且通过编程访问导出此列，则值将为 null。 | PublisherMarkedAnomaly |
| 新报告的使用情况 | 新报告的使用情况 | **适用于具有自定义计量维度的产品/服务**。<br>客户的超额使用情况（对于由发布者标识为异常的产品/服务的自定义计量维度）。 此字段指定发布者报告的新超额使用情况。<br>如果发布者没有含自定义计量维度的产品/服务，并且通过编程访问导出此列，则值将为 null。 | NewReportedUsage |
| 执行的操作 | 执行的操作 | **适用于具有自定义计量维度的产品/服务**。<br>指定发布者确认以下事项的时间：客户对产品/服务的自定义计量维度的超额使用情况是真实的或虚假的。<br>如果发布者没有含自定义计量维度的产品/服务，并且通过编程访问导出此列，则值将为 null。 | ActionTakenAt |
| 采取的操作 | 采取的操作 | **适用于具有自定义计量维度的产品/服务**。<br>指定确认以下事项的人员：客户对产品/服务的自定义计量维度的超额使用情况是真实的或虚假的。<br>如果发布者没有含自定义计量维度的产品/服务，并且通过编程访问导出此列，则值将为 null。 | ActionTakenBy |
| 估计的财务影响 (USD) | 估计的财务影响，以美元计 | **适用于具有自定义计量维度的产品/服务**。<br>当合作伙伴中心将客户对产品/服务的自定义计量维度的超额使用情况标记为异常时，该字段会指定异常超额使用情况的估计财务影响（以美元为单位）。<br>如果发布者没有含自定义计量维度的产品/服务，并且通过编程方式导出此列，则值将为 null。 | EstimatedFinancialImpactUSD |
| 不适用 | 资源 ID | 资源的完全限定 ID，包括资源名称和资源类型。 注意，此数据字段仅在下载报告中提供。<br>使用以下格式：<br> /subscriptions/{guid}/resourceGroups/{resource-group-name}/{resource-provider-namespace}/{resource-type}/{resource-name} | 空值 |
|||||

### <a name="usage-page-filters"></a>使用情况页面筛选器

“使用情况”页面筛选器在“订单”页面级别应用。 可以选择一个或多个筛选器，根据你选择查看的条件以及你希望在“使用订单数据”网格/导出结果中查看的数据来呈现图表。 这些筛选器将应用于根据“使用情况”页面右上角所选月份范围提取的数据。

VM 原始使用情况的小组件和导出报告与 VM 规范化使用情况类似，但有以下区别：

- 规范化使用小时数定义为按 VM 核心数规范化的使用小时数（[VM 核心数] x [原始使用小时数]）。 指定为“SHAREDCORE”的 VM 使用 1/6（或 0.1666）作为 [VM 核心数] 的乘数。
- 原始使用时间定义为 VM 已运行的时间量（以使用单位表示）。

### <a name="metered-billing-usage"></a>按流量计费的使用情况

“按流量计费的使用情况”选项卡显示按计量维度度量使用情况的产品/服务类型的使用情况信息。 当前提供 SaaS 套餐类型超额。 该选项卡显示 SaaS 按流量计费使用情况的超额趋势的图形表示形式：

- 按计量维度的超额趋势：显示套餐的所选计量维度的每月超额趋势。 X 轴表示月份，Y 轴表示超额使用数量。 自定义计量的度量单位也显示在 Y 轴上。

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="使用情况仪表板上的 VM 规范化使用情况图表图解。":::

- 按 SKU 的超额趋势：按 SKU/计划表示所选计量维度的使用数量趋势。 显示所选产品/服务使用量最高的前 5 个计划。

- 按客户划分的超额趋势：客户排行榜代表使用时间最多的客户堆叠列表，并显示在“排行榜”上，按自定义计量的最高使用量进行排名。 在排行榜中选择一个客户可查看所选计量维度的超额使用趋势。

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="使用情况仪表板上的“按客户统计的按流量计费的使用情况”图表图解。":::

如果你有多个使用自定义计量的产品/服务，则按流量计费的使用情况报表将根据产品/服务的自定义计量维度显示所有产品/服务的使用情况信息。

> [!TIP]
> 可以使用任意小组件右上角的下载图标来下载数据。 可以通过单击“赞成”或“反对”图标来提供每个小组件的反馈。

## <a name="next-steps"></a>后续步骤

- 若要大致了解商业市场内可用的分析报表，请参阅[在合作伙伴中心访问商业市场的分析报表](analytics.md)。
- 有关为产品/服务汇总市场活动的聚合数据的图表、趋势和值，请参阅[商业市场分析中的“摘要”仪表板](./summary-dashboard.md)。
- 有关采用图形和可下载格式显示的订单的信息，请参阅[商业市场分析中的订单仪表板](./orders-dashboard.md)
- 有关虚拟机 (VM) 产品/服务使用情况和按流量计费指标的信息，请参阅[商业市场分析中的使用情况仪表板](usage-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](downloads-dashboard.md)。
- 若要查看客户对 Azure 市场和 Microsoft AppSource 中产品/服务的反馈的综合视图，请参阅[合作伙伴中心的评分和评价分析仪表板](ratings-reviews.md)。
- 有关商业市场分析的常见问题解答以及数据术语的综合词典，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。
