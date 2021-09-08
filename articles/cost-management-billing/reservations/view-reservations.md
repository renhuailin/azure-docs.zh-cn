---
title: 查看和管理 Azure 预留项的权限
description: 了解如何在 Azure 门户中查看和管理 Azure 预留项。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 08/11/2021
ms.author: banders
ms.openlocfilehash: 0c278256c952fce12833a8f4c5fe55e18c3b40b0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099892"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>查看和管理 Azure 预留项的权限

本文介绍预留权限的工作原理，以及用户如何在 Azure 门户和 Azure PowerShell 中查看和管理 Azure 预留。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="who-can-manage-a-reservation-by-default"></a>默认情况下谁可以管理预留

默认情况下，以下用户可以查看和管理预留：

- 预留订单中将添加购买预留的人员和用于购买预留的计费订阅的帐户管理员。
- 企业协议和 Microsoft 客户协议计费管理员。
- 拥有可管理所有 Azure 订阅和管理组的提升访问权限的用户
- Azure Active Directory (Azure AD) 租户（目录）中预留的预留管理员
- 预留读者对 Azure Active Directory (Azure AD) 租户（目录）中的预留拥有只读访问权限

预留项的生命周期独立于 Azure 订阅，因此预留项不是 Azure 订阅下的资源。 相反，它是一个租户级资源，具有自己的 Azure RBAC 权限，与订阅无关。 在购买后，预留项不会从订阅继承权限。

## <a name="view-and-manage-reservations"></a>查看和管理预留

如果你是计费管理员，请在 Azure 门户中使用以下步骤来查看和管理所有预留和预留交易。

1. 登录到 [Azure 门户](https://portal.azure.com)，导航到“成本管理 + 计费”。
    - 如果你是 EA 管理员，请在左侧菜单中选择“计费范围”，然后在计费范围列表中选择一个范围。
    - 如果你是 Microsoft 客户协议计费配置文件所有者，请在左侧菜单中选择“计费配置文件”。 在计费配置文件列表中选择一个计费配置文件。
1. 在左侧菜单中，选择“产品 + 服务” > “预留项” 。
1. 此时将显示 EA 注册或计费配置文件的完整预留项列表。
1. 计费管理员可以通过选择预留项，然后在出现的窗口中选择“授予访问权限”，来取得预留项的所有权。

### <a name="add-billing-administrators"></a>添加计费管理员

在 Azure 门户中，将用户作为计费管理员添加到企业协议或 Microsoft 客户协议。

- 对于企业协议，请添加具有“企业管理员”角色的用户，以便查看和管理适用于企业协议的所有预留订单。 企业管理员可以在“成本管理 + 计费”中查看和管理预留项。
    - 具有“企业管理员（只读）”角色的用户只能从“成本管理 + 计费”中查看预留项。 
    - 除非使用访问控制 (IAM) 将部门管理员和帐户所有者显式添加到预留中，否则他们无法查看相关预留。 有关详细信息，请参阅[管理 Azure 企业角色](../manage/understand-ea-roles.md)。
- 在 Microsoft 客户协议下，具有计费配置文件所有者角色或计费配置文件参与者角色的用户可以管理使用计费配置文件完成的所有预留购买。 计费配置文件阅读者和发票管理员可以通过计费配置文件查看付费的所有预留。 但是，他们不能对预留进行更改。
    有关详细信息，请参阅[计费对象信息角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="view-reservations-with-azure-rbac-access"></a>使用 Azure RBAC 访问权限查看预留项

如果你购买了预留或已添加到预留，请在 Azure 门户中使用以下步骤来查看和管理预留。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务” > “预留”以列出自己有权访问的预留 。

## <a name="manage-subscriptions-and-management-groups-with-elevated-access"></a>通过提升的访问权限管理订阅和管理组

你可以提升用户的[访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)。

拥有提升的访问权限后：

1. 导航到“所有服务” > “预留项”以查看租户中的所有预留项 。
1. 若要对预留项进行修改，请使用访问控制 (IAM) 将自己添加为预留订单的所有者。

## <a name="grant-access-to-individual-reservations"></a>授予对单个预留的访问权限

对预留拥有所有者访问权限的用户和计费管理员可以在 Azure 门户中委派单个预留订单的访问管理。

若要允许其他人管理预留，可通过两种方式实现：

- 通过向单个预留订单的资源范围内的用户分配“所有者”角色，委托该预留订单的访问权限管理。 如果希望提供有限的访问权限，请选择其他角色。  
     有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

- 将用户作为计费管理员添加到企业协议或 Microsoft 客户协议：
    - 对于企业协议，请添加具有“企业管理员”角色的用户，以便查看和管理适用于企业协议的所有预留订单。 具有“企业管理员（只读）”角色的用户可以查看预留。 除非使用访问控制 (IAM) 将部门管理员和帐户所有者显式添加到预留中，否则他们无法查看相关预留。 有关详细信息，请参阅[管理 Azure 企业角色](../manage/understand-ea-roles.md)。

        企业管理员可以获得预留订单的所有权，并可以使用访问控制 (IAM) 将其他用户添加到预留。
    - 在 Microsoft 客户协议下，具有计费配置文件所有者角色或计费配置文件参与者角色的用户可以管理使用计费配置文件完成的所有预留购买。 计费配置文件阅读者和发票管理员可以通过计费配置文件查看付费的所有预留。 但是，他们不能对预留进行更改。
    有关详细信息，请参阅[计费对象信息角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="grant-access-with-powershell"></a>使用 PowerShell 授予访问权限

拥有预留订单的所有者访问权限的用户、拥有提升的访问权限的用户以及[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)可以委派他们有权访问的所有预留订单的访问管理。

## <a name="assign-the-owner-role-for-all-reservations"></a>分配所有预留的所有者角色

使用以下 Azure PowerShell 脚本，向用户授予 Azure AD 租户（目录）中所有预留订单的 Azure RBAC 访问权限。

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources
 
Connect-AzAccount -Tenant <TenantId>
 
$response = Invoke-AzRestMethod -Path /providers/Microsoft.Capacity/reservations?api-version=2020-06-01 -Method GET
 
$responseJSON = $response.Content | ConvertFrom-JSON
 
$reservationObjects = $responseJSON.value
 
foreach ($reservation in $reservationObjects)
{
  $reservationOrderId = $reservation.id.substring(0, 84)
  Write-Host "Assiging Owner role assignment to "$reservationOrderId
  New-AzRoleAssignment -Scope $reservationOrderId -ObjectId <ObjectId> -RoleDefinitionName Owner
}
```

### <a name="parameters"></a>参数

**-ObjectId**  用户、组或服务主体的 Azure AD ObjectId。
- 键入：String
- 别名：Id、PrincipalId
- 位置：已命名
- 默认值： 无
- 接受管道输入：True
- 接受通配符：False

**-TenantId** 租户唯一标识符。
- 键入：String
- 位置：5
- 默认值： 无
- 接受管道输入：False
- 接受通配符：False

[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)可以将用户添加到预留管理员和预留读者角色。

## <a name="add-a-reservation-administrator-role-at-the-tenant-level"></a>添加租户级别的预留管理员角色

使用以下 Azure PowerShell 脚本通过 PowerShell 添加租户级别的预留管理员角色。

```azurepowershell
Import-Module Az.Accounts
Import-Module Az.Resources
Connect-AzAccount -Tenant <TenantId>
New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Administrator"
```

### <a name="parameters"></a>参数

**-ObjectId** 用户、组或服务主体的 Azure AD ObjectId。
- 键入：String
- 别名：Id、PrincipalId
- 位置：已命名
- 默认值： 无
- 接受管道输入：True
- 接受通配符：False

**-TenantId** 租户唯一标识符。
- 键入：String
- 位置：5
- 默认值： 无
- 接受管道输入：False
- 接受通配符：False

## <a name="assign-a-reservation-reader-role-at-the-tenant-level"></a>分配租户级别的预留读者角色

使用以下 Azure PowerShell 脚本通过 PowerShell 分配租户级别的预留读者角色。

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources

Connect-AzAccount -Tenant <TenantId>

New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Reader"
```

### <a name="parameters"></a>参数

**-ObjectId** 用户、组或服务主体的 Azure AD ObjectId。
- 键入：String
- 别名：Id、PrincipalId
- 位置：已命名
- 默认值： 无
- 接受管道输入：True
- 接受通配符：False

**-TenantId** 租户唯一标识符。
- 键入：String
- 位置：5
- 默认值： 无
- 接受管道输入：False
- 接受通配符：False


## <a name="next-steps"></a>后续步骤

- [管理 Azure 预留项](manage-reserved-vm-instance.md)。
