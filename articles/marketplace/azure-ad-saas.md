---
title: 商业市场中的 Azure Active Directory 和 SaaS 可交易产品/服务
description: 了解如何在 Microsoft 商业市场中搭配使用 Azure Active Directory 和 SaaS 可交易产品/服务。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93124911"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>商业市场中的 Azure AD 和 SaaS 可交易产品/服务

Microsoft 基于云的标识和访问管理服务 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 可帮助用户登录和访问内部和外部资源。 在 Microsoft 商业市场中，Azure AD 使所有人都能够更轻松、更安全地使用 SaaS 可交易产品/服务，包括发布者、买家和用户。 借助 Azure AD，发布者可以为其服务型软件 (SaaS) 应用自动化用户预配，之后买家可以自行管理这些预配用户。 

此外，当用户在 Azure AD 中登录应用时，[Azure AD 单一登录](../active-directory/manage-apps/what-is-single-sign-on.md) (SSO) 还提供了安全性和便利。 更快的参与和优化的体验还能使买家和用户在与发布者的 SaaS 应用进行首次交互时就产生信任感。 这会留下正面的印象，从而吸引公众注意，促进再次购买。

按照本文中的指导进行操作，有助于在商业市场中认证你的 SaaS 产品/服务。 有关认证的详细信息，请阅读详细的[商业市场认证策略](/legal/marketplace/certification-policies#100-general)，包括[特定于 SaaS](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) 的策略。

## <a name="before-you-begin"></a>开始之前

在合作伙伴中心[创建 SaaS 产品/服务](./create-new-saas-offer.md)时，可以从将显示在产品/服务列表中的一组特定列表选项中进行选择。 此选择将决定产品/服务在商业市场中的交易方式。 通过 Microsoft 销售的产品/服务称为可交易产品/服务。 我们会代表你就所有可交易产品/服务向客户收费。 如果选择通过 Microsoft 进行销售，并委托我们代表你处理交易（“是”选项），那么你已选择创建可交易产品/服务，且本文很适合你。 建议你通读本文。

如果你选择只是通过商业市场上架你的产品/服务，但独立处理交易（“否”选项），那么我们提供三个选项指明潜在客户访问产品/服务的方式：“立即获取(免费)”、“免费试用”，以及“与我联系”。 如果选择“立即获取(免费)”或“免费试用”，则本文不适合你。 但是，可参阅[针对商业市场中的免费或试用版 SaaS 产品/服务构建登陆页](./azure-ad-free-or-trial-landing-page.md)，以了解详细信息。 如果选择“与我联系”，则发布者没有直接责任。 继续在合作伙伴中心创建产品/服务。

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>如何针对 SaaS 产品/服务搭配使用 Azure AD 和商业市场

Azure AD 可实现商业市场解决方案的无缝购买、履行和管理。 图 1 显示了发布者、买家和用户如何交互以购买和激活订阅。 它还显示了客户如何使用和管理其从商业市场获取的 SaaS 应用程序。 在此图中，买家是从商业市场发起购买的 SaaS 应用程序用户。

如图 1 所示，当买家选择产品/服务时，他们启动了一条工作流链，其中包括购买、订阅和用户管理。 在此链中，你作为发布者负责满足某些要求，而 Microsoft 在关键点提供支持。

***图 1：在商业市场中针对 SaaS 产品/服务使用 Azure AD***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="演示购买管理、订阅管理和可选的用户管理过程步骤。":::

以下各部分详细介绍了每个过程步骤的要求。

## <a name="process-steps-for-purchase-management"></a>购买管理过程步骤

此图显示了购买管理的四个过程步骤。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="演示购买管理的四个过程步骤。":::

下表详细介绍了购买管理过程步骤。

| 过程步骤 | 发布者操作 | 建议或要求（针对发布者） |
| ------------ | ------------- | ------------- |
| 1.买家使用 Azure ID 标识登录到商业市场，并选择一个 SaaS 产品/服务。 | 发布者无需进行操作。 | 不适用 |
| 2.购买后，买家在 Azure 市场中选择“配置帐户”或在 AppSource 中选择“立即配置”，此操作会将买家定向到此产品/服务的发布者登陆页。 买家必须能够使用 Azure AD SSO 登录到发布者的 SaaS 应用程序，并且必须仅被要求最低程度同意，无需 Azure AD 管理员批准。 | 设计产品/服务的[登陆页](azure-ad-transactable-saas-landing-page.md)，使其接收使用 Azure AD 或 Microsoft 帐户 (MSA) 标识的用户，并辅助所需的任何其他预配或设置。 | 必需 |
| 3.发布者从 SaaS 履行 API 请求购买的详细信息。 | 使用从登陆页的应用程序 ID 生成的[访问令牌](./partner-center-portal/pc-saas-registration.md)，[调用解析终结点](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)来检索有关购买的详细信息。 | 必需 |
| 4.通过 Azure AD 和 Microsoft Graph API，发布者会收集在发布者的 SaaS 应用程序中预配买家所需的公司和用户详细信息。  | 分解 Azure AD 用户令牌以查找名称和电子邮件，或[调用 Microsoft Graph API](/graph/use-the-api) 并使用委托的权限来[检索](/graph/api/user-get)有关登录用户的信息。 | 必需 |
||||

## <a name="process-steps-for-subscription-management"></a>订阅管理过程步骤

下图显示了订阅管理的两个过程步骤。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="演示订阅管理的两个过程步骤。":::

下表详细介绍了订阅管理过程步骤。

| 过程步骤 | 发布者操作 | 建议或要求（针对发布者） |
| ------------ | ------------- | ------------- |
| 5.发布者通过 SaaS 履行 API 管理 SaaS 应用程序的订阅。 | 通过 [SaaS 履行 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md) 处理订阅更改和其他管理任务。<br><br>此步骤需要过程步骤 3 中所述的访问令牌。 | 必需 |
| 6.使用计量定价时，发布者将使用情况事件发送到计量服务 API。 | 如果 SaaS 应用使用基于使用情况的计费方式，请通过[市场计量服务 API](./partner-center-portal/marketplace-metering-service-apis.md) 发出使用情况通知。<br><br>此步骤需要步骤 3 中所述的访问令牌。 | 针对计量必需 |
||||

## <a name="process-steps-for-user-management"></a>用户管理过程步骤

下图显示了用户管理的三个过程步骤。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="演示用户管理的三个可选过程步骤。":::

过程步骤 7 到 9 是可选的用户管理过程步骤。 它们为支持 Azure AD 单一登录 (SSO) 的发布者提供额外的好处。 下表详细介绍了用户管理过程步骤。

| 过程步骤 | 发布者操作 | 建议或要求（针对发布者） |
| ------------ | ------------- | ------------- |
| 7.买家公司的 Azure AD 管理员可以选择通过 Azure AD 来管理用户和组的访问权限。 | 如果已为用户设置 Azure AD SSO（步骤 9），则无需发布者进行任何操作就可启用。 | 不适用 |
| 8.Azure AD 预配服务在 Azure AD 与发布者的 SaaS 应用程序之间传达更改。 | [实现 SCIM 终结点](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md)，以便在添加或删除用户时接收来自 Azure AD 的更新。 | 建议 |
| 9.在授权和预配应用程序后，买家公司的用户可以使用 Azure AD SSO 登录到发布者的 SaaS 应用程序。 | 通过[使用 Azure AD SSO](../active-directory/manage-apps/what-is-single-sign-on.md)，用户可以使用一个帐户登录一次发布者的 SaaS 应用程序。 | 建议 |
||||

## <a name="next-steps"></a>后续步骤

- [为商业市场中的可交易 SaaS 产品/服务构建登陆页](azure-ad-transactable-saas-landing-page.md)
- [针对商业市场中的免费或试用版 SaaS 产品/服务构建登陆页](azure-ad-free-or-trial-landing-page.md)
- [如何在商业市场中创建 SaaS 产品/服务](create-new-saas-offer.md)