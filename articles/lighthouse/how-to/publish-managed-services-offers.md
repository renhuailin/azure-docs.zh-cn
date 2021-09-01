---
title: 将托管服务产品发布到 Azure 市场
description: 了解如何发布可将客户添加到 Azure Lighthouse 的托管服务产品。
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: af5ca37d312f5bdfcfae179997b920a466f01462
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742189"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>将托管服务产品发布到 Azure 市场

本文介绍如何使用合作伙伴中心的[商业市场](../../marketplace/overview.md)计划向 [Azure 市场](https://azuremarketplace.microsoft.com)发布公共或私有托管服务产品。 购买该产品/服务的客户将委托订阅或资源组，允许你通过 [Azure Lighthouse](../overview.md) 进行管理。

## <a name="publishing-requirements"></a>发布要求

若要创建和发布产品/服务，你需要拥有有效的[合作伙伴中心帐户](../../marketplace/create-account.md)。 若无帐户，可以按照[注册流程](https://aka.ms/joinmarketplace)中的步骤，先在合作伙伴中心创建帐户，再加入商业市场计划。

根据[托管服务产品认证要求](/legal/marketplace/certification-policies#700-managed-services)，你必须达到[白银级或黄金级云平台资格级别](/partner-center/learn-about-competencies)，或是 [Azure 专家 MSP](https://partner.microsoft.com/membership/azure-expert-msp)，才能发布托管服务产品。 此外，你[还必须输入潜在客户目标](../../marketplace/plan-managed-service-offer.md#customer-leads)，此后，每次客户部署你的产品/服务时，CRM 系统中都会创建一条客户记录。

如果你不想将产品/服务发布到 Azure 市场，或不满足所有要求，可使用 Azure 资源管理器模板手动添加客户。 有关详细信息，请参阅[添加客户到 Azure Lighthouse](onboard-customer.md)。

可参考下表确定是否通过发布托管服务产品或使用 Azure 资源管理器模板来添加客户。

|**注意事项**  |**托管服务产品**  |**ARM 模板**  |
|---------|---------|---------|
|需要[合作伙伴中心帐户](../../marketplace/create-account.md)   |是         |否        |
|需满足[白银级或黄金级云平台资格级别](/partner-center/learn-about-competencies)或 [Azure 专家 MSP](https://partner.microsoft.com/membership/azure-expert-msp)      |是         |否         |
|通过 Azure 市场提供给新客户     |是     |否       |
|可以只向特定客户发布产品/服务     |是（仅适用于私有产品/服务，不适用于通过云解决方案提供商 (CSP) 计划的经销商获得的订阅）         |是         |
|需要客户接受 Azure 门户     |是     |否   |
|可以借助自动化来加入多个订阅、资源组或客户 |否     |是    |
|立即访问新内置角色和 Azure Lighthouse 功能     |不一定（通常会延迟一段时间才能访问）         |是         |
|客户可在 Azure 门户中查看和接受更新后的产品/服务 | 是 | 否 |

> [!NOTE]
> 托管服务产品可能在 Azure 政府版和其他国家云中不可用。

## <a name="create-your-offer"></a>创建产品/服务

有关如何创建产品（包括需要提供的所有信息和资产）的详细说明，请参阅[创建托管服务产品/服务](../../marketplace/create-managed-service-offer.md)。

若要了解常规发布流程，请参阅[商业市场文档](../../marketplace/overview.md)。 还应查看[商业市场认证策略](/legal/marketplace/certification-policies)，特别是[托管服务](/legal/marketplace/certification-policies#700-managed-services)部分。

客户添加产品/服务后，他们可以委托一个或多个订阅或资源组，然后将其[添加到 Azure Lighthouse](#the-customer-onboarding-process)。

> [!IMPORTANT]
> 托管服务产品中的每个计划都有“清单详情”部分，在该部分，你可以定义租户中哪些 Azure Active Directory (Azure AD) 实体有权访问委托资源组和/或访问购买该计划的客户的订阅。 需注意的是，此处添加的任何组（或用户或服务主体）对每个购买此计划的客户都拥有相同权限。 若要分配不同组与每个客户对接，可针对每个客户单独发布[私有计划](../../marketplace/private-offers.md)。 请记住，私有计划不适用于通过云解决方案提供商 (CSP) 计划的经销商获得的订阅。

## <a name="publish-your-offer"></a>发布产品/服务

完成所有部分后，下一步就是将产品/服务发布到 Azure 市场。 选择“发布”按钮启动产品/服务上线过程。 有关此过程的更多信息，请点击[此处](../../marketplace/review-publish-offer.md)。

你可以随时[发布产品/服务](../../marketplace/update-existing-offer.md)的更新版本。 例如，你可能想要向以前发布的产品/服务添加新的角色定义。 当你执行此操作时，已添加该产品/服务的客户会在 Azure 门户中的[服务提供程序](view-manage-service-providers.md)页面上看到一个图标，他们可通过图标知道更新可用。 每个客户都可以[查看更改](view-manage-service-providers.md#update-service-provider-offers)并决定是否要更新到新版本。 

## <a name="the-customer-onboarding-process"></a>客户加入过程

客户添加产品/服务后，他们可以[委托一个或多个特定订阅或资源组](view-manage-service-providers.md#delegate-resources)，然后将它们加入 Azure Lighthouse。 如果客户已接受产品/服务但尚未委托任何资源，则他们会在 Azure 门户[“服务提供商”](view-manage-service-providers.md)页中“提供商产品/服务”部分的顶部看到备注。

> [!IMPORTANT]
> 委托必须由客户租户中的非来宾帐户完成，该帐户对于正在加入的订阅（或包含正在加入的资源组的订阅）拥有具备 `Microsoft.Authorization/roleAssignments/write` 权限的角色，如 [所有者](../../role-based-access-control/built-in-roles.md#owner)。 若要查看所有可以委托订阅的用户，客户租户中的用户可在 Azure 门户中选择订阅，打开“访问控制（标识和访问管理）”，然后[查看具有‘所有者’角色的所有用户](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

客户委托订阅（或订阅中的一个或多个资源组）后，将为该订阅注册“Microsoft.ManagedServices”资源提供程序，此后，租户中的用户可根据产品/服务中的授权访问委托的资源。

> [!NOTE]
> 若要以后将其他订阅或资源组委托给相同的产品/服务，客户需要在委托之前在每个订阅上 [手动注册 **Microsoft.ManagedServices** 资源提供程序](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。

如果你发布了产品/服务的更新版本，客户可以在 [Azure 门户中查看更改并接受新版本](view-manage-service-providers.md#update-service-provider-offers)。

## <a name="next-steps"></a>后续步骤

- 关于[商业市场](../../marketplace/overview.md)。
- [链接合作伙伴 ID](partner-earned-credit.md) 以跟踪你在客户互动中的影响。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。