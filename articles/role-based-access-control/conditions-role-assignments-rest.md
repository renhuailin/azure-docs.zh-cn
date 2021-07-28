---
title: 使用 REST API 添加或编辑 Azure 角色分配条件（预览版）- Azure RBAC
description: 了解如何使用 REST API 和 Azure 基于角色的访问控制 (Azure RBAC) 在 Azure 角色分配中添加、编辑、列出或删除基于特性的访问控制 (ABAC) 条件。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: 4a929fd99f10e66ea07dacc0302dd6d0bba83a96
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656553"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-the-rest-api-preview"></a>使用 REST API 添加或编辑 Azure 角色分配条件（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[Azure 角色分配条件](conditions-overview.md)是一项额外检查，你可选择将其添加到角色分配中，来提供更精细的访问控制。 例如，为了读取对象，你可添加要求对象具有特定标记的条件。 本文介绍如何使用 REST API 为角色分配添加、编辑、列出或删除条件。

## <a name="prerequisites"></a>先决条件

有关添加或编辑角色分配条件的先决条件的信息，请参阅[条件的先决条件](conditions-prerequisites.md)。

## <a name="add-a-condition"></a>添加条件

若要添加角色分配条件，请使用[角色分配 - Create](/rest/api/authorization/roleassignments/create) REST API。 将 `api-version` 设置为 `2020-03-01-preview` 或更高版本。 如果要对角色分配使用 `description` 属性，请使用 `2020-04-01-preview` 或更高版本。  [角色分配 - Create](/rest/api/authorization/roleassignments/create) 包括与条件相关的以下参数。

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `condition` | 字符串 | 可授予用户权限的条件。 |
| `conditionVersion` | 字符串 | 条件语法的版本。 如果指定了 `condition` 而未指定 `conditionVersion`，则版本设为默认值 2.0。 |

使用以下请求和正文：

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}",
        "condition": "{condition}",
        "conditionVersion": "2.0",
        "description": "{description}"
    }
}
```

以下示例演示如何使用条件分配[存储 Blob 数据读者](built-in-roles.md#storage-blob-data-reader)角色。 该条件检查容器名称是否为“blobs-example-container”。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
        "conditionVersion": "2.0",
        "description": "Read access if container name equals blobs-example-container"
    }
}
```

下面显示了输出示例：

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "principalType": "User",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
        "conditionVersion": "2.0",
        "createdOn": "2021-04-20T06:20:44.0205560Z",
        "updatedOn": "2021-04-20T06:20:44.2955371Z",
        "createdBy": null,
        "updatedBy": "{updatedById}",
        "delegatedManagedIdentityResourceId": null,
        "description": "Read access if container name equals blobs-example-container"
    },
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId}"
}
```

## <a name="edit-a-condition"></a>编辑条件

若要编辑现有角色分配条件，请使用添加角色分配条件所用的[角色分配 - Create](/rest/api/authorization/roleassignments/create) REST API。 以下是示例 JSON，其中 `condition` 和 `description` 已更新。 只有 `condition`、`conditionVersion` 和 `description` 属性可以编辑。 必须指定其他属性以匹配现有角色分配。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
        "conditionVersion": "2.0",
        "description": "Read access if container name equals blobs-example-container or blobs-example-container2"
    }
}
```


## <a name="list-a-condition"></a>列出条件

若要列出角色分配条件，请使用[角色分配 - List](/rest/api/authorization/roleassignments/list) REST API。 将 `api-version` 设置为 `2020-03-01-preview` 或更高版本。 如果要对角色分配使用 `description` 属性，请使用 `2020-04-01-preview` 或更高版本。 有关详细信息，请参阅[使用 REST API 列出 Azure 角色分配](role-assignments-list-rest.md)。

## <a name="delete-a-condition"></a>删除条件

若要删除角色分配条件，请编辑角色分配条件并将条件和条件版本设置为空字符串或 null。

另外，如果想同时删除角色分配和条件，可以使用[角色分配 - Delete](/rest/api/authorization/roleassignments/delete) API。 有关详细信息，请参阅[删除 Azure 角色分配](role-assignments-remove.md)。

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件（预览版）](../storage/common/storage-auth-abac-examples.md)
- [教程：使用 Azure 门户添加角色分配条件来限制对 Blob 的访问（预览版）](../storage/common/storage-auth-abac-portal.md)
- [排查 Azure 角色分配条件问题（预览版）](conditions-troubleshoot.md)
