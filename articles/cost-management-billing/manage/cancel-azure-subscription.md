---
title: 取消 Azure 订阅
description: 介绍如何取消 Azure 订阅，例如免费试用版订阅
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: banders
ms.openlocfilehash: 3182da0d04d61d5bad4ce3e41f213ecc3be7ecaf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444449"
---
# <a name="cancel-your-azure-subscription"></a>取消 Azure 订阅

如果不再需要 Azure 订阅，可以在 Azure 门户中将其取消。

Microsoft 建议你在取消订阅之前执行以下操作，虽然这不是必需的：

* 备份数据。 例如，若要在 Azure 存储或 SQL 中存储数据，请下载一个副本。 如果有虚拟机，请在本地保存该虚拟机的映像。
* 关闭服务。 转到 [管理门户中的资源页](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，**停止** 任何正在运行的虚拟机、应用程序或其他服务。
* 考虑迁移数据。 请参阅[将资源移到新资源组或订阅中](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。
* 删除所有资源和所有资源组。
* 如果在 `AssignableScopes` 中有任何引用此订阅的自定义角色，则应更新这些自定义角色以删除此订阅。 如果在取消订阅后尝试更新自定义角色，可能会收到错误。 有关详细信息，请参阅[排查自定义角色问题](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles)和 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)。

如果取消付费的 Azure 支持计划，则需为剩余的订阅期付费。 有关详细信息，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)。

## <a name="cancel-subscription-in-the-azure-portal"></a>在 Azure 门户中取消订阅

1. 从 [Azure 门户中的“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择订阅。
1. 选择要取消的订阅。
1. 选择“概述”，然后选择“取消订阅” 。
    ![显示“取消”按钮的屏幕截图](./media/cancel-azure-subscription/cancel_ibiza.png)
1. 按照提示进行操作并完成取消。

> [!NOTE]
> 如果客户提出请求或发生未付款或欺诈的情况，合作伙伴可以暂停或取消订阅。 有关详细信息，请参阅[暂停或取消订阅](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription)。

## <a name="who-can-cancel-a-subscription"></a>谁可以取消订阅？

下表说明了取消订阅所需的权限。

|订阅类型     |谁可以取消  |
|---------|---------|
|通过 Azure 网站注册 Azure 时创建的订阅。 例如，当你注册 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)、[采用即用即付费率的帐户](https://azure.microsoft.com/offers/ms-azr-0003p/)或作为 [Visual Studio 订阅者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)时。 |  订阅的帐户管理员和所有者  |
|[Microsoft 企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)和 [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  订阅的帐户所有者和所有者       |
|[Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/)和 [Azure 开发测试计划](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  订阅的所有者      |

## <a name="what-happens-after-subscription-cancellation"></a>取消订阅之后会发生什么情况？

取消后，会立即停止计费。 但是，可能需要花费多达 10 分钟，取消才会显示在门户中。 如果在计费周期中途取消订阅，我们会在周期结束后于标准发票日期发送最终账单。

取消后，你的服务将被禁用。 那意味着虚拟机已解除分配，临时 IP 地址已释放，并且存储是只读的。

取消订阅后，Microsoft 会先等待 30 - 90 天，然后才会永久删除用户的数据，这样做是考虑到用户可能需要访问这些数据，或者改变了主意。 我们不会因为保留这些数据而向你收费。 若要了解详细信息，请参阅 [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)（Microsoft 信任中心 - 我们如何管理用户的数据）。

## <a name="delete-free-trial-subscription"></a>删除免费试用版订阅

如果你有免费试用版订阅，则无需等待 30 天的订阅自动删除期。 你可以在取消订阅三天后将其删除。 “删除订阅”选项在你取消订阅三天后才可用。

1. 取消订阅后，请等待三天。
1. 在 Azure 门户中的[“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上选择你的订阅。
1. 选择要删除的订阅。
1. 选择“概述”，然后选择“删除订阅” 。

## <a name="reactivate-a-subscription"></a>重新激活订阅

如果意外取消了采用即用即付费率的订阅，可[在帐户中心重新激活订阅](subscription-disabled.md)。

如果你的订阅不是采用即用即付费率的订阅，可在取消后的 90 天内联系支持人员来重新激活订阅。

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>为什么在 Azure 门户上看不到“取消订阅”选项？ 

你可能没有取消订阅所需的权限。 有关谁可以取消各种类型的订阅的说明，请参阅[谁可以取消订阅？](#who-can-cancel-a-subscription)。

## <a name="how-do-i-delete-my-azure-account"></a>如何删除 Azure 帐户？

*我需要删除我的帐户，包括所有个人信息。我已经取消了有效的（免费试用版）订阅。我没有任何有效的订阅，并且想要完全删除我的帐户*。

* 如果是通过组织获取的 Azure Active Directory 帐户，则 Azure AD 管理员可以删除该帐户。 在那之后，服务被禁用。 那意味着虚拟机已解除分配，临时 IP 地址已释放，并且存储是只读的。 总之，取消后，会立即停止计费。

* 如果不是通过组织获取的 Azure AD 帐户，则可以取消后再删除 Azure 订阅，然后从该帐户中删除信用卡。 虽然该操作不会删除帐户，但会使其无法使用。 如果关联的 Microsoft 帐户当前未用于任何其他目的，则还可以进一步操作并将其删除。

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>如何取消 Visual Studio Professional 帐户？

请参阅[续订和取消](/visualstudio/subscriptions/faq/admin/renewal-cancellation)一文。 如果有任何 Visual Studio Azure 订阅，也需要取消和删除它们。

## <a name="next-steps"></a>后续步骤

- 如果需要，可以在[帐户中心](subscription-disabled.md)重新激活即用即付订阅。