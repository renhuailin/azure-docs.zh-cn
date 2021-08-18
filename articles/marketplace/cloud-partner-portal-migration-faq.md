---
title: 有关从云合作伙伴门户转换为合作伙伴中心的常见问题解答 - Microsoft 商业市场
description: 解答有关将产品/服务从云合作伙伴门户转换到合作伙伴中心的常见问题。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: 6c5f67307a39dddcc0dd30732095b4c44278804c
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575561"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>有关从云合作伙伴门户转换为合作伙伴中心的常见问题解答

云合作伙伴门户已转换为合作伙伴中心。 合作伙伴中心提供了一种简单、集成的体验，用于在 [Microsoft AppSource](https://appsource.microsoft.com/) 或 [Azure 市场](https://azuremarketplace.microsoft.com/)上发布新产品/服务，以及用于管理从云合作伙伴门户迁移的现有产品/服务。 云合作伙伴门户的所有功能都可在合作伙伴中心获得。 本文介绍有关此问题的常见问题解答。

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>转换为合作伙伴中心对我而言有什么意义？

你可以继续在合作伙伴中心开展业务：

| 区域 | 更改 |
| --- | --- |
| 帐户 | 无需创建新的合作伙伴中心帐户；你可以使用现有的云合作伙伴门户凭据登录到合作伙伴中心，现在你可在合作伙伴中心管理你的帐户、用户、权限和计费。 发布协议和公司配置文件信息将与付款配置文件信息、用户帐户和权限以及有效产品/服务一起迁移到新的合作伙伴中心帐户。 有关详细信息，请访问[在合作伙伴中心管理商业市场帐户](manage-account.md)。 |
| 产品/服务发布和产品/服务管理体验 | 我们已将你的产品/服务数据从云合作伙伴门户迁移到合作伙伴中心。 你现在可以在合作伙伴中心访问你的产品/服务，合作伙伴中心提供了改进的用户体验和直观的界面。 了解如何[更新商业市场中的现有产品/服务](update-existing-offer.md)。 |
| 你的产品/服务在商业市场的供应情况 | 无更改。 如果你的产品/服务已在商业市场上线，则会继续保持上线状态。 |
| 新的购买和部署 | 无更改。 你的客户可以继续购买和部署你的产品/服务，而不会出现任何中断。 |
| 付款 | 任何购买和部署将继续如往常一样付款给你。 详细了解[在合作伙伴中心收取付款](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context)。 |
| API 与现有[云合作伙伴门户 API](cloud-partner-portal-api-overview.md) 的集成 | 现有云合作伙伴门户 API 仍受支持，现有集成仍可正常工作。 有关详细信息，请访问[是否仍支持云合作伙伴门户 REST API？](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| 分析 | 可以通过在合作伙伴中心查看分析，在商业市场中继续监视销售额、评估性能以及优化产品/服务。 分析报告在 CPP 和合作伙伴中心的显示方式有一些区别。 例如，CPP 中的“Seller Insights”有一个“订单和使用情况”选项卡，该选项卡显示基于使用情况的产品/服务和非基于使用情况的产品/服务的数据，而在合作伙伴中心中，“订单”页有一个针对 SaaS 产品/服务的单独选项卡。 有关详细信息，请访问[在合作伙伴中心访问商业市场分析报告](analytics.md)。 |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>是否需要在合作伙伴中心创建新帐户来管理我的产品/服务？

否，将保留你的帐户。 这意味着如果你当前有合作伙伴，则可使用现有的云合作伙伴门户帐户凭据登录到合作伙伴中心。 请勿创建新帐户，否则在云合作伙伴门户中创建并迁移到合作伙伴中心的任何产品/服务都不会与其关联。

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>合作伙伴中心中哪些页面与我在云合作伙伴门户中所用的页面相对应？

下面是云合作伙伴门户中常用页面的合作伙伴中心链接。 如果将云合作伙伴门户链接保存为书签，则需要对其进行更新。

| 云合作伙伴门户页面 | 云合作伙伴门户页面链接 | 合作伙伴中心页面链接 |
| --- | --- | --- |
| 全部产品/服务页 | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| 所有发布者页面 | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| 发布者资料 | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| “用户”页 | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| “历史记录”页面 | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | 合作伙伴中心尚不支持“历史记录”功能。 |
| 见解仪表板 | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| 支出报表 | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>付款报告差异

以下是已停用的云合作伙伴门户和当前合作伙伴中心之间的付款报告差异：

| 云合作伙伴门户 | 合作伙伴中心 |
| --- | --- |
| **链接**： https://cloudpartner.azure.com/ | **链接**： https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory 和 https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigation**：Insights Payout 中提供的付款报告 | **Navigation**：合作伙伴中心提供的付款报告 - 付款图标 |
| **范围**：<ul><li>对于正在进行、已收取和已支付的收款，每个行项的交易均可见。</li><li>报告 - 显示创建采购订单后的所有行项，包括正在进行的收款和正在进行的计费，以及收款状态和尚不符合付款条件的行项。</li></ul> | **范围**：<ul><li>显示被视为合格收入的行项。</li><li>客户先向 Microsoft 付款，然后 ISV 可以看到付款报告。</li><li>付款报告不会显示正在进行的收款和正在进行的计费。</li></ul> |
| **交易尚未做好付款的准备**：正在进行的计费 | **交易尚未做好付款的准备**：下一次估计付款：付款状态处于未处理状态。 |
| **付款状态**：不适用 | **付款状态**：<ul><li>未处理：收入可以用于付款。</li><li>即将发送：收入将在下一个月的付款中发送给出版商。</li><li>已发送：已将付款发送到银行。</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>在云合作伙伴门户中发布的产品/服务呢？

产品/服务已迁移至合作伙伴中心，在你登录到合作伙伴中心后，即可访问这些产品/服务，但 Dynamics Nav 托管服务和 Cortana Intelligence 产品/服务除外。 如果你的产品/服务已在商业市场上线，将继续保持上线状态，且你的客户将能够继续在不中断的情况下进行购买和部署。 有关更多详细信息，请参阅下一个问题：哪些产品/服务已迁移到合作伙伴中心？。

## <a name="what-offers-were-moved-to-partner-center"></a>哪些产品/服务已迁移到合作伙伴中心？

之前在云合作伙伴门户中受支持的所有产品/服务类型都在合作伙伴中心受支持，但 Dynamics Nav 托管服务和 Cortana Intelligence 产品/服务除外。

对于合作伙伴中心支持的产品/服务类型，所有产品/服务都已迁移，无论其状态如何；草稿、已退市和仅预览版产品/服务也会迁移。

| 产品/服务类型 | 已迁移到合作伙伴中心？ | 后续步骤 |
| --- | --- | --- |
| SaaS | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[为商业市场规划 SaaS 产品/服务](plan-saas-offer.md)。 |
| 虚拟机 | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[规划虚拟机产品/服务](marketplace-virtual-machines.md)。 |
| Azure 应用程序 | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[创建 Azure 应用程序产品/服务](azure-app-offer-setup.md)。 |
| Dynamics 365 Business Central | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[创建 Dynamics 365 Business Central 产品/服务](dynamics-365-business-central-offer-setup.md)。 |
| Dynamics 365 for Customer Engagement 和 PowerApps | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[创建 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务](dynamics-365-customer-engage-offer-setup.md)。 |
| Dynamics 365 for Operations | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[创建 Dynamics 365 for Operations 产品/服务](./dynamics-365-operations-offer-setup.md)。 |
| Power BI 应用 | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[创建 Power BI 应用产品/服务](./power-bi-app-offer-setup.md)。 |
| IoT Edge 模块 | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[创建 IoT Edge 模块产品/服务](iot-edge-offer-setup.md)。 |
| 容器 | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[创建 Azure 容器产品/服务](./azure-container-offer-setup.md)。 |
| 咨询服务 | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[创建咨询服务产品/服务](./create-consulting-service-offer.md)。 |
| 托管服务 | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请访问[为 Microsoft 商业市场计划托管服务产品/服务](./plan-managed-service-offer.md)。 |
| Dynamics NAV 托管服务 | 否 | Microsoft 已将 Dynamics NAV 托管服务演变为 [Dynamics 365 Business Central](/dynamics365/business-central/)，因此我们将 Dynamics NAV 托管服务上线产品/服务从 AppSource 中退市。 这些产品/服务不再被客户发现，也未移动到合作伙伴中心。 若要在 AppSource 中提供你的产品/服务，请将其作为 Dynamics 365 Business Central 产品/服务提供，并将其提交到[合作伙伴中心](https://partner.microsoft.com/)。 有关详细信息，请访问[创建 Dynamics 365 Business Central 产品/服务](dynamics-365-business-central-offer-setup.md)。 |
| Cortana Intelligence | 否 | Microsoft 已演变了 Cortana Intelligence 的产品路线图，因此我们将 Cortana Intelligence 上线产品/服务从 AppSource 中退市。 这些产品/服务不再被客户发现，也未移动到合作伙伴中心。 若要在商业市场中提供你的产品/服务，请将你的产品/服务作为软件即服务 (SaaS) 产品/服务提供，并将其提交到[合作伙伴中心](https://partner.microsoft.com/)。 有关详细信息，请访问[如何为商业市场规划 SaaS 产品/服务](./plan-saas-offer.md)。 |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>我在合作伙伴中心找不到我的云合作伙伴门户产品/服务

在“合作伙伴中心”中看到的内容取决于你注册的计划、你所属的帐户以及分配给你的用户角色和权限。 有很多合作伙伴中心计划可用，且你可以注册多个计划。 你还可以访问具有相同用户凭据的多个帐户。

在云合作伙伴门户中创建的产品/服务在合作伙伴中心的“商业市场”计划下以及用于创建产品/服务的帐户下提供。 若要确保查看的是正确的计划和正确的帐户，请按照以下步骤进行操作。 有关其他故障排除提示，请参阅[管理合作伙伴中心帐户](/partner-center/partner-center-account-setup)。

### <a name="access-the-right-program-in-partner-center"></a>在合作伙伴中心访问正确的计划

1. 使用登录云合作伙伴门户时所用的凭据登录到[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2166002)。 左侧的导航窗格显示与注册的计划相关联的选项。

    示例：假设你有权访问三个计划：MPN 计划、引荐计划和商业市场计划。 登录到合作伙伴中心时，会在导航窗格中看到这三个计划。

2. 选择“商业市场” > “概述”以访问你的产品/服务。

    如果左侧导航窗格中未显示“商业市场计划”，则可能是使用的帐户错误。 按照下一部分中的步骤访问正确的帐户。

    [![显示“合作伙伴中心概述”菜单的屏幕截图](media/cpp-pc-faq/overview-menu.png "显示合作伙伴中心概述菜单")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>在合作伙伴中心内访问正确的帐户

如果你是多个帐户的成员，将在合作伙伴中心中看到一个帐户选取器按钮，该按钮由左侧导航菜单中的两个箭头标记。 选择“帐户选取器”按钮以查看你所属的所有帐户的列表。 选择列表中的任何帐户以切换到该帐户，并查看与该帐户相关的所有计划和信息。 如果在导航菜单中看不到帐户选取器，则你是单个帐户的成员。

[![屏幕截图显示合作伙伴中心“帐户选取器”按钮。](media/cpp-pc-faq/picker-button.png "显示合作伙伴中心“帐户选取器”按钮")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>如何创建新产品/服务？

在[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165935)访问商业市场计划，以创建新产品/服务。

[![显示合作伙伴中心“概述”菜单的屏幕截图。](media/cpp-pc-faq/new-offer.png "显示合作伙伴中心概述菜单")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>我无法登录，需要打开一个支持票证

如果你无法登录帐户，可以在此处打开一个[支持票证](https://go.microsoft.com/fwlink/?linkid=2165533)。

## <a name="where-are-instructions-for-using-partner-center"></a>使用合作伙伴中心的说明位于何处？

请转到[商业市场文档](index.yml)。 若要查看有关在合作伙伴中心创建产品/服务的帮助文章，请展开“创建并管理产品/服务”。

## <a name="what-are-the-publishing-and-offer-management-differences"></a>发布和产品/服务管理之间有哪些区别？

下面是云合作伙伴门户和合作伙伴中心之间的一些区别。

### <a name="modular-publishing-capabilities"></a>模块化发布功能

合作伙伴中心提供了一个模块化发布功能，你可用它来选择你想要发布的更改，而不是总是一次性发布所有更新。 例如，以下屏幕截图显示了选择发布的唯一更改是指对“属性”和“产品/服务列表”的更改 。 你在“预览版”页面中所做的更改将不会发布。

[![屏幕截图显示合作伙伴中心“审阅和发布”页。](media/cpp-pc-faq/review-page.png "显示合作伙伴中心“审阅和发布”页")](media/cpp-pc-faq/review-page.png#lightbox)

未发布的更新将保存为草稿。 在上线提供给公众之前，可继续使用产品/服务预览来验证你的产品/服务。

### <a name="enhanced-preview-options"></a>强化预览选项

合作伙伴中心包含一个[“比较”功能](update-existing-offer.md#compare-changes-to-your-offer)，其中有改进后的筛选选项。 这使你能够根据产品/服务的预览版本和上线版本。

[![屏幕截图显示合作伙伴中心比较功能。](media/cpp-pc-faq/compare.png "显示合作伙伴中心比较功能")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>品牌和导航更改

你将注意到有一些品牌方面的更改。 例如，“SKU”在合作伙伴中心的品牌显示为“计划”。

[![屏幕截图显示合作伙伴中心“计划”页。](media/cpp-pc-faq/plans.png "显示合作伙伴中心“计划”页")](media/cpp-pc-faq/plans.png#lightbox)

此外，你之前在云合作伙伴门户的“市场”和“店面详细信息”（咨询服务、Power BI 应用）页面中提供的信息现收集到合作伙伴中心的“产品/服务列表”页面  ：

[![显示合作伙伴中心产品/服务列表页的屏幕截图。](./media/cpp-pc-faq/offer-listing.png)](./media/cpp-pc-faq/offer-listing.png#lightbox)

你之前在云合作伙伴门户的单个页面中为 SKU 提供的信息现在可能会在合作伙伴中心通过多个页面进行收集：

- “计划设置”页
- 计划列表页面
- 计划可用性页面
- 计划技术配置页面，如下所示：

[![显示合作伙伴中心“技术配置”页。](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

产品/服务 ID 现在显示在产品/服务的左侧导航栏中：

![显示合作伙伴中心产品/服务 ID 位置](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>停止销售产品/服务

你可直接通过合作伙伴中心门户请求在市场上[停止销售某款产品/服务](update-existing-offer.md#stop-selling-an-offer-or-plan)。 可在产品/服务的“产品/服务概述”页面上找到该选项。

[![屏幕截图显示了停止销售产品/服务的合作伙伴中心页。](media/cpp-pc-faq/stop-sell.png "显示用于停止销售产品/服务的合作伙伴中心页")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>是否仍支持云合作伙伴门户 REST API？

云合作伙伴门户 API 已与合作伙伴中心集成，仍可继续使用。 转换到合作伙伴中心会引入小更改。 请查看下表，确保你的代码可在合作伙伴中心继续正常工作。

| API | 更改说明 | 影响 |
| --- | --- | --- |
| POST 发布、上线、取消 | 对于已迁移的产品/服务，响应标头将采用不同的格式，但将以相同的方式继续工作，这表示有一个相对路径用于检索操作状态。 | 在发送某产品/服务的任何相应 POST 请求时，位置标头将采用以下两种格式之一，具体取决于产品/服务的迁移状态： <ul><li>未迁移的产品/服务：`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>已迁移的产品/服务：`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET 操作 | 对于之前支持在响应中使用“notification-email”字段的产品/服务，此字段将被弃用，且将不对已迁移的产品/服务返回此字段。 | 对于已迁移的产品/服务，我们将不再向请求中指定的一列电子邮件地址发送通知。 相反，API 服务将与 合作伙伴中心内的通知电子邮件进程保持一致来发送电子邮件。 具体而言，操作进度通知将发送至合作伙伴中心内在你的帐户设置的卖家联系人信息部分中设定的电子邮件地址。<br><br>请确保合作伙伴中心的[帐户设置](https://partner.microsoft.com/dashboard/account/management)中卖家联系人信息部分设定的电子邮件地址正确，以接收通知。 |
|||