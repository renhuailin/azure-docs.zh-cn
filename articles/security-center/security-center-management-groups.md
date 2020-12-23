---
title: 在 Azure 安全中心内实现租户级公开范围 | Microsoft Docs
description: 本文介绍如何通过将策略应用于关联到 Azure Active Directory 租户的所有订阅来大规模管理安全状况。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2020
ms.author: memildin
ms.openlocfilehash: d03177e3224bbd3f53320871efc6a0d6b3ea479d
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922754"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>组织管理组、订阅和租户范围的可见性

本文介绍如何通过将安全策略应用到链接到 Azure Active Directory 租户的所有 Azure 订阅来大规模管理组织的安全状况。

若要了解在 Azure AD 租户中注册的所有订阅的安全状态，必须对根管理组分配拥有足够读取权限的 Azure 角色。


## <a name="organize-your-subscriptions-into-management-groups"></a>将订阅组织到管理组

### <a name="introduction-to-management-groups"></a>管理组简介

借助 Azure 管理组，可以对各组订阅高效管理访问、策略和报告，并能对根管理组执行操作，从而有效管理整个 Azure 资产。 可以将订阅整理到管理组中，并向管理组应用治理策略。 管理组中的所有订阅都会自动继承应用于管理组的策略。 

为每个 Azure AD 租户提供了一个名为 **根管理组** 的顶级管理组。 此根管理组内置在层次结构中，包含其所有下级管理组和订阅。 借助此组，可以在目录级别应用全局策略和 Azure 角色分配。 

执行以下任何操作时，都会自动创建根管理组： 
- 在 [Azure 门户](https://portal.azure.com)中打开 **管理组**。
- 使用 API 调用创建管理组。
- 通过 PowerShell 创建管理组。 有关 PowerShell 说明，请参阅为 [资源和组织管理创建管理组](../governance/management-groups/create-management-group-portal.md)。

管理组无需加入安全中心，但建议你至少创建一个管理组，以便创建根管理组。 创建管理组后，Azure AD 租户下的所有订阅都会与它关联。 


有关管理组的详细概述，请参阅[使用 Azure 管理组整理资源](../governance/management-groups/overview.md)一文。

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>在 Azure 门户中查看和创建管理组

1. 在 [Azure 门户](https://portal.azure.com)中，使用顶部栏中的 "搜索" 框来查找和打开 **管理组**。

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="访问管理组":::

    此时将显示管理组的列表。

1. 若要创建管理组，请选择 " **添加管理组**"，输入相关详细信息，然后选择 " **保存**"。

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="向 Azure 添加管理组":::

    - “管理组 ID”是用来在此管理组上提交命令的目录唯一标识符。 此标识符一旦创建便无法再编辑，因为它用来在整个 Azure 系统中标识这个组。 
    - 显示名称字段是在 Azure 门户中显示的名称。 创建管理组时，单独的显示名称是一个可选字段，并且可以随时更改。  


### <a name="add-subscriptions-to-a-management-group"></a>向管理组添加订阅
可以向创建的管理组添加订阅。

1. 在 " **管理组**" 下，选择你的订阅的管理组。

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="为你的订阅选择管理组":::

1. 当组的页打开时，选择 "**详细信息**"

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="打开管理组的详细信息页":::

1. 从组的详细信息页中，选择 " **添加订阅**"，然后选择订阅并选择 " **保存**"。 重复此步骤，直到已在范围中添加所有订阅。

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="向管理组添加订阅":::
   > [!IMPORTANT]
   > 管理组可以包含订阅和子管理组。 向父管理组分配拥有 Azure 角色的用户时，子管理组的订阅继承访问权限。 子管理组还继承在父管理组设置的策略。 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>向自己授予租户范围的权限

Azure Active Directory 具有 **全局管理员** 角色的用户可能拥有租户范围内的责任，但缺乏 azure 权限，无法在 Azure 安全中心查看组织范围内的信息。 

> [!TIP]
> 如果你的组织使用 [Azure AD Privileged Identity Management (PIM) ](../active-directory/privileged-identity-management/pim-configure.md)或任何其他 PIM 工具管理资源访问权限，则全局管理员角色必须是活动的，用户才能进行这些更改。

为自己分配租户级别权限：

1. 作为在租户的根管理组上没有分配的全局管理员用户，打开安全中心的 " **概述** " 页，并在横幅中选择 " **租户范围的可见性** " 链接。 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="在 Azure 安全中心启用租户级权限":::

1. 选择要分配的新 Azure 角色。 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="定义要分配给用户的租户级权限的窗体":::

    > [!TIP]
    > 通常情况下，若要在根级别应用策略，需要安全管理员角色，若要提供租户级可见性，安全读取者角色就足够了。 有关这些角色授予的权限的详细信息，请参阅[安全管理员内置角色说明](../role-based-access-control/built-in-roles.md#security-admin)或[安全读取者内置角色说明](../role-based-access-control/built-in-roles.md#security-reader)。
    >
    > 有关特定于安全中心的这些角色之间的差异，请参阅 [角色和允许的操作](security-center-permissions.md#roles-and-allowed-actions)中的表。

    组织范围的视图是通过在租户的根管理组级别授予角色实现的。  

1. 注销 Azure 门户，然后再次登录。

1. 获得提升的访问权限后，立即打开或刷新 Azure 安全中心，验证能否查看 Azure AD 租户下的所有订阅。 

## <a name="assign-azure-roles-to-other-users"></a>将 Azure 角色分配给其他用户

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>通过 Azure 门户向用户分配 Azure 角色： 
1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 若要查看管理组，请在 Azure 主菜单下选择“所有服务”，然后选择“管理组”。
1.  选择管理组，并选择 " **详细信息**"。

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="管理组详细信息屏幕截图":::

1. 选择 **(IAM) 的访问控制** ，然后选择 **角色分配**。
1. 选择“添加角色分配”。
1. 选择要分配的角色和用户，然后选择 " **保存**"。  
   
   ![添加安全读取者角色屏幕截图](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>使用 PowerShell 向用户分配 Azure 角色： 
1. 安装 [Azure PowerShell](/powershell/azure/install-az-ps)。
2. 运行以下命令： 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. 当出现提示时，请使用全局管理员凭据登录。 

    ![登录提示屏幕截图](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. 运行下面的命令，授予读者角色权限：

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. 若要删除角色，请运行下面的命令： 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>撤消提升的访问权限 
向用户分配 Azure 角色后，租户管理员应将自己从用户访问管理员角色中删除。

1. 登录 [Azure 门户](https://portal.azure.com)或 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

2. 在导航列表中，选择 " **Azure Active Directory** "，然后选择 " **属性**"。

3. 在“Azure 资源的访问管理”下，将开关设置为“否” 。

4. 若要保存设置，请选择 " **保存**"。



## <a name="next-steps"></a>后续步骤
本文介绍了如何在 Azure 安全中心内实现租户级公开范围。 如需相关信息，请参阅：

- [Azure 安全中心中的权限](security-center-permissions.md)