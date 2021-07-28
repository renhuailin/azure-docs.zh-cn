---
title: 使用 Azure CLI 列出 Azure 角色分配 - Azure RBAC
description: 了解如何使用 Azure CLI 和 Azure 基于角色的访问控制 (Azure RBAC) 来确定用户、组、服务主体和托管标识有权访问的资源内容。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 35170cb047ae7b4cfd376f86a76a36c88bbbaaa2
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109787484"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>使用 Azure CLI 列出 Azure 角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] 本文介绍如何使用 Azure CLI 列出角色分配。

> [!NOTE]
> 如果组织已将管理功能外包给使用 [Azure Lighthouse](../lighthouse/overview.md) 的服务提供商，则此处将不会显示该服务提供商授权的角色分配。

## <a name="prerequisites"></a>先决条件

- [Azure Cloud Shell Bash](../cloud-shell/overview.md) 或 [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>列出用户的角色分配

若要列出特定用户的角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)：

```azurecli
az role assignment list --assignee {assignee}
```

默认情况下，将仅显示当前订阅的角色分配。 若要查看当前订阅和以下订阅的角色分配，请添加 `--all` 参数。 若要查看继承的角色分配，请添加 `--include-inherited` 参数。

以下示例列出的角色分配直接分配给 *patlong\@contoso.com* 用户：

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>列出资源组的角色分配

若要列出存在于资源组范围的角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)：

```azurecli
az role assignment list --resource-group {resourceGroup}
```

以下示例列出 *pharma-sales* 资源组的角色分配：

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>列出订阅的角色分配

若要列出订阅范围内的所有角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)。 若要获取订阅 ID，可以在 Azure 门户中的“订阅”边栏选项卡上找到它，也可以使用 [az account list](/cli/azure/account#az_account_list)。

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

示例：

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理组的角色分配

若要列出管理组范围内的所有角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)。 若要获取管理组 ID，可以在 Azure 门户中的“管理组”边栏选项卡上找到它，也可以使用 [az account management-group list](/cli/azure/account/management-group#az_account_management_group_list)。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

示例：

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出托管标识的角色分配

1. 获取系统分配的或用户分配的托管标识的主体 ID。

    若要获取用户分配的托管标识的主体 ID，可以使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) 或 [az identity list](/cli/azure/identity#az_identity_list)。

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    若要获取系统分配的托管标识的主体 ID，可以使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)。

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. 若要列出角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)。

    默认情况下，将仅显示当前订阅的角色分配。 若要查看当前订阅和以下订阅的角色分配，请添加 `--all` 参数。 若要查看继承的角色分配，请添加 `--include-inherited` 参数。

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 分配 Azure 角色](role-assignments-cli.md)