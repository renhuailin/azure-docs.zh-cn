---
title: 合作伙伴中心的 Microsoft 商业 marketplace 分析中的客户仪表板、Azure Marketplace 和 Microsoft AppSource
description: 了解如何使用商业 marketplace 分析中的客户仪表板访问有关客户的信息，包括增长趋势。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 878fa181ff09d547ef6594a238e9c1d883cc89b7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460486"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>商业 marketplace 分析中的客户仪表板

本文提供了有关合作伙伴中心的客户仪表板的信息。 此仪表板显示有关客户的信息，包括以图形和可下载的格式显示的增长趋势。

若要访问合作伙伴中心的 "客户" 仪表板，请在 "**商用 Marketplace** " 下选择 "**[分析](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **客户**"。

>[!NOTE]
> 有关分析术语的详细定义，请参阅 [商业市场分析术语和常见问题](./analytics-faq.md)。

## <a name="customers-dashboard"></a>“客户”仪表板

客户仪表板显示已获得产品/服务的客户的数据。 你可以查看以下各项的图形表示形式：

- 活动和改动客户的趋势
- 客户增长趋势，包括现有的、新的和改动的客户
- 按订单和使用量分类的客户
- 客户百分比 
- 按订单和使用情况的客户类型
- 按地域分类的客户
- 客户详细信息表
- 客户页面筛选器

> [!NOTE]
> 合作伙伴中心中客户获取和报告之间的最大延迟为48小时。

## <a name="elements-of-the-customers-dashboard"></a>客户仪表板的元素

以下部分介绍如何使用 "客户" 仪表板以及如何读取数据。

### <a name="month-range"></a>月份范围

可以在每个页面的右上角找到 "月份范围" 选择。 通过选择基于过去6个月或12个月的月份范围或选择最大持续时间为12个月的自定义月份范围，自定义 " **客户** " 页图形的输出。 默认月份范围 (计算期间) 为六个月。

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="说明 &quot;客户&quot; 页上的月份筛选器。":::

> [!NOTE]
> 可视化小组件中的所有度量值和导出报表都服从用户选择的计算时间段。

### <a name="active-and-churned-customers-trend"></a>活动和改动客户的趋势

在本部分中，你将找到所选计算期间的客户增长趋势。 指标和增长趋势由折线图表示，并通过将鼠标悬停在图表上的折线图上来显示每个月的值。 小组件中 **活动客户** 下面的百分比值表示所选计算期间的增长量。 例如，以下屏幕截图显示所选计算期间0.92% 的增长。

[![说明 "客户" 页上的 "客户" 小组件。](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

有三种客户类型：新客户、现有客户和流失客户。

- 新客户指在选定月份内首次购买一个或多个产品/服务的客户。
- 现有客户指在选定月份之前已购买一个或多个产品/服务的客户。
- 流失客户指已取消以前购买的所有产品/服务的客户。 改动客户在 "趋势" 小组件中以负轴表示。

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>客户增长趋势，包括现有的、新的和改动的客户

在本部分中，你将看到所有客户的趋势和计数，其中包括新的、现有的和改动的增长趋势。

- 折线图表示总体客户增长百分比。
- 月份列表示按新客户、现有客户和流失客户统计的叠加的客户计数。
- 改动客户计数按 X 轴的负方向显示。
- 您可以选择特定的图例项来显示更详细的视图。 例如，从图例中选择 "新客户"，只显示新客户。
- 将鼠标悬停在图表中的某一列上只显示该月的详细信息。

[![说明 "客户" 页上的 "客户趋势" 小组件。](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>按订单/使用情况的客户

**按订单/使用情况** 图表的客户有三个选项卡：订单、规范化使用情况和原始使用情况。 选择 " **订单** " 选项卡以显示订单详细信息。

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="说明 &quot;客户&quot; 页上按订单和使用情况小组件列出的客户的订单选项卡。":::

注意以下事项：

- 领导者面板提供按订单计数排名的客户的详细信息。 选择客户后，客户详细信息显示在相邻的 "详细信息"、"按 Sku 排序" 和 "按座位列出的 Sku" 部分中。
- 当发布服务器使用所有者角色登录时，客户配置文件详细信息将显示在此区域中。 如果发布服务器使用参与者角色登录，则此部分中的详细信息将不可用。
- " **按 sku** 分类的订单" 环形图显示为计划购买的订单明细。 显示具有最高订单数的前五个计划，而其余订单按 " **全部 rest**" 分组。
- " **通过座位** 显示的 sku" 图表显示为计划订购的座位的细分。 显示最高座位数最多的5个计划，而其余订单在 " **全部 rest**" 下分组。

还可以选择 " **规范化使用情况** " 或 " **原始使用情况** " 选项卡来查看使用情况详细信息。

- 领导者面板提供按使用小时数排名的客户的详细信息。 选择客户后，客户的详细信息将显示在 "详细信息"、"按产品数标准化的使用情况" 和 "按虚拟机 (VM) 大小 () " 部分中的 "规范化使用情况" 部分中。
- 当发布者使用所有者角色登录时，此区域中将显示客户配置文件详细信息。 如果发布者使用参与者角色登录，则此部分中的详细信息将不可用。
- **规范化的使用情况提供** 环形图显示了产品/服务消耗的使用情况细目。 将显示最高使用量计数最高的5个计划，而剩余的产品/服务将按 " **全部 rest**" 分组。
- **Vm 大小的规范化使用量 (s)** 环形图显示不同 vm 大小 () 所使用的使用量的细分。 将显示具有最高标准化使用量的前五个 VM 大小，而其余使用情况将归为 " **全部 rest**" 下。

### <a name="top-customers-percentile"></a>排名靠前的客户

**排名靠前的客户** 的图表有三个选项卡： "订单"、"已规范化使用情况" 和 "原始使用情况"。 _排名靠前的客户_ 在 x 轴上显示，由订单数决定。 y 轴显示客户的订单计数。 辅助性 y 轴（折线图）显示订单总数的累计百分比。 可以通过将鼠标悬停在折线图的各个点上方来显示详细信息。

[![说明 "客户" 页上排名靠前的客户的小组件的 "订单" 选项卡。](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>按订单和使用情况的客户类型

“按客户类型统计的订单量/使用量”图显示新客户和现有客户的订单数、规范化使用量和原始使用小时数；根据订单的选择，图中分别显示规范化使用量和原始使用量。

此图显示了以下内容：

- 新客户指在相同日历月份（y 轴）内第一次购买一个或多个产品/服务或报告了使用量的客户。
- 现有客户指在报告的日历月份之前（在 y 轴上）已购买产品/服务或报告了使用量的客户。

[![说明 "客户" 页上 "按客户类型显示的订单" 小组件的 "订单" 选项卡。](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>按地域分类的客户

对于所选的计算期间，热度地图显示客户的总数，以及针对 "地域" 维度的新添加的客户的百分比。 地图上的由浅到深的颜色代表客户计数从低到高的值。 选择表中的一条记录，以放大某个国家或地区。

[![说明 "客户" 页上的 "按地理位置排序" 小组件的 "订单" 选项卡。](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

注意以下事项：

- 可移动地图来查看确切的位置。
- 可缩放到具体的位置。
- 热度地图有一个补充性的网格，它显示特定位置的客户计数、订单计数和规范化使用小时数的详细信息。
- 可在网格中搜索和选择一个国家/地区，放大地图找到该位置。 通过选择地图中的 " **主页** " 按钮，还原到原始视图。

### <a name="customer-details-table"></a>客户详细信息表

" **客户详细信息** " 表显示按首次获取产品/服务的日期排序的排名靠前的1000客户的列表。 您可以通过选择 "详细信息" 功能区中的展开图标来展开某个部分。

注意以下事项：

- 仅当客户同意时，客户个人信息才可用。 仅当你已使用所有者角色级别的权限登录时，才能查看此信息。
- 网格中的每列都可进行排序。
- 数据可以提取到。CSV 或。TSV 文件（如果记录计数小于1000）。
- 如果记录数超过1000，则导出的数据将在接下来的30天内异步置于下载页中。
- 对表应用筛选器，以便仅显示感兴趣的数据。 按公司名称、客户 ID、Marketplace 订阅 ID、Azure 许可证类型、获取日期、丢失日期、客户电子邮件、客户国家/地区/州/省/市/自治区、客户语言等筛选数据。
- 当受保护的客户购买了某个产品/服务时，“客户详细数据”中的信息将被屏蔽 (************)。
- 客户维度详细信息（如公司名称、客户名称和客户电子邮件）是组织 ID 级别，而不是位于 Azure Marketplace 或 Microsoft AppSource 事务级别。

_**表1：数据字典**_

| 列名称 | 属性名称 | 定义 |
| ------------ | ------------- | ------------- |
| Marketplace 订阅 Id | 市场订阅 ID | 与客户用于购买商业 marketplace 产品/服务的 Azure 订阅相关联的唯一标识符。 该 ID 以前称为“Azure 订阅 GUID”。 |
| DateAcquired | 获取日期 | 客户购买的任何产品/服务的第一个日期。 |
| DateLost | 丢失日期 | 客户取消上次购买的所有产品/服务的最后日期。 |
| Provider Name | Provider Name | Microsoft 与客户之间的关系所涉及的提供者的名称。 如果客户是“通过经销商购买产品/服务的企业”，则此提供商是经销商。 如果涉及到云解决方案提供商 (CSP)，则此提供商是 CSP。 |
| 提供商电子邮件 | 提供商电子邮件 | Microsoft 与客户之间的关系中所涉及的提供商的电子邮件地址。 如果客户是“通过经销商购买产品/服务的企业”，则此提供商是经销商。 如果涉及到云解决方案提供商 (CSP)，则此提供商是 CSP。 |
| FirstName | 客户名字 | 客户提供的名字。 名称可能不同于客户的 Azure 订阅中提供的名称。 |
| LastName | 客户姓氏 | 客户提供的最后一个名称。 名称可能不同于客户的 Azure 订阅中提供的名称。 |
| 电子邮件 | 客户电子邮件 | 最终客户提供的电子邮件地址。 电子邮件可能与客户的 Azure 订阅中的电子邮件地址不同。 |
| 客户公司名称 | 客户公司名称 | 客户提供的公司名称。 名称可能不同于客户的 Azure 订阅中的城市。 |
| CustomerCity | 客户所在城市 | 客户提供的城市名称。 城市可能不同于客户的 Azure 订阅中的城市。 |
| 客户邮政编码 | 客户邮政编码 | 客户提供的邮政编码。 代码可能与客户的 Azure 订阅中提供的邮政编码不同。 |
| CustomerCommunicationCulture | 客户通信语言 | 客户的首选通信语言。 |
| CustomerCountryRegion | 客户所在国家/地区 | 客户提供的国家/地区名称。 国家/地区可能不同于客户的 Azure 订阅中的国家/地区。 |
| AzureLicenseType | Azure 许可证类型 | 购买 Azure 的客户使用的许可协议类型。 也称为 _通道_。 可能的值为：<ul><li>云解决方案提供商</li><li>企业</li><li>企业通过分销商</li><li>即付即用</li></ul> |
| PromotionalCustomers | 是促销联系人选择加入 | 此值告知是否客户主动从发布者渠道登记为促销联系人。 目前我们不会向客户提供该选项，因此，在整个板块中都指示了“否”。 部署此功能后，我们将相应地开始更新。 |
| CustomerState | 客户所在的州/省 | 客户提供的住所的状态。 状态可能与客户的 Azure 订阅中提供的状态不同。 |
| CommerceRootCustomer | 商业根客户 | 一个计费帐户 ID 可以与多个客户 Id 相关联。<br>计费帐户 ID 和客户 ID 的一个组合可以与多个商业 marketplace 订阅相关联。<br>Commerce Root 客户表示订阅的客户的名称。 |
| Customer Id | 客户 ID | 分配给客户的唯一标识符。 一个客户可以有零个或多个 Azure Marketplace 订阅。 |
| 计费帐户 Id | 计费帐户 ID | 生成计费的帐户的标识符。 将 **计费帐户 ID** 映射到 **customerID** ，以将支出交易报告与客户、订单和使用情况报告连接起来。 |
||||

### <a name="customers-page-filters"></a>客户页面筛选器

“客户页”筛选器在客户页级别上应用。 您可以选择多个筛选器来呈现您选择的条件的图表，以及要在 "详细订单数据" 网格/导出中查看的数据。 筛选器将应用于在 "客户" 页右上角所选月份范围内提取的数据。

> [!TIP]
> 您可以使用任何小组件右上角的下载图标来下载数据。 可以通过单击 "滚动更新" 或 "拇指向下" 图标来提供每个小组件的反馈。

## <a name="next-steps"></a>后续步骤

- 有关商业应用商店中提供的分析报表的概述，请参阅 [在合作伙伴中心访问商业市场的分析报表](./partner-center-portal/analytics.md)。
- 有关为产品/服务汇总市场活动的聚合数据的图表、趋势和值，请参阅[商业市场分析中的“摘要”仪表板](./summary-dashboard.md)。
- 有关以图形和可下载格式显示的订单的信息，请参阅 [商业 marketplace 分析中的 "订单" 仪表板](./orders-dashboard.md)。
- 对于虚拟机 (VM) 提供使用情况和计量计费指标，请参阅 [商业 marketplace 分析中的使用情况仪表板](./usage-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](./partner-center-portal/downloads-dashboard.md)。
- 若要查看有关 Azure Marketplace 中产品/服务的客户反馈和 Microsoft AppSource 的综合视图，请参阅 [合作伙伴中心的分级 & 审查分析仪表板](./partner-center-portal/ratings-reviews.md)。
- 有关商业 marketplace 分析的常见问题以及针对数据术语的综合性字典，请参阅 [商业市场分析术语和常见问题](./analytics-faq.md)。