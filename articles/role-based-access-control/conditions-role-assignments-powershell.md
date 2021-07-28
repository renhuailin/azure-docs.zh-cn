---
title: 使用 Azure PowerShell 添加或编辑 Azure 角色分配条件（预览版）- Azure RBAC
description: 了解如何使用 Azure PowerShell 和 Azure 基于角色的访问控制 (Azure RBAC) 在 Azure 角色分配中添加、编辑、列出或删除基于特性的访问控制 (ABAC) 条件。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: 0ecc87a59d6db01c5c5dc4093bb42e4ee9c190d9
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656571"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-azure-powershell-preview"></a>使用 Azure PowerShell 添加或编辑 Azure 角色分配条件（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[Azure 角色分配条件](conditions-overview.md)是一项额外检查，你可选择将其添加到角色分配中，来提供更精细的访问控制。 例如，为了读取对象，你可添加要求对象具有特定标记的条件。 本文介绍如何使用 Azure PowerShell 为角色分配添加、编辑、列出或删除条件。

## <a name="prerequisites"></a>先决条件

有关添加或编辑角色分配条件的先决条件的信息，请参阅[条件的先决条件](conditions-prerequisites.md)。

## <a name="add-a-condition"></a>添加条件

若要添加角色分配条件，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 命令包含以下与条件相关的参数。

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `Condition` | 字符串 | 可授予用户权限的条件。 |
| `ConditionVersion` | 字符串 | 条件语法的版本。 必须设置为 2.0。 如果指定了 `Condition`，则也必须指定 `ConditionVersion`。 |

以下示例演示如何初始化变量以使用条件分配[ Blob 数据读者](built-in-roles.md#storage-blob-data-reader)角色。 该条件检查容器名称是否为“blobs-example-container”。

```azurepowershell
$subscriptionId = "<subscriptionId>"
$resourceGroup = "<resourceGroup>"
$roleDefinitionName = "Storage Blob Data Reader"
$roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
$userObjectId = "<userObjectId>"
$scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
$description = "Read access if container name equals blobs-example-container"
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$conditionVersion = "2.0"
```

使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 通过条件分配角色。

```azurepowershell
New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
```

这是一个输出示例：

```azurepowershell
RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
DisplayName        : User1
SignInName         : user1@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : <userObjectId>
ObjectType         : User
CanDelegate        : False
Description        : Read access if container name equals blobs-example-container
ConditionVersion   : 2.0
Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))
```

在 PowerShell 中，如果条件包含美元符号 ($)，则必须使用反撇号 (\`) 作为其前缀。 例如，以下条件使用美元符号来描述标记键名称。 若要详细了解 PowerShell 中的引号规则，请参阅[关于引号规则](/powershell/module/microsoft.powershell.core/about/about_quoting_rules)。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
```

## <a name="edit-a-condition"></a>编辑条件

若要编辑现有角色分配条件，请使用 [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment)。 只有 `Condition`、`ConditionVersion` 和 `Description` 属性可以编辑。 `-PassThru` 参数会导致 [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) 返回更新的角色分配，这允许在变量中进行可视化或存储，以供后续使用。

可以通过两种方法编辑条件。 可以使用 `PSRoleAssignment` 对象或 JSON 文件。

### <a name="edit-a-condition-using-the-psroleassignment-object"></a>使用 PSRoleAssignment 对象编辑条件

1. 使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 通过将条件设为 `PSRoleAssignment` 对象获取现有的角色分配。

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. 编辑条件。

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))"
    ```

1. 初始化条件和说明。

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access if container name equals blobs-example-container or blobs-example-container2"
    ```

1. 使用 [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) 更新角色分配的条件。

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    这是一个输出示例：

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : User1
    SignInName         : user1@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access if container name equals blobs-example-container or blobs-example-container2
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))
    ```

### <a name="edit-a-condition-using-a-json-file"></a>使用 JSON 文件编辑条件

若要编辑条件，还可以提供 JSON 文件作为输入。 以下是示例 JSON 文件，其中 `Condition` 和 `Description` 已更新。 必须指定 JSON 文件中的所有属性才能更新条件。

```json
{
    "RoleDefinitionId": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "ObjectId": "<userObjectId>",
    "ObjectType": "User",
    "Scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>",
    "Condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "ConditionVersion": "2.0",
    "CanDelegate": false,
    "Description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "RoleAssignmentId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>"
}
```

使用 [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) 更新角色分配的条件。

```azurepowershell
Set-AzRoleAssignment -InputFile "C:\path\roleassignment.json" -PassThru
```

这是一个输出示例：

```azurepowershell
RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
DisplayName        : User1
SignInName         : user1@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : <userObjectId>
ObjectType         : User
CanDelegate        : False
Description        : Read access if container name equals blobs-example-container or blobs-example-container2
ConditionVersion   : 2.0
Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))
```

## <a name="list-a-condition"></a>列出条件

若要列出角色分配条件，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。 有关详细信息，请参阅[使用 Azure PowerShell 列出 Azure 角色分配](role-assignments-list-powershell.md)。

## <a name="delete-a-condition"></a>删除条件

若要删除角色分配条件，请编辑角色分配条件并将 `Condition` 和 `ConditionVersion` 属性设置为空字符串 (`""`) 或 `$null`。

另外，如果想同时删除角色分配和条件，可以使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 命令。 有关详细信息，请参阅[删除 Azure 角色分配](role-assignments-remove.md)。

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件（预览版）](../storage/common/storage-auth-abac-examples.md)
- [教程：使用 Azure PowerShell 添加角色分配条件以限制对 Blob 的访问（预览版）](../storage/common/storage-auth-abac-powershell.md)
- [排查 Azure 角色分配条件问题（预览版）](conditions-troubleshoot.md)
