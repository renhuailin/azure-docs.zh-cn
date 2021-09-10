---
title: 使用 Azure PowerShell 分配 Azure 角色 - Azure RBAC
description: 了解如何使用 Azure PowerShell 和 Azure 基于角色的访问控制 (Azure RBAC) 为用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2021
ms.author: rolyon
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0138a36c976a9c7c273d61fdd19a50ffbe31354
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305923"
---
# <a name="assign-azure-roles-using-azure-powershell"></a>使用 Azure PowerShell 分配 Azure 角色

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 本文介绍如何使用 Azure PowerShell 分配角色。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

若要分配角色，必须具有：

- `Microsoft.Authorization/roleAssignments/write` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)
- [Azure Cloud Shell 中的 PowerShell](../cloud-shell/overview.md) 或 [Azure PowerShell](/powershell/azure/install-az-ps)
- 用于运行 PowerShell 命令的帐户必须具有 Azure Active Directory Graph `Directory.Read.All` 和 Microsoft Graph `Directory.Read.All` 权限。

## <a name="steps-to-assign-an-azure-role"></a>分配 Azure 角色的步骤

角色分配包含三个要素：安全主体、角色订阅和范围。

### <a name="step-1-determine-who-needs-access"></a>步骤 1：确定谁需要访问权限

可以将角色分配到用户、组、服务主体或托管标识。 若要分配角色，可能需要指定对象的唯一 ID。 ID 的格式为：`11111111-1111-1111-1111-111111111111`。 可以使用 Azure 门户或 Azure PowerShell 获取 ID。

**User**

对于 Azure AD 用户，请获取用户主体名称（例如 *patlong\@contoso.com*）或用户对象 ID。 若要获取该对象 ID，可以使用 [Get-AzADUser](/powershell/module/az.resources/get-azaduser)。

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**组**

对于 Azure AD 组，你需要组对象 ID。 若要获取该对象 ID，可以使用 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)。

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**服务主体**

对于 Azure AD 服务主体（应用程序使用的标识），你需要服务主体对象 ID。 若要获取该对象 ID，可以使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)。 对于服务主体，使用对象 ID，而不是应用程序 ID。

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**托管的标识**

对于系统分配的或用户分配的托管标识，你需要对象 ID。 若要获取该对象 ID，可以使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)。

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-select-the-appropriate-role"></a>步骤 2：选择合适的角色

权限组合成角色。 可以从包含多个 [Azure 内置角色](built-in-roles.md)的列表中选择，也可以使用自己的自定义角色。 最佳做法是以所需的最少权限授予访问权限，因此避免分配范围更广泛的角色。

若要列出角色并获取唯一的角色 ID，可以使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

下面介绍了如何列出特定角色的详细信息。

```azurepowershell
Get-AzRoleDefinition <roleName>
```

有关详细信息，请参阅[列出 Azure 角色定义](role-definitions-list.md#azure-powershell)。
 
### <a name="step-3-identify-the-needed-scope"></a>步骤 3：识别所需的范围

Azure 提供四个级别的范围：资源、[资源组](../azure-resource-manager/management/overview.md#resource-groups)、订阅，以及[管理组](../governance/management-groups/overview.md)。 最佳做法是以所需的最少权限授予访问权限，因此避免在更广泛的范围分配角色。 有关范围的详细信息，请参阅[了解范围](scope-overview.md)。

**资源范围**

对于资源范围，你需要资源的资源 ID。 可以通过在 Azure 门户中查看资源的属性来找到资源 ID。 资源 ID 采用以下格式。

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**资源组范围**

对于资源组范围，你需要资源组的名称。 可以在 Azure 门户的“资源组”页上找到此名称，也可以使用 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)。

```azurepowershell
Get-AzResourceGroup
```

**订阅范围** 

对于订阅范围，你需要订阅 ID。 可以在 Azure 门户的“订阅”页上找到此 ID，也可以使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)。

```azurepowershell
Get-AzSubscription
```

**管理组范围** 

对于管理组范围，你需要管理组名称。 可以在 Azure 门户的“管理组”页上找到此名称，也可以使用 [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-assign-role"></a>步骤 4：分配角色

若要分配角色，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 命令。 根据范围，命令通常采用以下格式之一。

**资源范围**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**资源组范围**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**订阅范围** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**管理组范围** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="assign-role-examples"></a>分配角色示例

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>为存储帐户资源范围中的所有 blob 容器分配角色

在名为 storage12345 的存储帐户的资源范围内将[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)角色分配给对象 ID 为 55555555-5555-5555-5555-555555555555 的服务主体。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>为特定 blob 容器资源范围分配角色

在名为 blob-container-01 的 blob 容器的资源范围内将[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)角色分配给对象 ID 为 55555555-5555-5555-5555-555555555555 的服务主体。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>为特定虚拟网络资源范围内的某个组分配角色

将[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给名为 pharma-sales-project-network 的虚拟网络的资源范围内 ID 为“aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa”的“医药销售管理员”组。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>在资源组范围内为某个用户分配角色

在 *pharma-sales* 资源组范围内将 [虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给 *patlong\@contoso.com* 用户。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

另外，也可以使用 `-Scope` 参数来指定完全限定的资源组：

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>在资源组范围内使用唯一角色 ID 为某个用户分配角色

很多时候角色名称可能会更改，例如：

- 你使用的是自己的自定义角色，你决定更改名称。
- 你使用的是预览版角色，其名称中有“(预览)”字样。 发布角色时重命名了角色。

即使重命名了角色，角色 ID 也不会更改。 如果使用脚本或自动化来创建角色分配，最佳做法是使用唯一的角色 ID 而非角色名称。 这样一来，即使角色重命名，脚本仍可以使用。

以下示例将 [“虚拟机参与者”角色](built-in-roles.md#virtual-machine-contributor)分配给 *pharma-sales* 资源组范围内的 *patlong\@contoso.com* 用户。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>在资源组范围内为某个应用程序分配角色

在 pharma-sales 资源组范围内将[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给服务主体对象 ID 为“77777777-7777-7777-7777-777777777777”的应用程序。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>在订阅范围内为某个用户分配角色

在订阅范围内将[读者](built-in-roles.md#reader)角色分配给 annm\@example.com 用户。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>在管理组范围内为某个用户分配角色

在管理组范围内将[账单读者](built-in-roles.md#billing-reader)角色分配给 alain\@example.com 用户。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure PowerShell 列出 Azure 角色分配](role-assignments-list-powershell.md)
- [教程：使用 Azure PowerShell 向组授予对 Azure 资源的访问权限](tutorial-role-assignments-group-powershell.md)
- [使用 Azure PowerShell 管理资源](../azure-resource-manager/management/manage-resources-powershell.md)
