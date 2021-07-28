---
title: 使用 Azure CLI 添加或编辑 Azure 角色分配条件（预览版）- Azure RBAC
description: 了解如何使用 Azure CLI 和 Azure 基于角色的访问控制 (Azure RBAC) 在 Azure 角色分配中添加、编辑、列出或删除基于特性的访问控制 (ABAC) 条件。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: c7baafbd34e9af488fbbee6237625aa469dff12c
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656625"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-azure-cli-preview"></a>使用 Azure CLI 添加或编辑 Azure 角色分配条件（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[Azure 角色分配条件](conditions-overview.md)是一项额外检查，你可选择将其添加到角色分配中，来提供更精细的访问控制。 例如，为了读取对象，你可添加要求对象具有特定标记的条件。 本文介绍如何使用 Azure CLI 为角色分配添加、编辑、列出或删除条件。

## <a name="prerequisites"></a>先决条件

有关添加或编辑角色分配条件的先决条件的信息，请参阅[条件的先决条件](conditions-prerequisites.md)。

## <a name="add-a-condition"></a>添加条件

若要添加角色分配条件，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)。 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令包含以下与条件相关的参数。

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `condition` | 字符串 | 可授予用户权限的条件。 |
| `condition-version` | 字符串 | 条件语法的版本。 如果指定了 `--condition` 而未指定 `--condition-version`，则版本设为默认值 2.0。 |

以下示例演示如何使用条件分配[存储 Blob 数据读者](built-in-roles.md#storage-blob-data-reader)角色。 该条件检查容器名称是否为“blobs-example-container”。

```azurecli
az role assignment create --role "Storage Blob Data Reader" --assignee "user1@contoso.com" --resource-group {resourceGroup} \
--description "Read access if container name equals blobs-example-container" \
--condition "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))" \
--condition-version "2.0"
```

下面显示了输出示例：

```azurecli
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="edit-a-condition"></a>编辑条件

若要编辑现有角色分配条件，请使用 [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) 和 JSON 文件作为输入。 以下是一个示例 JSON 文件，其中条件和说明已更新。 只有 `condition`、`conditionVersion` 和 `description` 属性可以编辑。 必须指定所有属性才能更新角色分配条件。

```json
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

使用 [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) 更新角色分配的条件。

```azurecli
az role assignment update --role-assignment "./path/roleassignment.json"
```

下面显示了输出示例：

```azurecli
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="list-a-condition"></a>列出条件

若要列出角色分配条件，请使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)。 有关详细信息，请参阅[使用 Azure CLI 列出 Azure 角色分配](role-assignments-list-cli.md)。

## <a name="delete-a-condition"></a>删除条件

若要删除角色分配条件，请编辑角色分配条件并将 `condition` 和 `condition-version` 属性设置为空字符串 (`""`) 或 `null`。

另外，如果想同时删除角色分配和条件，可以使用 [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) 命令。 有关详细信息，请参阅[删除 Azure 角色分配](role-assignments-remove.md)。

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件（预览版）](../storage/common/storage-auth-abac-examples.md)
- [教程：使用 Azure CLI 添加角色分配条件以限制对 Blob 的访问（预览版）](../storage/common/storage-auth-abac-cli.md)
- [排查 Azure 角色分配条件问题（预览版）](conditions-troubleshoot.md)
