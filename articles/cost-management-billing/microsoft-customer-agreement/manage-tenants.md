---
title: 管理 Microsoft 客户协议计费帐户中的租户 - Azure
description: 本文可帮助你了解和管理与你的 Microsoft 客户协议计费帐户关联的租户。
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: microsoft-customer-agreement
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: 9fe8fc7660bf63e13f8286900bbfab5d8399d2c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737019"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>管理 Microsoft 客户协议计费帐户中的租户

本文可帮助你了解和管理与你的 Microsoft 客户协议计费帐户关联的租户。 在确保对计费环境进行安全访问的同时，使用这些信息来管理租户、转移订阅和管理计费所有权。

## <a name="whats-a-tenant"></a>什么是租户？

租户是组织的数字表示形式，主要与域（如 Microsoft.com）相关联。 它是一个通过 Azure Active Directory 管理的环境，使你能够向用户分配用于管理 Azure 资源和计费的权限。

每个租户均不相同并与其他租户分离，但你可以允许其他租户的来宾用户访问你的租户以跟踪你的成本并管理计费。

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>租户和订阅如何与计费帐户相关

你使用自己的 Microsoft 客户协议（计费帐户）来跟踪成本和管理计费。 每个计费帐户都至少有一个计费对象信息项。 通过计费对象信息，可管理发票和付款方式。 各计费对象信息默认包含一个发票科目。 如果需要，可创建更多发票科目以跟踪和管理成本，并对成本进行分组。

- 你的计费帐户与单个租户关联。 这意味着只有属于该租户的用户才能访问你的计费帐户。
- 为计费帐户创建新的 Azure 订阅时，它始终在计费帐户租户中进行创建。 但是，可将订阅移到其他租户。 你还可将其他租户的现有订阅链接到你的计费帐户。 这样做，你可通过一个租户集中管理计费，同时根据需要将资源和订阅保留在其他租户中。

下图显示了如何将计费帐户和订阅链接到租户。 Contoso MCA 计费帐户与租户 1 关联，而 Contoso PAYG 帐户与租户 2 关联。 假设 Contoso 要通过其 MCA 计费帐户为其 PAYG 订阅付费，他们可使用计费所有权转移将该订阅链接到其 MCA 计费帐户。 订阅及其资源仍与租户 2 关联，但使用 MCA 计费帐户对它们付费。

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="显示示例计费层次结构的关系图。" border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>在单个 Microsoft 客户协议中管理多个租户下的订阅

当计费所有者有针对计费帐户的[适当权限](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks)时，他们可创建订阅。 默认情况下，Microsoft 客户协议下创建的任何新订阅都位于 Microsoft 客户协议租户中。

- 可将其他租户的订阅链接到你的 Microsoft 客户协议计费帐户。 获取订阅的计费所有权只会更改开票排列。 这不会影响服务租户或 Azure RBAC 角色。
- 若要更改服务租户中的订阅所有者，必须将[订阅转移到不同的 Azure Active Directory 目录](../../role-based-access-control/transfer-subscription.md)。

MCA 计费帐户由单个租户/目录管理。 计费帐户仅控制其租户中订阅的计费。 但是，你可以使用计费所有权转让将订阅链接到不同租户中的计费帐户。

### <a name="billing-ownership-transfer"></a>计费所有权转让

计费所有权转让只更改单个订阅的发票安排。 订阅的用户和资源管理不会更改。

计费所有权转让执行两件事：

- 删除订阅的原始计费所有权。
- 将订阅计费所有权链接到可能位于不同的租户/目录中的目标计费帐户。

计费所有权转让不会影响以下项：

- 用户
- 资源
- Azure RBAC 权限


## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>向 Microsoft 客户协议租户添加来宾用户

添加到你的 Microsoft 客户协议计费租户的用户必须被邀请为来宾，才能管理不同租户中的计费职责。

若要邀请某用户作为来宾，该用户必须有一个现有电子邮件地址，该地址的域与你的 Azure Active Directory (AD) 域不同。 Azure AD 向来宾用户发送一封电子邮件，其中包含用于身份验证的链接。

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="显示一个示例电子邮件邀请的屏幕截图。" lightbox="./media/manage-tenants/guest-invitation-email.png" :::

将用户添加到 Microsoft 客户协议租户后，他们必须[接受邀请](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)。

在他们选择“接受邀请”链接时，系统将提示他们在 Azure 上进行身份验证。

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="显示在 Azure 上进行身份验证的提示的屏幕截图。" lightbox="./media/manage-tenants/authenticate.png" :::

然后，他们选择“接受”。

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="显示接受邀请的提示的屏幕截图。" lightbox="./media/manage-tenants/accept-invitation.png" :::

接受后，他们可[在“成本管理 + 计费”下查看 Microsoft 客户协议计费帐户](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement)。

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="在计费帐户列表中显示 Microsoft 客户协议的屏幕截图。" lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

邀请来宾用户的授权由 Azure AD 设置控制。 “组织关系”页上的“设置”下显示了设置的值 。 确保该设置已选择，否则邀请会失败。有关详细信息，请参阅[限制来宾用户的访问权限](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)。

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="显示外部协作设置的屏幕截图。" lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> 来宾用户获取了对 Microsoft 客户协议租户的访问权限，这可能会带来安全问题。 有关详细信息，请参阅[了解如何限制来宾用户的默认权限](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)。

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>在 Azure AD 租户下管理多个 Microsoft 云服务

可在单个 Azure AD 租户下为组织管理多个云服务。 所有 Microsoft 云产品/服务的用户帐户都存储在 Azure AD 租户中，该租户包含用户帐户和组。 下图显示了具有多个服务（使用包含帐户的公共 Azure AD 租户）的组织的示例。 每个服务都有自己的门户（用蓝色文本表示），用户在其中管理自己的服务。

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="图中显示了一个具有多个服务（使用包含帐户的公共 Azure AD 租户）的组织的示例。" border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>后续步骤

阅读以下文章，了解如何管理灵活的计费所有权，并确保对你的 Microsoft 客户协议的安全访问。

- [如何设置租户](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Azure 内置角色](../../role-based-access-control/built-in-roles.md)
- [将 Azure 订阅转移到其他 Azure AD 目录](../../role-based-access-control/transfer-subscription.md)
- [在 Azure Active Directory 中限制来宾访问权限（预览版）](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [将来宾用户添加到 Azure 门户中的目录](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Azure Active Directory 中的默认用户权限是什么？](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)