---
title: 使用 Azure CLI 添加或删除 Azure 角色分配 - Azure RBAC
description: 了解如何使用 Azure CLI 和 Azure 基于角色的访问控制 (Azure RBAC) 向用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperfq1, devx-track-azurecli
ms.openlocfilehash: 94052b847f48a9fd676496601d85a8cb58a76944
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184291"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>使用 Azure CLI 添加或删除 Azure 角色分配

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 本文介绍如何使用 Azure CLI 分配角色。

## <a name="prerequisites"></a>先决条件

若要添加或删除角色分配，必须拥有以下权限：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)
- [Bash Azure Cloud Shell](../cloud-shell/overview.md) 或 [Azure CLI](/cli/azure)

## <a name="steps-to-add-a-role-assignment"></a>角色分配的添加步骤

在 Azure RBAC 中，若要授予访问权限，请添加角色分配。 角色分配包含三个要素：安全主体、角色订阅和范围。 若要添加角色分配，请执行以下步骤。

### <a name="step-1-determine-who-needs-access"></a>步骤 1：确定谁需要访问权限

可以将角色分配到用户、组、服务主体或托管标识。 若要添加角色分配，你可能需要指定对象的唯一 ID。 ID 的格式为：`11111111-1111-1111-1111-111111111111`。 可以使用 Azure 门户或 Azure CLI 获取 ID。

**User**

对于 Azure AD 用户，请获取用户主体名称（例如 *patlong\@contoso.com*）或用户对象 ID。 若要获取对象 ID，可以使用 [az ad user show](/cli/azure/ad/user#az_ad_user_show)。

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**组**

对于 Azure AD 组，你需要组对象 ID。 若要获取对象 ID，可以使用 [az ad group show](/cli/azure/ad/group#az_ad_group_show) 或 [az ad group list](/cli/azure/ad/group#az_ad_group_list)。

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**服务主体**

对于 Azure AD 服务主体（应用程序使用的标识），你需要服务主体对象 ID。 若要获取对象 ID，可以使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)。 对于服务主体，使用对象 ID，而不是应用程序 ID。

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**托管的标识**

对于系统分配的或用户分配的托管标识，你需要对象 ID。 若要获取对象 ID，可以使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)。

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

若要仅列出用户分配的托管标识，可以使用 [az identity list](/cli/azure/identity#az_identity_list)。

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>步骤 2：查找适当的角色

权限组合成角色。 可以从包含多个 [Azure 内置角色](built-in-roles.md)的列表中选择，也可以使用自己的自定义角色。 最佳做法是以所需的最少权限授予访问权限，因此避免分配范围更广泛的角色。

若要列出角色并获取唯一的角色 ID，可以使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list)。

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

下面介绍了如何列出特定角色的详细信息。

```azurecli
az role definition list --name "{roleName}"
```

有关详细信息，请参阅[列出 Azure 角色定义](role-definitions-list.md#azure-cli)。
 
### <a name="step-3-identify-the-needed-scope"></a>步骤 3：识别所需的范围

Azure 提供四个级别的范围：资源、[资源组](../azure-resource-manager/management/overview.md#resource-groups)、订阅，以及[管理组](../governance/management-groups/overview.md)。 最佳做法是以所需的最少权限授予访问权限，因此避免在更广泛的范围分配角色。 有关范围的详细信息，请参阅[了解范围](scope-overview.md)。

**资源范围**

对于资源范围，你需要资源的资源 ID。 可以通过在 Azure 门户中查看资源的属性来找到资源 ID。 资源 ID 采用以下格式。

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**资源组范围**

对于资源组范围，你需要资源组的名称。 可以在 Azure 门户的 **“资源组”** 页上找到此名称，也可以使用 [az group list](/cli/azure/group#az_group_list)。

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**订阅范围** 

对于订阅范围，你需要订阅 ID。 可以在 Azure 门户中的“订阅”页上找到 ID，也可以使用 [az account list](/cli/azure/account#az_account_list)。

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**管理组范围** 

对于管理组范围，你需要管理组名称。 可以在 Azure 门户中的“管理组”页面上找到此名称，也可以使用 [az account management-group list](/cli/azure/account/management-group#az_account_management_group_list)。

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>步骤 4：添加角色分配

若要添加角色分配，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令。 根据范围，命令通常采用以下格式之一。

**资源范围**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**资源组范围**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**订阅范围** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**管理组范围** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

下面显示了在资源组范围内将[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给用户时的输出示例。

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="add-role-assignment-examples"></a>添加角色分配示例

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>为存储帐户资源范围中的所有 blob 容器添加角色分配

在名为 storage12345 的存储帐户的资源范围内将[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)角色分配给对象 ID 为 55555555-5555-5555-5555-555555555555 的服务主体。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>为特定 blob 容器资源范围添加角色分配

在名为 blob-container-01 的 blob 容器的资源范围内将[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)角色分配给对象 ID 为 55555555-5555-5555-5555-555555555555 的服务主体。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>为特定虚拟网络资源范围内的某个组添加角色分配

将[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给名为 pharma-sales-project-network 的虚拟网络的资源范围内 ID 为 22222222-2222-2222-2222-222222222222 的“Ann Mack 团队”组。

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>在资源组范围内为某个用户添加角色分配

在 *pharma-sales* 资源组范围内将 [虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给 *patlong\@contoso.com* 用户。

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>在资源组范围内使用唯一角色 ID 为某个用户添加角色分配

很多时候角色名称可能会更改，例如：

- 你使用的是自己的自定义角色，你决定更改名称。
- 你使用的是预览版角色，其名称中有“(预览)”字样。 发布角色时重命名了角色。

即使重命名了角色，角色 ID 也不会更改。 如果使用脚本或自动化来创建角色分配，最佳做法是使用唯一的角色 ID 而非角色名称。 这样一来，即使角色重命名，脚本仍可以使用。

以下示例将 [“虚拟机参与者”角色](built-in-roles.md#virtual-machine-contributor)分配给 *pharma-sales* 资源组范围内的 *patlong\@contoso.com* 用户。

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>在资源组范围内为所有 blob 容器添加角色分配

在 Example-Storage-rg 资源组范围内将[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)角色分配给对象 ID 为 55555555-5555-5555-5555-555555555555 的服务主体。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

另外，也可以使用 `--scope` 参数来指定完全限定的资源组：

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>在资源组范围内为应用程序添加角色分配

在 pharma-sales 资源组范围内将[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给服务主体对象 ID 为 44444444-4444-4444-4444-444444444444 的应用程序。

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>在资源组范围内为新的服务主体添加角色分配

如果创建新的服务主体并立即尝试将角色分配给该服务主体，则在某些情况下该角色分配可能会失败。 例如，如果使用脚本创建新的托管标识，然后尝试将角色分配给该服务主体，则角色分配可能会失败。 此失败的原因可能是复制延迟。 服务主体是在一个区域中创建的；但是，角色分配可能发生在尚未复制服务主体的另一个区域中。 若要解决这种情况，应该在创建角色分配时指定主体类型。

若要添加角色分配，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)，为 `--assignee-object-id` 指定值，然后将 `--assignee-principal-type` 设置为 `ServicePrincipal`。

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

以下示例将“虚拟机参与者”角色分配给“pharma-sales”资源组范围内的 msi-test 托管标识：

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>在订阅范围内为用户添加角色分配

在订阅范围内将[读者](built-in-roles.md#reader)角色分配给 annm\@example.com 用户。

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>在订阅范围内为组添加角色分配

在订阅范围内将[读者](built-in-roles.md#reader)角色分配给 ID 为 22222222-2222-2222-2222-222222222222 的“Ann Mack 团队”组。

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>在订阅范围内为所有 blob 容器添加角色分配

在订阅范围内将[存储 Blob 数据读者](built-in-roles.md#storage-blob-data-reader)角色分配给 alain\@example.com 用户。

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>在管理组范围内为用户添加角色分配

在管理组范围内将[账单读者](built-in-roles.md#billing-reader)角色分配给 alain\@example.com 用户。

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-a-role-assignment"></a>删除角色分配

在 Azure RBAC 中，若要删除访问权限，请使用 [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) 删除角色分配。

以下示例在 *pharma-sales* 资源组上从 *patlong\@contoso.com* 用户删除“虚拟机参与者”角色分配：

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

在订阅范围内将[读者](built-in-roles.md#reader)角色从 ID 为 22222222-2222-2222-2222-222222222222 的“Ann Mack 团队”组中删除。

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

在管理组范围内将[账单读者](built-in-roles.md#billing-reader)角色从 alain\@example.com 用户中删除。

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 列出 Azure 角色分配](role-assignments-list-cli.md)
- [使用 Azure CLI 管理 Azure 资源和资源组](../azure-resource-manager/management/manage-resources-cli.md)
