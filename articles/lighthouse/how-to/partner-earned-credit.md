---
title: 链接合作伙伴 ID 以跟踪对委派资源的影响
description: 关联合作伙伴 ID，以获得在通过 Azure Lighthouse 管理的客户资源上合作伙伴赚取的额度 (PEC)。
ms.date: 07/02/2021
ms.topic: how-to
ms.openlocfilehash: 72a31da9cf9927de00f005ce374488c0de9eba47
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113288718"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>链接合作伙伴 ID 以跟踪对委派资源的影响 

如果你是 [Microsoft 合作伙伴网络](https://partner.microsoft.com/)的成员，则可以将你的合作伙伴 ID 链接到管理委派的客户资源所用的凭据，从而允许 Microsoft 发现和识别能驱动 Azure 客户成功的合作伙伴。 [CSP（云解决方案提供商）](/partner-center/csp-overview)合作伙伴还可以使用此链接为[已签署 Microsoft 客户协议 (MCA)](/partner-center/confirm-customer-agreement) 且[采用 Azure 计划](/partner-center/azure-plan-get-started)的客户接收[合作伙伴获得的托管服务额度 (PEC)](/partner-center/partner-earned-credit)。

若要获得 Azure Lighthouse 活动的认可，需要与管理租户中的至少一个用户帐户[链接 MPN ID](../../cost-management-billing/manage/link-partner-id.md)，并确保已链接的帐户有权访问你所载入的每一个订阅。

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>在加入新客户时关联合作伙伴 ID

按照以下过程链接合作伙伴 ID（如果适用，启用合作伙伴赚取的额度）。 你需要知道自己的 [MPN 合作伙伴 ID](/partner-center/partner-center-account-setup#locate-your-mpn-id) 才能完成这些步骤。 请务必使用合作伙伴配置文件中显示的“关联 MPN ID”。

为简单起见，建议在租户中创建一个服务主体帐户，将其链接到 **已关联的 MPN ID**，然后向其授予一定的访问权限，允许其访问你加入的每一位拥有 [符合 PEC 条件的 Azure 内置角色](/partner-center/azure-roles-perms-pec)的客户。

1. 在管理租户中[创建服务主体用户帐户](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。 在此示例中，我们将使用此服务主体帐户的名称“Provider Automation Account”。
1. 使用该服务主体帐户，在管理租户中[链接到已关联的 MPN ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id)。 此操作只需执行一次。
1. 当[使用 ARM 模板](onboard-customer.md)或[托管服务产品](publish-managed-services-offers.md)加入客户时，请务必至少添加一个授权，其中包括 Provider Automation Account 作为具有[符合 PEC 条件的 Azure 内置角色](/partner-center/azure-roles-perms-pec)的用户。 此角色必须作为永久分配授予，而不是作为即时[合格授权](create-eligible-authorizations.md)，才能应用 PEC。

按照这些步骤操作，你管理的每个客户租户都将与你的合作伙伴 ID 相关联。 Provider Automation Account 无需在客户租户中进行身份验证或执行任何操作。

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="显示合作伙伴 ID 与 Azure Lighthouse 链接过程的图示。":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>将合作伙伴 ID 添加到之前载入的客户

如果之前已载入客户，可能不想执行其他部署来添加 Provider Automation Account 服务主体。 那么可以改为将 **已关联的 MPN ID** 链接到已有权在该客户的租户中运行的某个用户帐户。 确保该帐户已被授予[符合 PEC 条件的 Azure 内置角色](/partner-center/azure-roles-perms-pec)作为永久角色分配。

在管理租户中将帐户[链接到已关联的 MPN ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) 后，将能够跟踪识别对该客户的影响。

## <a name="confirm-partner-earned-credit"></a>确认合作伙伴赚取的额度

可以[在 Azure 门户中查看 PEC 详细信息](/partner-center/partner-earned-credit-explanation#azure-cost-management)，并确认哪些成本已获得 PEC 权益。 请记住，PEC 仅适用于已签署 MCA 并在 Azure 计划范围内的 CSP 客户。

如果已按照上述步骤操作，但未得到预期的关联，请在 Azure 门户中打开支持请求。

还可以使用[合作伙伴中心 SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) 并按 `rateOfPartnerEarnedCredit` 筛选，以便自动对订阅进行 PEC 验证。

## <a name="next-steps"></a>后续步骤

- 了解有关 [Microsoft 合作伙伴网络](/partner-center/mpn-overview)的详细信息。
- 了解 [PEC 如何计算和计费](/partner-center/partner-earned-credit-explanation)。
- [客户加入](onboard-customer.md) Azure Lighthouse。