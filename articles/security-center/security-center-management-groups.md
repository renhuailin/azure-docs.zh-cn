---
title: 将订阅组织到管理组中，并将角色分配给 Azure 安全中心的用户
description: 了解如何在 Azure 安全中心将 Azure 订阅组织到管理组中，并向组织中的用户分配角色
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 3508d508a19d6ce7fba4f3ef3a4fa545a58a167d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099380"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>将订阅组织到管理组中，并为用户分配角色

本文介绍如何通过将安全策略应用于关联到 Azure Active Directory 租户的所有 Azure 订阅来大规模管理组织的安全状况。

若要了解在 Azure AD 租户中注册的所有订阅的安全状态，必须对根管理组分配拥有足够读取权限的 Azure 角色。


## <a name="organize-your-subscriptions-into-management-groups"></a>将订阅组织到管理组中

### <a name="introduction-to-management-groups"></a>管理组简介

借助 Azure 管理组，可以对各组订阅高效管理访问、策略和报告，并能对根管理组执行操作，从而有效管理整个 Azure 资产。 可以将订阅整理到管理组中，并向管理组应用治理策略。 管理组中的所有订阅都会自动继承应用于管理组的策略。 

每个 Azure AD 租户都指定有一个顶级管理组，称为“根管理组”。 此根管理组内置在层次结构中，包含其所有下级管理组和订阅。 借助此组，可以在目录级别应用全局策略和 Azure 角色分配。 

执行以下任何操作时，都会自动创建根管理组： 
- 在 [Azure 门户](https://portal.azure.com)中打开“管理组”。
- 通过 API 调用来创建管理组。
- 通过 PowerShell 创建管理组。 有关 PowerShell 的说明，请参阅[创建管理组以管理资源和组织](../governance/management-groups/create-management-group-portal.md)。

虽然不一定要在安全中心内创建管理组，但建议至少创建一个，以便创建根管理组。 创建管理组后，Azure AD 租户下的所有订阅都会与它关联。 


有关管理组的详细概述，请参阅[使用 Azure 管理组整理资源](../governance/management-groups/overview.md)一文。

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>在 Azure 门户中查看和创建管理组

1. 从 [Azure 门户](https://portal.azure.com)中，使用顶部栏中的“搜索”框找到并打开“管理组”。

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="访问管理组":::

    此时将显示管理组的列表。

1. 若要创建管理组，请选择“添加管理组”，输入相关详细信息，然后选择“保存” 。

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="向 Azure 添加管理组":::

    - “管理组 ID”是用来在此管理组上提交命令的目录唯一标识符。 此标识符一旦创建便无法再编辑，因为它用来在整个 Azure 系统中标识这个组。 
    - 显示名称字段是在 Azure 门户中显示的名称。 创建管理组时，单独的显示名称是一个可选字段，并且可以随时更改。  


### <a name="add-subscriptions-to-a-management-group"></a>向管理组添加订阅
可以向创建的管理组添加订阅。

1. 在“管理组”下，为你的订阅选择管理组。

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="为你的订阅选择管理组":::

1. 当组的页面打开时，选择“详细信息”

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="打开管理组的“详细信息”页":::

1. 从组的“详细信息”页中，选择“添加订阅”，然后选择你的订阅，再选择“保存” 。 重复执行上述步骤，直到已添加范围内的所有订阅。

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="向管理组添加订阅":::
   > [!IMPORTANT]
   > 管理组可以包含订阅和子管理组。 向父管理组分配拥有 Azure 角色的用户时，子管理组的订阅继承访问权限。 子管理组还继承在父管理组设置的策略。 



## <a name="assign-azure-roles-to-other-users"></a>将 Azure 角色分配给其他用户

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>通过 Azure 门户向用户分配 Azure 角色： 
1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 若要查看管理组，请在 Azure 主菜单下选择“所有服务”，然后选择“管理组”。
1.  选择一个管理组，然后选择“详细信息”。

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="管理组详细信息屏幕截图":::

1. 选择“访问控制(IAM)”，然后选择“角色分配” 。
1. 选择“添加角色分配”。
1. 选择要分配的角色和用户，然后选择“保存”。  
   
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

2. 在导航列表中，选择“Azure Active Directory”，然后选择“属性” 。

3. 在“Azure 资源的访问管理”下，将开关设置为“否” 。

4. 选择“保存”以保存设置。



## <a name="next-steps"></a>后续步骤
本文介绍了如何将订阅组织到管理组中，以及如何将角色分配给用户。 如需相关信息，请参阅：

- [Azure 安全中心中的权限](security-center-permissions.md)