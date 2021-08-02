---
title: SaaS 履行 API 常见问题解答 - Microsoft 商业市场
description: 了解 Microsoft 商业市场对于 Azure 客户订阅 SaaS 产品/服务的一些集成要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/19/2021
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 06debcfbf4404497e17f1446779189b124b0079e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952636"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>有关 SaaS 履行 API 的常见问题

本文介绍了 Microsoft 商业市场对于 Azure 客户订阅 SaaS 产品/服务的一些集成要求。

## <a name="discovery-experience"></a>发现体验

在发布了 SaaS 产品后，Azure 用户就可以在 Azure 市场中发现它。 你的客户可以根据产品类型 (SaaS) 来筛选产品/服务，并发现他们关注的 SaaS 服务。

## <a name="purchase-experience"></a>购买体验

用户在对某个特定 SaaS 服务产生使用意向后，可从 Azure 市场订阅该服务。

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>对于 Azure 用户，在 Azure 市场中订阅 SaaS 产品/服务是什么意思？

它的意思是，用户可以查看与该 SaaS 服务关联的使用条款和隐私声明，并同意按照你（即该 SaaS 产品/服务的发布者）设置的计费条款在收到 Microsoft 发票时为该服务付费。 用户可以使用他们在 Azure 中现有的付款配置文件来支付使用 SaaS 服务所产生的费用。

此功能有多方面的益处。 现在，客户可以通过使用 Microsoft 云平台作为受信任的源，在一个位置完成发现和订阅，而不必对每一个计划要使用的 ISV 软件都进行审查。 此外，客户还可以使用他们现有的付款配置文件，而无需单独为每个 ISV 软件显式付费。

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>在订阅产品/服务时，是否会自动向用户收费？

在订阅 SaaS 产品/服务时，用户已同意通过 Microsoft 平台支付使用 SaaS 服务的费用。 但是，只有在使用该产品/服务时，才会开始收取费用。 用户必须转到该 SaaS 产品/服务，并确认创建帐户，才能开始使用该产品/服务。 然后，你将会通知 Microsoft 开始为此客户 SaaS 订阅计费。

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>在用户订阅 SaaS 产品/服务时，你会怎样收到通知？

在订阅产品/服务后，Azure 用户可以在 Azure 中发现和管理他们所有的产品/服务。 在默认情况下，新订阅的 SaaS 产品/服务的状态会显示为“正在预配，等待履行”。 在此状态下，系统将会提示 Azure 用户执行“配置帐户”操作，以便浏览到 Azure 门户中的 SaaS 订阅管理体验。

在用户选择“配置帐户”时，他们会被重定向到 SaaS 服务网站。 发布者在发布该产品/服务时配置了 URL。 此页面被称为发布者的登陆页。 Azure 用户基于他们在 Azure 中现有的 Azure Active Directory (Azure AD) 凭据来登录 SaaS 登录页面。

> [!IMPORTANT]
> 必须按照[策略](/legal/marketplace/certification-policies?context=/azure/marketplace/context/context)指示，使用 Azure Active Directory、单一登录 (Azure AD SSO) 登录购买用户。 从 Microsoft Graph API 检索到的用户资源上的 `mail` 属性可在 Azure AD 情况下提供联系信息，并为 MASA 提供 `userPrincipalName`。 Azure AD 的“mail”字段可能为空，而且用户可能未记录电子邮件。 在这种情况下，建议检测并请求联系人电子邮件。 你只有这次机会可以获取联系人电子邮件，以便在客户加入过程中或之后联系客户。

在将 Azure 用户重定向到该登陆页后，会将令牌添加到查询 URL。 此令牌生存期较短，且有效期为 24 小时。 然后，你可以检测到该令牌的存在，并调用 Microsoft 的 API 来获取更多与该令牌关联的上下文。

![客户订阅流](media/saas-metering-service-integration-flow-a.png)

若要详细了解有关在 SaaS 产品/服务生命周期中处理交易方案的 API 协定，请参阅 [SaaS 履行 API](pc-saas-fulfillment-api-v2.md)。

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>如何知道用户在 Azure 中订阅哪个 SaaS 产品/服务？

对 `Resolve` API 的响应包括与该 SaaS 订阅相关联的产品/服务和计划信息。

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure 用户如何更改与此 Azure 订阅关联的计划？

* Azure 用户可以直接在 SaaS 体验中或通过 Microsoft 平台来更改与 SaaS 订阅关联的计划。

* 在计费周期内，随时可以执行转换。 系统会提示你确认任何转换，转换在确认之后会立即生效。

* 预付费计划（每月或每年）费率是按比例计算的 。 截至转换时发放的任何超额部分将会在下一张发票中收取费用。 新的超额部分将会根据新计划来发放。

>[!Note]
>如果你不希望支持特定的转换路径，可以阻止降级。

以下序列会在 Azure 客户在 SaaS 体验中更改计划时捕获流：

![客户计划更改流](media/saas-metering-service-integration-flow-b.png)

以下序列会在 Azure 客户在 Microsoft 的在线商店中更改计划时捕获流：

![客户在线商店计划更改流](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure 用户如何从 Azure 订阅关联的计划取消订阅？

Azure 用户可以直接在 SaaS 体验中或者通过 Microsoft 平台来取消订阅已购买的 SaaS 产品/服务。 在用户取消订阅后，从下一个计费周期开始将不再向他们收费。

以下序列在 Azure 客户在 SaaS 体验中取消订阅 SaaS 产品/服务时捕获流：

![在 SaaS 体验中取消订阅](media/saas-metering-service-integration-flow-d.png)

以下序列在 Azure 用户在 Microsoft 在线商店中取消订阅时捕获流：

![客户在 Microsoft 在线商店中取消订阅](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>后续步骤

[Azure 市场计量服务 API](../marketplace-metering-service-apis.md)