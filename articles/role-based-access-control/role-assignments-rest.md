---
title: 使用 REST API 分配 Azure 角色 - Azure RBAC
description: 了解如何使用 REST API 和 Azure 基于角色的访问控制 (Azure RBAC) 为用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 0c59db98f3f38a7e715c0dce77c397f0e2562343
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356648"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>使用 REST API 分配 Azure 角色

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 本文介绍如何使用 REST API 分配角色。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>分配 Azure 角色

若要分配角色，请使用[角色分配 - Create](/rest/api/authorization/roleassignments/create) REST API 并指定安全主体、角色定义和范围。 若要调用此 API，必须具有对 `Microsoft.Authorization/roleAssignments/write` 操作的访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。

1. 使用[角色定义 - List](/rest/api/authorization/roledefinitions/list) REST API 或参阅[内置角色](built-in-roles.md)，获取你想要分配的角色定义的标识符。

1. 使用 GUID 工具生成将用于角色分配标识符的唯一标识符。 标识符的格式为：`00000000-0000-0000-0000-000000000000`

1. 从以下请求和正文开始：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. 在 URI 内，将“{scope}”替换为角色分配的范围。

    > [!div class="mx-tableFixed"]
    > | 作用域 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 资源 |

    在前面的示例中，microsoft.web 是引用应用服务实例的资源提供程序。 同样，可以使用任何其他资源提供程序并指定范围。 有关详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)和支持的 [Azure 资源提供程序操作](resource-provider-operations.md)。  

1. 将“{roleAssignmentId}”替换为角色分配的 GUID 标识符。

1. 在请求正文中，将 *{scope}* 替换为角色分配的范围。

    > [!div class="mx-tableFixed"]
    > | 作用域 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 资源 |

1. 将“{roleDefinitionId}”替换为角色定义标识符。

1. 将“{principalId}”替换为将分配有角色的用户、组或服务主体的对象标识符。

以下请求和正文将[备份读取者](built-in-roles.md#backup-reader)角色分配给订阅范围内的用户：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

下面显示了输出示例：

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

### <a name="new-service-principal"></a>新服务主体

如果创建新服务主体并立即尝试将角色分配给该服务主体，则在某些情况下该角色分配可能会失败。 例如，如果创建新的托管标识，然后尝试将角色分配给该服务主体，则角色分配可能会失败。 失败原因可能是复制延迟。 服务主体是在一个区域中创建的；但是，角色分配可能发生在尚未复制服务主体的其他区域中。

若要解决这种情况，请使用[角色分配 - 创建](/rest/api/authorization/roleassignments/create) REST API 并将 `principalType` 属性设置为 `ServicePrincipal`。 还必须将 `apiVersion` 设置为 `2018-09-01-preview` 或更高版本。

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>后续步骤

- [使用 REST API 列出 Azure 角色分配](role-assignments-list-rest.md)
- [使用 Resource Manager 模板和 Resource Manager REST API 部署资源](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API 参考](/rest/api/azure/)
- [使用 REST API 创建或更新 Azure 自定义角色](custom-roles-rest.md)
