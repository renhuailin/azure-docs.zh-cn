---
title: 更改 Azure 订阅套餐
description: 了解如何更改 Azure 订阅并切换到其他套餐。
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/27/2021
ms.author: banders
ms.openlocfilehash: 7388ee42195d93a9b7d9a64567f25be91b0eb38d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111949"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>将 Azure 订阅更改为其他套餐

作为具有[即用即付费订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)的客户，你可以在 Azure 门户中将 Azure 订阅切换到其他套餐。 例如，可以通过此功能充分利用 [Visual Studio 订户的每月信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

如果 Visual Studio 订阅已过期，可以切换到[即用即付订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。

只想从免费试用版升级？ 请参阅[升级订阅](upgrade-azure-subscription.md)。

## <a name="whats-supported"></a>支持的操作

可从即用即付订阅切换为：

- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

还可以从过期的 [Visual Studio Enterprise 订阅](https://azure.microsoft.com/offers/ms-azr-0063p/)切换到[即用即付订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。 

> [!NOTE]
> 有关其他产品/服务更改，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="switch-from-a-pay-as-you-go-subscription"></a>从即用即付订阅切换

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到“订阅”，然后选择即用即付订阅。
1. 在页面顶部，选择“切换套餐”。 仅当具有即用即付费订阅并已完成第一个计费周期时，此选项才可用。  
    :::image type="content" source="./media/switch-azure-offer/switch-offer.png" alt-text="显示订阅详细信息和“切换套餐”选项的屏幕截图。" lightbox="./media/switch-azure-offer/switch-offer.png" :::
1. 从订阅可切换到的套餐的列表中选择所需套餐。 此列表因帐户的关联成员资格而异。 如果没有任何可用的产品/服务，请检查 [可以切换到的可用产品/服务的列表](#whats-supported)，并确保具有合适的成员身份。 然后选择“下一步”。
    :::image type="content" source="./media/switch-azure-offer/select-offer.png" alt-text="屏幕截图显示在何处选择要切换到的套餐。" lightbox="./media/switch-azure-offer/select-offer.png" :::
    根据要切换到的套餐，你可能会看到一条有关切换所造成的后果的备注。 继续操作之前，请仔细查看此列表并遵循相关说明。 可能还需要验证电话号码。
1. 查看任何说明或验证电话号码后，选择“切换套餐”。
1. 订阅现在已经切换到新的套餐。

## <a name="convert-a-visual-studio-enterprise-subscription"></a>转换 Visual Studio Enterprise 订阅

Visual Studio Enterprise 订阅过期时，会禁用该订阅。 若要继续将 Azure 服务与订阅一起使用，必须将订阅转换为即用即付模式。

>[!NOTE]
> - 升级到即用即付订阅后，将停止向你提供每月额度，并开始根据你的每月使用量按月向你收费（通过你之前的付款方式）。
> - 切换到即用即付订阅时，会丢失使用情况和计费信息。 建议在切换套餐之前[保存过去所有的发票和使用情况数据](download-azure-invoice-daily-usage-date.md)。

转换订阅：

1. 导航到“订阅”，然后选择你的 Visual Studio Enterprise 订阅。
1. 页面顶部显示一个横幅，其中指出 `Your Visual Studio subscription is expired and has been disabled. To continue using this subscription, please click here to convert to a pay-as-you-go subscription`。 选择该横幅。  
    :::image type="content" source="./media/switch-azure-offer/visual-studio-expired-banner.png" alt-text="显示过期订阅的屏幕截图，其中包含提示进行转换的横幅。" lightbox="./media/switch-azure-offer/visual-studio-expired-banner.png" :::
1. 在“切换套餐”页面上，选择套餐选项卡，选择即用即付选项，然后选择“下一步”。  
    :::image type="content" source="./media/switch-azure-offer/select-offer-pay-as-you-go.png" alt-text="显示“切换套餐”选项的屏幕截图。" lightbox="./media/switch-azure-offer/select-offer-pay-as-you-go.png" :::
1. 在“选择付款方式”选项卡上，选择“选择付款方式”，输入付款方式信息，然后选择“下一步 。  
1. 在“查看和转换”页面上，可以选择更改订阅名称。 查看协议信息，选择“我同意”选项，然后在页面底部选择“切换套餐” 。  
    :::image type="content" source="./media/switch-azure-offer/switch-offer-visual-studio-agreement.png" alt-text="屏幕截图显示“审核和转换”选项卡，其中含订阅名称和协议接受内容。" lightbox="./media/switch-azure-offer/switch-offer-visual-studio-agreement.png" :::

导航到“订阅”，查看新转换的订阅。


## <a name="frequently-asked-questions-for-pay-as-you-go-subscriptions"></a>即用即付订阅的常见问题解答

以下部分回答从即用即付订阅转换为其他模式的常见问题。

### <a name="what-is-an-azure-offer"></a>什么是 Azure 产品/服务？

Azure 套餐是用户拥有的 Azure 订阅 *类型*。 例如，[具有即用即付率的订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p/)和 [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) 都是 Azure 套餐。 每个产品/服务附带不同的[条款](https://azure.microsoft.com/support/legal/offer-details/)，有些还附带特殊的权益。 订阅详细信息页上显示了订阅的套餐。

:::image type="content" source="./media/switch-azure-offer/subscription-details.png" alt-text="显示套餐类型的订阅详细信息页" lightbox="./media/switch-azure-offer/subscription-details.png" :::

### <a name="why-dont-i-see-the-switch-offer-option"></a>为什么我看不到“切换套餐”选项？

在以下情况下，可能看不到“切换套餐”选项：

* 你没有[采用即用即付费率的订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。 目前，只有采用即用即付费率的订阅才能转换为其他套餐。
  * 如果使用的是[免费试用版](https://azure.microsoft.com/free/)，请了解如何[升级到即用即付](upgrade-azure-subscription.md)。
  * 若要从其他订阅切换套餐，[请与支持人员联系](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
* 仍处于第一个计费周期；必须等到第一个计费周期结束，然后才能切换套餐。

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>为什么会出现“你所在的国家或地区目前没有可用的产品/服务”？

* 你可能没有进行任何产品/服务切换的资格。 请查看[可切换的可用产品/服务列表](#whats-supported)，确保已通过 Visual Studio 或 Bizspark 激活了正确的权益。
* 某些套餐可能并非在所有国家/地区都可用。

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>切换 Azure 产品/服务会对我的服务和帐单进行哪些操作？

下面详述了切换 Azure 套餐时发生的情况。

#### <a name="no-service-downtime"></a>服务不会中断

与订阅关联的任何用户都不会出现服务无法使用的情况。 但是，切换到的产品/服务可能会有一些限制。 例如，某些套餐禁止用于生产，因此需要将生产资源移到另一个订阅。

#### <a name="quota-increases-are-reset"></a>配额增加被重置

切换套餐时将重置任何[超过默认限制的限额或配额增加](../../azure-portal/supportability/resource-manager-core-quotas-request.md)。 即使更多资源超出默认限制也不会中断服务。 例如，如果订阅使用 200 个内核，那么切换套餐会将内核配额重置回默认的 20 个内核。 使用 200 个内核的 VM 不受影响，将继续运行。 但如果不提出其他增加配额的请求，则将无法预配更多内核。

#### <a name="billing"></a>计费

在转换当天，将针对所有未付费用生成发票。 之后，按新套餐的定价条款对订阅进行计费。 订阅计费周年会变为更改套餐的日期。 更改产品/服务之前的使用情况与计费数据不会被保留，因此建议在切换之前先下载一个副本。

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>能否从采用即用即付费率的订阅迁移到云解决方案提供商 (CSP) 或企业协议 (EA)？

* 若要迁移到 CSP，请参阅[在订阅者与 CSP 之间转移 Azure 订阅](transfer-subscriptions-subscribers-csp.md)。
* 如果你有即用即付订阅（Azure 套餐 ID MS-AZR-0003P）或采用即用即付费率的 Azure 计划（Azure 套餐 ID MS-AZR-0017G）并且想要迁移到 EA 注册，请让注册管理员将你的帐户添加到 EA。 按照邀请电子邮件中的说明，根据 EA 注册过程来移动订阅。 有关详细信息，请参阅[更改 Azure 订阅或帐户所有权](ea-portal-administration.md#change-azure-subscription-or-account-ownership)。

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>是否可将数据和服务迁移到新订阅？

* 可以迁移直接将资源迁移到新订阅，具体请参阅[将资源移到新资源组或订阅中](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。
* 要将 Azure 订阅的所有权及其包含的所有内容转让给其他某人，请参阅[转让 Azure 订阅的所有权](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [开始分析成本](../costs/quick-acm-cost-analysis.md)
