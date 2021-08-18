---
title: 将现有 Azure 订阅添加到租户 - Azure AD
description: 有关将现有 Azure 订阅添加到 Azure Active Directory (Azure AD) 租户的说明。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc428a79e38f390d926f268bd8147eb7ee06a648
ms.sourcegitcommit: 04d2dec0316576388e6e10c1558b0a0b3688d01c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112421813"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>将 Azure 订阅关联或添加到 Azure Active Directory 租户

Azure 订阅与 Azure Active Directory (Azure AD) 之间存在信任关系。 订阅信任 Azure AD 对用户、服务和设备执行身份验证。

多个订阅可以信任同一个 Azure AD 目录。 每个订阅只能信任一个目录。

一个或多个 Azure 订阅可以与 Azure Active Directory (Azure AD) 实例建立信任关系，以便针对 Azure 服务对安全主体和设备进行身份验证和授权。  订阅过期时，受信任的 Azure AD 服务实例会保留，但安全主体将失去对 Azure 资源的访问权限。

当用户注册 Microsoft 云服务时，系统会创建一个新的 Azure AD 租户，并使该用户成为全局管理员角色的成员。 但是，当订阅的所有者将其订阅加入现有租户时，系统不会将该所有者分配到全局管理员角色。

所有用户都有一个用于身份验证的“主”目录。 用户还可以充当其他目录中的来宾。 可在 Azure AD 中查看每位用户的主目录和来宾目录。

:::image type="content" source="media/active-directory-how-subscriptions-associated-directory/trust-relationship-azure-ad.png" alt-text="显示 Azure 订阅与 Azure Active Directory 之间的信任关系的屏幕截图。":::

> [!Important]
> 将订阅与其他目录关联时，如果用户的角色是使用 [Azure 基于角色的访问控制](../../role-based-access-control/role-assignments-portal.md)分配的，则用户将失去其访问权限。 经典订阅管理员（包括服务管理员和共同管理员）也会失去访问权限。
>
> 如果将 Azure Kubernetes 服务 (AKS) 群集移到其他订阅，或者将拥有该群集的订阅移到新租户，该群集将会由于失去角色分配和服务主体权限而丢失功能。 有关 AKS 的详细信息，请参阅 [Azure Kubernetes 服务 (AKS)](../../aks/index.yml)。

## <a name="before-you-begin"></a>准备阶段

在关联或添加订阅之前，请执行以下任务：

- 查看下述在关联或添加订阅后会发生的更改的列表，以及你可能受到的具体影响：

  - 已使用 Azure RBAC 分配了角色的用户将失去其访问权限。
  - 服务管理员和共同管理员将失去其访问权限。
  - 如果你有任何密钥保管库，这些密钥保管库将无法访问，而且你必须在关联后对其进行修复。
  - 如果对资源（例如虚拟机或逻辑应用）使用任何托管标识，则必须在关联后重新启用或重新创建这些标识。
  - 如果拥有已注册的 Azure Stack，则将必须在关联后重新注册它。
  - 有关详细信息，请参阅[将 Azure 订阅转移到其他 Azure AD 目录](../../role-based-access-control/transfer-subscription.md)。

- 使用符合以下条件的帐户登录：

  - 具有该订阅的[所有者](../../role-based-access-control/built-in-roles.md#owner)角色分配。 有关如何分配所有者角色的信息，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
  - 在当前目录和新目录中存在。 当前目录已与订阅相关联。 要将新目录与订阅相关联。 若要详细了解如何获取其他目录的访问权限，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../external-identities/add-users-administrator.md)。

- 请确保未使用 Azure 云服务提供商 (CSP) 订阅（MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P）、Microsoft 内部订阅 (MS-AZR-0015P) 或面向学生的 Microsoft Azure 入门版订阅 (MS-AZR-0144P)。

## <a name="associate-a-subscription-to-a-directory"></a>将订阅关联到目录<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

若要将现有订阅关联到 Azure AD 目录，请执行以下步骤：

1. 登录，然后从 [Azure 门户中的“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择要使用的订阅。

1. 选择“更改目录”。

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="屏幕截图显示包含突出显示“更改目录”选项的“订阅”页。":::

1. 查看出现的任何警告，然后选择“更改”。

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="屏幕截图显示包含示例目录的“更改”目录页和突出显示的“更改”按钮。":::

   订阅目录更改后，会显示一条成功消息。

1. 选择订阅页上的“切换目录”，转到新目录。

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="屏幕截图显示包含示例信息的“目录切换器”页。":::

   正确显示所有内容可能需要数小时。 如果时间看起来太长，请查看“全局订阅筛选器”。 确保未隐藏移动的订阅。 可能需要注销 Azure 门户并重新登录才能查看新目录。

更改订阅目录是服务级操作，不会影响订阅的账单所有权。 若要删除原始目录，必须将订阅的账单所有权转让给新的帐户管理员。若要详细了解如何转让账单所有权，请参阅[将 Azure 订阅所有权转让给其他帐户](../../cost-management-billing/manage/billing-subscription-transfer.md)。

## <a name="post-association-steps"></a>关联后的步骤

将订阅关联到不同的目录后，可能需要执行以下任务来恢复操作：

- 如果有任何密钥保管库，则必须更改该密钥保管库租户 ID。 有关详细信息，请参阅[在订阅移动后更改密钥保管库租户 ID](../../key-vault/general/move-subscription.md)。

- 如果对资源使用了系统分配的托管标识，则必须重新启用这些标识。 如果使用了用户分配的托管标识，则必须重新创建这些标识。 重新启用或重新创建托管标识后，必须重新建立分配给这些标识的权限。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../managed-identities-azure-resources/overview.md)。

- 如果已使用此订阅注册了 Azure Stack，则必须重新注册。 有关详细信息，请参阅[将 Azure Stack Hub 注册到 Azure](/azure-stack/operator/azure-stack-registration)。

- 有关详细信息，请参阅[将 Azure 订阅转移到其他 Azure AD 目录](../../role-based-access-control/transfer-subscription.md)。

## <a name="next-steps"></a>后续步骤

- 若要创建新的 Azure AD 租户，请参阅[快速入门：在 Azure Active Directory 中创建新租户](active-directory-access-create-new-tenant.md)。

- 若要详细了解 Microsoft Azure 如何控制资源访问，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

- 若要详细了解如何在 Azure AD 中分配角色，请参阅[为使用 Azure Active Directory 的用户分配管理员和非管理员角色](active-directory-users-assign-role-azure-portal.md)。
