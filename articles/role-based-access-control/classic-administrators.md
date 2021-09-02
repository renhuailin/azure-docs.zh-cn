---
title: Azure 经典订阅管理员
description: 介绍如何添加或更改 Azure 共同管理员和服务管理员角色，以及如何查看帐户管理员。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8a9ef41dcb85ddd8478078a927759190a6475840
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652091"
---
# <a name="azure-classic-subscription-administrators"></a>Azure 经典订阅管理员

Microsoft 建议使用 Azure 基于角色的访问控制 (Azure RBAC) 来管理对 Azure 资源的访问。 但是，如果你仍在使用经典部署模型，则需要使用经典订阅管理员角色：服务管理员和共同管理员。 有关详细信息，请参阅 [Azure 资源管理器与经典部署](../azure-resource-manager/management/deployment-models.md)。

本文介绍如何添加或更改共同管理员和服务管理员角色，以及如何查看帐户管理员。

## <a name="add-a-co-administrator"></a>添加共同管理员

> [!TIP]
> 如果用户需要使用 [Azure 服务管理 PowerShell 模块](/powershell/module/servicemanagement/azure.service)管理 Azure 经典部署，则你只需添加共同管理员。 如果用户只使用 Azure 门户管理经典资源，则无需为该用户添加经典管理员。

1. 以“服务管理员”或“共同管理员”角色登录到 [Azure 门户](https://portal.azure.com)。

1. 打开[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择一个订阅。

    只能在订阅范围分配共同管理员。

1. 单击“访问控制(IAM)”。

1. 单击“经典管理员”选项卡。

    ![打开经典管理员的屏幕截图](./media/shared/classic-administrators.png)

1. 单击“添加” > “添加共同管理员”打开“添加共同管理员”窗格。 

    如果“添加共同管理员”选项已禁用，则表示你没有相应的权限。

1. 选择要添加的用户，然后单击“添加”。

    ![添加协同管理员的屏幕截图](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>将来宾用户添加为共同管理员

要将来宾用户添加为共同管理员，请按照上一部分[添加共同管理员](#add-a-co-administrator)中的步骤操作。 来宾用户必须满足以下条件：

- 来宾用户必须存在于你的目录中。 这意味着你邀请了用户加入目录，并且该用户接受了邀请。

要详细了解如何将来宾用户添加到目录，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/external-identities/add-users-administrator.md)。

### <a name="differences-for-guest-users"></a>来宾用户的差异

与具有“共同管理员”角色的成员用户相比，已分配“共同管理员”角色的来宾用户可能会看到一些差异。 假设出现了下面这种情景：

- 用户 A 拥有一个 Azure AD 帐户（工作或学校帐户），该用户是 Azure 订阅的服务管理员。
- 用户 B 是 Microsoft 帐户。
- 用户 A 向用户 B 分配共同管理员角色。
- 用户 B 几乎可以执行所有操作，但无法注册应用程序或查找 Azure AD 目录中的用户。

你希望用户 B 可以管理所有内容。 此区别的原因在于，Microsoft 帐户作为来宾用户（而不是成员用户）添加到订阅。 与成员用户相比，来宾用户在 Azure AD 中具有不同的默认权限。 例如，成员用户可以在 Azure AD 中读取其他用户，而来宾用户不能。 成员用户可以在 Azure AD 中注册新的服务主体，而来宾用户不能。

如果来宾用户需要能够执行这些任务，则可能的解决方案是分配来宾用户所需的特定 Azure AD 角色。 例如，在上一方案中，你可以分配[目录读取者](../active-directory/roles/permissions-reference.md#directory-readers)角色以便读取其他用户，并分配[应用程序开发人员](../active-directory/roles/permissions-reference.md#application-developer)角色以便能够创建服务主体。 有关成员和来宾用户及其权限的详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)。 有关为来宾用户授予访问权限的详细信息，请参阅[使用 Azure 门户向外部来宾用户分配 Azure 角色](role-assignments-external-users.md)。

请注意，[Azure 内置角色](../role-based-access-control/built-in-roles.md)与 [Azure AD 角色](../active-directory/roles/permissions-reference.md)不同。 内置角色不授予对 Azure AD 的任何访问权限。 有关详细信息，请参阅[了解不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)。

有关比较成员用户和来宾用户的信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)。

## <a name="remove-a-co-administrator"></a>删除共同管理员

1. 以“服务管理员”或“共同管理员”角色登录到 [Azure 门户](https://portal.azure.com)。

1. 打开[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择一个订阅。

1. 单击“访问控制(IAM)”。

1. 单击“经典管理员”选项卡。

1. 在要删除的共同管理员旁边添加复选标记。

1. 单击“删除”。

1. 在出现的消息框中单击“是”。

    ![删除协同管理员的屏幕截图](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>更改服务管理员

只有帐户管理员可以更改订阅的服务管理员。 默认情况下，当你注册 Azure 订阅时，服务管理员即是帐户管理员。

具有帐户管理员角色的用户可以访问Azure 门户和管理计费，但不能取消订阅。 具有服务管理员角色的用户拥有 Azure 门户的完全访问权限，可以取消订阅。 帐户管理员可以使自己成为服务管理员。

请按照这些步骤在 Azure 门户中更改服务管理员。

1. 请查看[服务管理员变更限制](#limitations-for-changing-the-service-administrator)，确保自己的方案受支持。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。

1. 打开“成本管理 + 计费”，并选择一个订阅。

1. 在左侧导航栏中，单击“属性”。

1. 单击“更改服务管理员”。

    ![此屏幕截图显示了 Azure 门户中的订阅属性](./media/classic-administrators/service-admin.png)

1. 在“编辑服务管理员”页中，输入新服务管理员的电子邮件地址。

    ![显示“编辑服务管理员”页的屏幕截图](./media/classic-administrators/service-admin-edit.png)

1. 单击 **“确定”** 保存更改。

### <a name="limitations-for-changing-the-service-administrator"></a>服务管理员变更限制

每个 Azure 订阅只能有一个服务管理员。 根据帐户管理员是 Microsoft 帐户还是 Azure AD 帐户（工作或学校帐户），更改服务管理员的行为将有所不同。

| 帐户管理员帐户 | 是否可以将服务管理员更改为其他 Microsoft 帐户？ | 是否可以将服务管理员更改为同一目录中的 Azure AD 帐户？ | 是否可以将服务管理员更改为不同目录中的 Azure AD 帐户？ |
| --- | --- | --- | --- |
| Microsoft 帐户 | 是 | 否 | 否 |
| Azure AD 帐户 | 是 | 是 | 否 |

如果帐户管理员是 Azure AD 帐户，可以将服务管理员更改为同一目录中的 Azure AD 帐户，但不能更改为不同目录中的 Azure AD 帐户。 例如，abby@contoso.com 可以将服务管理员更改为 bob@contoso.com，但不能将服务管理员更改为 john@notcontoso.com，除非 john@notcontoso.com 在 contoso.com 目录中存在。

有关 Microsoft 帐户和 Azure AD 帐户的详细信息，请参阅[什么是 Azure Active Directory？](../active-directory/fundamentals/active-directory-whatis.md)。

## <a name="remove-the-service-administrator"></a>删除服务管理员

你可能想要删除服务管理员，例如，当他们不再与公司有关时。 如果删除了服务管理员，则必须在订阅范围内将一个用户分配为[“所有者”](built-in-roles.md#owner)角色，以避免孤立该订阅。 订阅所有者与服务管理员具有相同的访问权限。

1. 以订阅“所有者”或“共同管理员”角色登录到 [Azure 门户](https://portal.azure.com)。

1. 打开[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择一个订阅。

1. 单击“访问控制(IAM)”。

1. 单击“经典管理员”选项卡。

1. 在服务管理员旁边添加复选标记。

1. 单击“删除”。

1. 在出现的消息框中单击“是”。

    ![删除服务管理员的屏幕截图。](./media/classic-administrators/service-admin-remove.png)

## <a name="view-the-account-administrator"></a>查看帐户管理员

帐户管理员是最初注册 Azure 订阅的用户，充当订阅的账单所有者。 若要更改订阅的账户管理员，请参阅[将 Azure 订阅所有权转让给其他帐户](../cost-management-billing/manage/billing-subscription-transfer.md)。

遵循以下步骤查看帐户管理员：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 打开“成本管理 + 计费”，并选择一个订阅。

1. 在左侧导航栏中，单击“属性”。

    订阅的帐户管理员会显示在“帐户管理员”框中。

    ![显示帐户管理员的屏幕截图](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>后续步骤

* [了解不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)
* [添加或更改 Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)