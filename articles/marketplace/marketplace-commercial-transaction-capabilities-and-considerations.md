---
title: Microsoft 商业市场交易功能
description: 本文介绍商业市场交易选项的定价、计费、开票和支出注意事项。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 77ec7a7dad4b215ae22bf3766172f1e92e932593
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736195"
---
# <a name="commercial-marketplace-transact-capabilities"></a>商业市场交易功能

本文介绍通过商业市场销售的可交易产品/服务的定价、计费、开票和支出注意事项。 有关发布不可交易（免费或 BYOL）的产品/服务的信息，请参阅[列表选项简介](determine-your-listing-type.md)。

## <a name="transactions-by-listing-option"></a>按列表选项统计的交易量

发布者或 Microsoft 负责管理商业市场中的产品/服务的软件许可证交易。 你为产品/服务选择的列表选项将决定交易的管理方。 有关每个发布选项的可用性和说明，请参阅[列表选项简介](determine-your-listing-type.md)。

### <a name="transact-publishing-option"></a>交易发布选项

选择通过 Microsoft 销售可利用 Microsoft 商业功能，并提供从发现和评估到购买和实现的端到端体验。 可交易产品/服务是指在产品/服务中 Microsoft 代表发布者促成以资金换取软件许可证的交易。 可交易的产品/服务针对现有的 Microsoft 订阅或信用卡进行计费，允许 Microsoft 代表发布者主持云市场交易。

在合作伙伴中心创建新产品/服务时，将要选择交易选项。 仅在产品/服务类型可供交易时，此选项才会显示。

## <a name="transact-overview"></a>交易概述

使用交易选项时，Microsoft 支持向客户的 Azure 订阅销售第三方软件并部署部分类型的产品/服务。 在选择产品/服务的定价模型时，发布者必须考虑基础结构费用和自己的软件许可费用。

目前以下产品/服务类型支持交易发布选项：

| 产品/服务类型 | 计费方式 | 按流量计费 | 定价模型 |
| ------------ | ------------- | ------------- | ------------- |
| Azure 应用程序 <br>（托管的应用程序） | 每月一次 | 是 | 基于使用情况 |
| Azure 虚拟机 | 按月计费* | 否 | 基于使用情况，BYOL |
| 软件即服务 (SaaS) | 按月计费和按年计费 | 是 | 固定费率、按用户定价，基于使用情况定价。 |
|||||

\* Azure 虚拟机产品/服务支持基于使用情况的计费计划。 这些计划根据每个核心、每个核心大小或每个市场和核心大小的使用情况按小时使用订阅，按月计费。

### <a name="metered-billing"></a>按流量计费

除了合同（权利）中包含的按月收费或按年收费的方式之外，你还可使用市场计量服务指定即用即付（基于消耗量）的收费方式。 你可针对你指定的市场计量服务维度（如带宽、票证或已处理的电子邮件）收取使用费用。 有关对 SaaS 产品/服务按流量计费的详细信息，请参阅[使用商业市场计量服务对 SaaS 按流量计费](./partner-center-portal/saas-metered-billing.md)。 有关对 Azure 应用程序产品/服务按流量计费的详细信息，请参阅[对托管的应用程序按流量计费](marketplace-metering-service-apis.md)。

### <a name="billing-infrastructure-costs"></a>计费基础结构成本

对于“虚拟机”和“Azure 应用程序”，将向客户的 Azure 订阅收取 Azure 基础结构使用费。 在客户的发票上，基础结构使用费与软件提供商的许可费分开计价和显示。

对于 SaaS 应用，发布者必须将 Azure 基础结构使用费用和软件许可费用作为单个成本项进行考虑。 它表示为客户的固定费用。 Azure 基础结构使用情况由发布者直接管理并对其计费。 客户将无法看到实际的基础结构使用费。 发布者通常选择将 Azure 基础结构使用费捆绑到其软件许可证定价中。 不计量软件许可费，它也不基于用户消耗量。

## <a name="pricing-models"></a>定价模型

根据使用的交易选项，订阅费用如下所示：

- 订阅定价：软件许可证费用显示为每月或每年的周期性订阅费用，采用固定费率或按席位收取。 周期性订阅费用不会因客户在期中取消或未使用服务而按比例计算。 如果客户在订阅期的中间升级或降级订阅，则可能会按比例计算周期性订阅费用。
- 基于使用情况的定价：对于 Azure 虚拟机产品/服务，将根据客户对产品/服务的使用程度向其收费。 对于虚拟机映像，将针对客户从 VM 映像部署虚拟机的使用情况，根据发布者规定的费率，按小时向客户收取 Azure 市场费用。 不同虚拟机大小的小时费用可能一致也可能不同。 不足一小时按分钟收费。 计划按月计费。
- 计量定价：对于 Azure 应用程序产品/服务和 SaaS 产品/服务，发布者可使用[市场计量服务](marketplace-metering-service-apis.md)，根据其配置的自定义计量维度对消耗量计费。 这些更改是对合同（权利）中包含的按月收费或按年收费方式的补充。 自定义计量维度的示例包括带宽、票证或已处理的电子邮件等。 发布者可为每个计划定义一个或多个计量维度，但每个产品/服务最多只能定义 30 个计量维度。 发布者根据产品/服务中定义的各项计量维度，负责跟踪每个客户的使用情况。 事件应该在一小时内报告给 Microsoft。 Microsoft 将在适用帐单周期内基于发布者报告的使用情况信息向客户收费。

> [!NOTE]
> 根据使用解决方案后的使用量计费的产品/服务不支持退款。

想要更改与产品/服务关联的使用费用的发布者，应首先从商业市场中删除该产品/服务（或该产品/服务中的特定计划）。 删除产品/服务应该根据 [Microsoft 发布者协议](/legal/marketplace/msft-publisher-agreement)的要求进行。 然后，发布者可以发布包含新使用费用的新产品/服务（或产品/服务中的计划）。 有关删除产品/服务或计划的信息，请参阅[停止分发产品/服务或计划](./update-existing-offer.md#stop-distribution-of-an-offer-or-plan)。

### <a name="determine-offer-type-and-pricing-plan"></a>确定产品/服务类型和定价计划

使用此流程图可确定相应的可交易产品/服务类型和定价计划，以在商业市场中销售软件。 此图表假定发布者清楚地了解软件交付和许可/计费模型。

通常，如果客户只想订阅服务并在线使用，则 SaaS 产品/服务非常合适。 借助 SaaS，发布者可在云基础结构中托管软件，从而管理技术基础结构、应用软件和数据管理职责。

如果希望客户在其自己的云基础结构中部署、管理和运行打包应用或服务（作为 ARM 模板中的 VM 映像和/或其他 Azure 服务），则虚拟机和 Azure 应用程序产品/服务非常合适。

[![显示用于确定产品/服务类型和定价计划的流程图。](media/commercial-marketplace-plans/offer-type-and-pricing-plan-flowchart.png)](media/commercial-marketplace-plans/offer-type-and-pricing-plan-flowchart.png#lightbox)
&nbsp;&nbsp;&nbsp;<sup>(1)</sup> 联系 [Microsoft Office Hours](https://microsoftcloudpartner.eventbuilder.com/MarketplaceDeveloperOfficeHours) 或[支持](./support.md)。<br>
&nbsp;&nbsp;&nbsp;<sup>(2)</sup> VM 产品/服务映像可包含在 Azure 应用产品/服务中，以提高定价灵活性。<br>
&nbsp;&nbsp;&nbsp;<sup>(3)</sup> 客户支付基础结构成本，因为对于 VM 和 Azure 应用产品/服务，Azure 服务部署在客户租户上。

### <a name="usage-based-and-subscription-pricing"></a>基于使用情况和订阅定价

将产品/服务发布为基于使用情况的交易或订阅交易时，Microsoft 将提供技术和服务以处理软件许可证的购买、退货和退款事宜。 在这种情况下，发布者出于上述目的授权 Microsoft 充当代理人。 发布者允许 Microsoft 促成软件许可交易。 发布者作为销售方、提供方、分销方和许可方，将保留你指定的信息。

Microsoft 使客户能在 Microsoft 商业市场和你的最终用户许可协议的条款和条件约束下订购、许可和使用你的软件。 创建产品/服务时，你必须提供自己的最终用户许可协议，或者选择[标准协定](./standard-contract.md)。

### <a name="free-software-trials"></a>免费软件试用

在交易发布方案中，可免费提供软件许可证 30 天或 120 天，具体取决于订阅。 将更改客户以获得适用的 Azure 基础结构使用情况。

### <a name="examples-of-pricing-and-store-fees"></a>定价和存储费用示例

**基于使用情况**

基于使用情况的定价的成本构成如下：

| **许可证费用** | **每小时 $1.00** |
|---------|---------|
| Azure 使用费用（D1/1 核） | 每小时 $0.14 |
| *由 Microsoft 向客户收费* | *每小时 $1.14* |
||

在此场景中，Microsoft 按每小时 $1.14 收取所发布的 VM 映像的使用费用。

| **Microsoft 收费** | **每小时 $1.14**  |
|---------|---------|
| Microsoft 将许可证费用的 97% 支付给你 | 每小时 0.97 美元 |
| Microsoft 保留许可证费用的 3%  |  每小时 0.03 美元 |
| Microsoft 保留 100% 的 Azure 使用费 | 每小时 $0.14 |
||

**自带许可 (BYOL)**

BYOL 的成本构成如下：

| **许可证费用** | **许可证费用由你协商确定和收取** |
|---------|---------|
|Azure 使用费用（D1/1 核）    |   每小时 $0.14     |
| *由 Microsoft 向客户收费* | *每小时 $0.14* |
||

在此场景中，Microsoft 按每小时 $0.14 收取所发布的 VM 映像的使用费用。

| **Microsoft 收费** | **每小时 $0.14** |
|---------|---------|
| Microsoft 保留 Azure 使用费用 | 每小时 $0.14 |
| Microsoft 保留许可证费用的 0% | 每小时 $0.00 |
||

**SaaS 应用订阅**

SaaS 订阅可按固定费率或按用户定价，按月或按年收取费用。 如果为 SaaS 产品/服务启用了“通过 Microsoft 销售”选项，则成本构成如下：

| **许可证费用** | **100.00 美元/月** |
|--------------|---------|
| Azure 使用费用（D1/1 核） | 直接向发布者而不是客户收费 |
| *由 Microsoft 向客户收费* | 100.00 美元/月（发布者必须将所有产生的或者转嫁的基础结构费用纳入许可证费用中） |
||

在此方案中，Microsoft 收取 100.00 美元的软件许可证费用，并支付 97.00 美元。

| **Microsoft 收费** | **100.00 美元/月** |
|---------|---------|
| Microsoft 将许可证费用的 97% 支付给你  | 97.00 美元/月 |
| Microsoft 保留许可证费用的 3% | 3\.00 美元/月 |

### <a name="commercial-marketplace-service-fees"></a>商业市场服务费用

当客户从商业市场购买你的交易产品/服务时，我们将收取 3% 的标准商店服务费用。

### <a name="customer-invoicing-payment-billing-and-collections"></a>客户开具发票、付款、计费和费用收集

开具发票与付款：可以使用客户的首选发票开具方法来递送订阅或 [PAYGO](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) 软件许可证费用。

企业协议：如果客户的首选发票开具方法是 Microsoft 企业协议，则将使用此方法以逐项列出费用的形式对软件许可证费用计费，并与任何 Azure 特定的使用费用分开列出。

信用卡和月度账单：客户可使用信用卡和月度账单付款。 在这种情况下，将像企业协议方案一样对软件许可证进行计费：采用逐项列出费用的形式并与任何特定于 Azure 的使用费分开。

免费额度和货币承诺付款：一些客户在企业协议中选择以货币承诺的方式为 Azure 预付款，或已获得了使用 Azure 的免费信用额度。 尽管可以这些信用额度可用于支付 Azure 使用情况，但它们不能用于支付发布者软件许可证费用。

计费和收款：发布者软件许可证计费使用客户选择的开票方法进行显示，并且遵循开票时间线。 将对没有企业协议的客户就市场软件许可证按月计费。 通过每季度显示的发票对具有企业协议的客户按月计费。

如果选择了订阅或“即用即付”（也称为“基于使用情况”）的定价模型，则 Microsoft 将充当发布者的代理，负责计费、支付和收款的所有方面。

### <a name="publisher-payout-and-reporting"></a>发布者付款和报表

Microsoft 作为代理收集的所有软件许可费用将收取 3% 的应用商店服务费（另行指定的除外），且会在发布者付款时扣除。

客户通常使用企业协议或支持信用卡的即用即付协议进行购买。 协议类型确定计费、发票、费用收集和付款时间。

>[!NOTE]
>在合作伙伴中心的“分析”部分可以找到交易发布选项的所有报告和见解。

#### <a name="billing-questions-and-support"></a>计费问题和支持

有关详细信息和法律政策，请参阅 [Microsoft 发布者协议](/legal/marketplace/msft-publisher-agreement)。 有关计费问题的帮助，请联系[商业市场发布者支持](https://go.microsoft.com/fwlink/?linkid=2165533)。

## <a name="transact-requirements"></a>交易要求

本部分介绍不同产品/服务类型的交易要求。

### <a name="requirements-for-all-offer-types"></a>所有产品/服务类型的要求

- 无论产品/服务采用怎样的定价模型，交易发布选项都需要使用 Microsoft 帐户和财务信息。
- 必需的财务信息包括支出帐户和计税配置文件。

若要详细了解如何设置这些帐户，请参阅[在合作伙伴中心管理商业市场帐户](manage-account.md)。

### <a name="requirements-for-specific-offer-types"></a>特定产品/服务类型的要求

通过 Microsoft 交易的功能仅适用于以下商业市场的产品/服务类型。 此列表提供了这些产品/服务类型可在商业市场中进行交易的要求。

- Azure 应用程序（解决方案模板和托管的应用程序计划）：在某些情况下，Azure 基础结构使用费用将与软件许可证费用分开计费，但会列在相同的帐单上送达客户。 但是，如果为 ISV 基础结构收费配置了托管的应用计划，则会向发布者收取 Azure 资源费用，客户则会收到包含基础结构费用、软件许可证费用和管理服务费用的固定费用。

- Azure 虚拟机：选择免费、BYOL 或基于使用情况的定价模型。 在客户的 Azure 帐单上，Microsoft 将发布者软件许可证费用与隐含的 Azure 基础结构费用分开显示。 Azure 基础结构费用取决于发布者的软件使用情况。

- SaaS 应用程序：必须是多租户解决方案，使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 进行身份验证，并与 [SaaS Fulfillment API](partner-center-portal/pc-saas-fulfillment-api-v2.md) 集成。 Azure 基础结构使用情况直接对你（发布者）进行管理和计费，因此你必须将 Azure 基础结构使用费用和软件许可费用作为单个成本项进行考虑。 有关详细指导，请参阅[如何为商业市场计划 SaaS 产品/服务](plan-saas-offer.md#plans)。

## <a name="private-plans"></a>专用计划

你可为产品/服务创建专用计划，包括协商定价、特定交易定价或自定义配置。

专用计划允许你为特定客户提供高于或低于公开计划的定价。 专用计划可为产品/服务提供折扣或添加奖励金。 将客户的 Azure 订阅列为计划级别，即可向一个或多个客户提供专用计划。

## <a name="next-steps"></a>后续步骤

- 查看按在线商店划分的发布模式，例如，解决方案如何映射到产品/服务类型和配置。
- [发布指南（按产品/服务类型）](publisher-guide-by-offer-type.md)。
- [商业市场产品/服务计划和定价](./plans-pricing.md)