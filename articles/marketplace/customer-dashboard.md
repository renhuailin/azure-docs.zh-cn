---
title: 合作伙伴中心、Azure 市场和 Microsoft AppSource 的 Microsoft 商业市场分析中的“客户”仪表板
description: 了解如何使用商业市场分析中的“客户”仪表板访问有关客户的信息（包括增长趋势）。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 9/27/2021
ms.openlocfilehash: 7d665b34a112328a742e2ba4ed9816406a58fa30
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082949"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的“客户”仪表板

本文介绍合作伙伴中心的“客户”仪表板。 此仪表板显示有关客户的信息，包括以图形和可下载的格式显示的增长趋势。

>[!NOTE]
> 有关分析术语的详细定义，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。

## <a name="customers-dashboard"></a>“客户”仪表板

[“客户”仪表板](https://go.microsoft.com/fwlink/?linkid=2166011)显示已获取产品/服务的客户的数据。 你可以查看以下各项的图形表示形式：

- 活跃客户和流失客户的趋势
- 客户（包括现有客户、新客户和流失客户）增长趋势
- 按订单和使用量分类的客户
- 客户百分位数
- 按订单量和使用量统计的客户类型
- 按地域分类的客户
- 客户详细信息表
- “客户”页筛选器

> [!NOTE]
> 客户获取产品/服务与合作伙伴中心内进行报告之间的最大延迟为 48 小时。

## <a name="access-the-customers-dashboard"></a>访问“客户”仪表板

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在“主页”上，选择“见解”磁贴。

    [ ![说明了合作伙伴中心主页上的“见解”磁贴。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 在左侧菜单中，选择“客户”。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航栏中，选择“商业市场” > “分析” > “客户”  。

---

## <a name="elements-of-the-customers-dashboard"></a>“客户”仪表板的元素

下面各部分介绍了如何使用“客户”仪表板以及如何读取数据。

### <a name="month-range"></a>月份范围

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

可在每页右上角找到月份范围选择。 基于过去 6 个或 12 个月选择一个月份范围，或者选择最长时段为 12 个月的自定义月份范围，以便自定义“客户”页面图的输出内容。 默认月份范围（计算期间）为 6 个月。

[ ![展示“客户”页上的月份筛选器。](./media/customer-dashboard/customers-workspace-filters.png) ](./media/customer-dashboard/customers-workspace-filters.png#lightbox)

#### <a name="current-view"></a>[当前视图](#tab/current-view)

可在每页右上角找到月份范围选择。 基于过去 6 个或 12 个月选择一个月份范围，或者选择最长时段为 12 个月的自定义月份范围，以便自定义“客户”页面图的输出内容。 默认月份范围（计算期间）为 6 个月。

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="展示“客户”页上的月份筛选器。":::

---

> [!NOTE]
> 可视化小组件和导出报表中的所有指标都使用用户选择的计算周期。

### <a name="active-and-churned-customers-trend"></a>活跃客户和流失客户的趋势

在此部分，你会看到所选计算周期的客户增长趋势。 指标和增长趋势由折线图表示，可以通过将鼠标悬停在图表的折线上方来显示每个月的值。 小组件中“活跃客户”下面的百分比值表示所选计算周期的增长量。 例如，以下屏幕截图显示所选计算周期的增长为 0.92%。

[![展示“客户”页上的“客户”小组件。](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

有三种客户类型：新客户、现有客户和流失客户。

- 新客户指在选定月份内首次购买一个或多个产品/服务的客户。
- 现有客户指在选定月份之前已购买一个或多个产品/服务的客户。
- 流失客户指已取消以前购买的所有产品/服务的客户。 流失客户在“趋势”小组件中以负轴表示。

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>客户（包括现有客户、新客户和流失客户）增长趋势

在此部分，你会看到所有客户（包括新客户、现有客户和流失客户）的趋势和计数，以及逐月的增长趋势。

- 折线图表示总体客户增长百分比。
- 月份列表示按新客户、现有客户和流失客户统计的叠加的客户计数。
- 流失客户计数显示在 X 轴的负方向。
- 可以选择特定的图例项来显示更详细的视图。 例如，从图例中选择新客户，以只显示新客户。
- 将鼠标悬停在图表中的某一列上，可只显示该月的详细信息。

[![展示“客户”页上的“客户趋势”小组件。](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>按订单量/使用量统计的客户

“按订单量/使用量统计的客户”图表有三个选项卡：“订单量”、“规范化使用量”和“原始使用量”。 选择“订单量”选项卡以显示订单详细信息。

[ ![展示“客户”页上“按订单量/使用量统计的客户”小组件的“订单量”选项卡。](./media/customer-dashboard/customers-by-orders-usage.png) ](./media/customer-dashboard/customers-by-orders-usage.png#lightbox)

注意以下事项：

- 排行榜显示按订单计数排名的客户的详细信息。 选择客户后，客户详细信息显示在相邻的“详细信息”、“按 SKU 统计的订单”和“按席位统计的 SKU”部分中。
- 当发布者使用所有者角色登录时，此区域中将显示客户配置文件详细信息。 如果发布者使用参与者角色登录，则此部分中的详细信息将不可用。
- “按 SKU 统计的订单”环形图显示了为计划购买的订单的明细。 显示了具有最高订单计数的前 5 个计划，而其余订单则归入“其余全部”。
- “按席位统计的 SKU”环形图显示了为计划订购的席位的明细。 显示了具有最高席位数的前 5 个计划，而其余订单则归入“其余全部”。

还可以选择“规范化使用量”或“原始使用量”选项卡来查看使用详情。 

- 排行榜显示按使用小时数排名的客户的详细信息。 选择客户后，客户详细信息显示在相邻的“详细信息”、“按产品/服务统计的规范化使用量”和“按虚拟机(VM)大小统计的规范化使用量”部分中。
- 当发布者使用所有者角色登录时，此区域中将显示客户配置文件详细信息。 如果发布者使用参与者角色登录，则此部分中的详细信息将不可用。
- “按产品/服务统计的规范化使用量”圆环图显示了产品/服务的使用量明细。 显示了具有最高使用量的前 5 个计划，而其余产品/服务则归入“其余全部”。
- “按 VM 大小统计的规范化使用量”圆环图显示了不同 VM 大小的使用量明细。 显示了具有最高规范化使用量的前 5 个 VM 大小，而其余使用量则归入“其余全部”。

### <a name="top-customers-percentile"></a>排名靠前客户百分位数

“排名靠前客户百分位数”图表有三个选项卡：“订单量”、“规范化使用量”和“原始使用量”。 排名靠前客户百分位数沿 x 轴显示，具体取决于客户的订单数量。 y 轴显示客户的订单计数。 辅助性 y 轴（折线图）显示订单总数的累计百分比。 可以通过将鼠标悬停在折线图的各个点上方来显示详细信息。

[![展示“客户”页上“排名靠前客户百分位数”小组件的“订单量”选项卡。](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>按订单量和使用量统计的客户类型

“按客户类型统计的订单量/使用量”图显示新客户和现有客户的订单数、规范化使用量和原始使用小时数；根据订单的选择，图中分别显示规范化使用量和原始使用量。

该图表显示了以下信息：

- 新客户指在相同日历月份（y 轴）内第一次购买一个或多个产品/服务或报告了使用量的客户。
- 现有客户指在报告的日历月份之前（在 y 轴上）已购买产品/服务或报告了使用量的客户。

[![展示“客户”页上“按客户类型统计的订单量”小组件的“订单量”选项卡。](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>按地域分类的客户

对于所选的计算周期，热度地图会显示客户的总数，以及针对地理维度新添加的客户的百分比。 地图上的由浅到深的颜色代表客户计数从低到高的值。 选择表中的记录以放大国家或地区。

[![展示“客户”页上“按地理位置统计的订单量”小组件的“订单量”选项卡。](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图有一个补充性的网格，它显示特定位置的客户计数、订单计数和规范化使用小时数的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 选择地图上的“主页”按钮可还原为原始视图。

### <a name="customer-details-table"></a>客户详细信息表

“客户详细信息”表显示了排名前 1,000 位客户的编号列表（按他们首次购买产品/服务的日期排序）。 可以通过选择详细信息功能区中的展开图标来展开某个部分。

注意以下事项：

- 仅当客户同意时，客户个人信息才可用。 如果已使用所有者角色级别的权限登录，则只能查看此信息。
- 网格中的每列都可进行排序。
- 如果记录计数小于 1,000，则可以将数据提取到 .CSV 或 .TSV 文件中。
- 如果记录数超过 1,000，则导出的数据在接下来的 30 天会异步放置在下载页面中。
- 在表中应用筛选器以仅显示感兴趣的数据。 按公司名称、客户 ID、市场订阅 ID、Azure 许可证类型、购买日期、丢失日期、客户电子邮件、客户国家/地区/州/市/邮政编码、客户语言等对数据进行筛选。
- 当受保护的客户购买了某个产品/服务时，“客户详细数据”中的信息将被屏蔽 (************)。
- 客户维度详细信息（如公司名称、客户名称和客户电子邮件）是组织 ID 级别的信息，而不是 Azure 市场或 Microsoft AppSource 事务级别的信息。

表 1：数据术语字典

| 列名称<br>用户界面 | 属性名称 | 定义 | 编程式访问报表中的<br>列名称 |
| ------------ | ------------- | ------------- | ------------- |
| 市场订阅 ID | 市场订阅 ID | 与客户用来购买商业市场产品/服务的 Azure 订阅关联的唯一标识符。 对于基础结构产品/服务，这是客户的 Azure 订阅 GUID。 对于 SaaS 产品/服务，此项显示为零，因为 SaaS 购买不需要 Azure 订阅。 | MarketplaceSubscriptionId |
| DateAcquired | 获取日期 | 客户首次购买你发布的任何产品/服务的日期。 | DateAcquired |
| DateLost | 丢失日期 | 客户最后一次取消以前购买的所有产品/服务中的最后一个产品/服务的日期。 | DateLost |
| Provider Name | Provider Name | 介入 Microsoft 与客户之间的关系的提供商的名称。 如果客户是“通过经销商购买产品/服务的企业”，则此提供商是经销商。 如果涉及到云解决方案提供商 (CSP)，则此提供商是 CSP。 | ProviderName |
| 提供商电子邮件 | 提供商电子邮件 | 介入 Microsoft 与客户之间的关系的提供商的电子邮件地址。 如果客户是“通过经销商购买产品/服务的企业”，则此提供商是经销商。 如果涉及到云解决方案提供商 (CSP)，则此提供商是 CSP。 | ProviderEmail |
| FirstName | 客户名字 | 客户提供的名字。 此处的名称可能不同于客户的 Azure 订阅中提供的名称。 | FirstName |
| LastName | 客户姓氏 | 客户提供的姓氏。 此处的名称可能不同于客户的 Azure 订阅中提供的名称。 | LastName |
| 电子邮件 | 客户电子邮件 | 最终客户提供的电子邮件地址。 此处的电子邮件地址可能不同于客户的 Azure 订阅中的电子邮件地址。 | 电子邮件 |
| 客户公司名称 | 客户公司名称 | 客户提供的公司名称。 此处的名称可能不同于客户的 Azure 订阅中的名称。 | CustomerCompany 名称 |
| CustomerCity | 客户所在城市 | 客户提供的城市名称。 此处的城市可能不同于客户的 Azure 订阅中的城市。 | CustomerCity |
| 客户邮政编码 | 客户邮政编码 | 客户提供的邮政编码。 此处的编码可能不同于客户的 Azure 订阅中提供的邮政编码。 | CustomerPostal 编码 |
| CustomerCommunicationCulture | 客户通信语言 | 客户的首选通信语言。 | CustomerCommunicationCulture |
| CustomerCountryRegion | 客户所在国家/地区 | 客户提供的国家/地区名称。 此处的国家/地区可能不同于客户的 Azure 订阅中的国家/地区。 | CustomerCountryRegion |
| AzureLicenseType | Azure 许可证类型 | 购买 Azure 的客户使用的许可协议类型。 也称为“渠道”。 可能的值包括：<ul><li>云解决方案提供商</li><li>Enterprise</li><li>通过经销商购买产品/服务的企业</li><li>即用即付</li></ul> | AzureLicenseType |
| PromotionalCustomers | 是促销联系人选择加入 | 此值告知是否客户主动从发布者渠道登记为促销联系人。 目前我们不会向客户提供该选项，因此，在整个板块中都指示了“否”。 部署此功能后，我们会开始相应地做出更新。 | PromotionalCustomers |
| CustomerState | 客户所在的州/省 | 客户提供的居住州/省。 此处的州/省可能不同于客户的 Azure 订阅中提供的州/省。 | CustomerState |
| CommerceRootCustomer | 商业根客户 | 一个计费帐户 ID 可以与多个客户 ID 相关联。<br>一个由计费帐户 ID 和客户 ID 形成的组合可以与多个商业市场订阅相关联。<br>“商业根客户”表示订阅的客户的名称。 | CommerceRootCustomer |
| 客户 ID | 客户 ID | 分配给客户的唯一标识符。 一个客户可以有零个或零个以上的 Azure 市场订阅。 | CustomerId |
| 计费帐户 ID | 计费帐户 ID | 针对其生成账单的帐户的标识符。 将“计费帐户 ID”映射到 customerID，以将“付款交易报表”与“客户”、“订单”和“使用情况报表”关联起来。 | BillingAccountId |
| 客户类型 | 客户类型 | 此字段的值表示客户的类型。 可能的值包括：<ul><li>个人</li> <li>organization</li></ul> | CustomerType |
|||||

### <a name="customers-page-filters"></a>“客户”页筛选器

“客户页”筛选器在客户页级别上应用。 可以选择多个筛选器根据你选择查看的条件以及你希望在“详细订单数据”网格/导出结果中查看的数据来呈现图表。 筛选器会应用于按你在“客户”页面右上角选择的月份范围提取的数据。

> [!TIP]
> 可以使用任意小组件右上角的下载图标来下载数据。 可以通过单击“赞成”或“反对”图标来提供每个小组件的反馈。

## <a name="next-steps"></a>后续步骤

- 若要大致了解商业市场内可用的分析报表，请参阅[在合作伙伴中心访问商业市场的分析报表](analytics.md)。
- 有关为产品/服务汇总市场活动的聚合数据的图表、趋势和值，请参阅[商业市场分析中的“摘要”仪表板](./summary-dashboard.md)。
- 有关采用图形和可下载格式显示的订单的信息，请参阅[商业市场分析中的“订单”仪表板](./orders-dashboard.md)。
- 有关虚拟机 (VM) 产品/服务使用情况和按流量计费指标的信息，请参阅[商业市场分析中的使用情况仪表板](./usage-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](downloads-dashboard.md)。
- 若要查看客户对 Azure 市场和 Microsoft AppSource 中产品/服务的反馈的综合视图，请参阅[合作伙伴中心的评分和评价分析仪表板](ratings-reviews.md)。
- 有关商业市场分析的常见问题解答以及数据术语的综合词典，请参阅[商业市场分析术语和常见问题](./analytics-faq.yml)。
