---
title: 适用于 Azure 专用链接的 Azure RBAC 权限
description: 开始了解部署专用终结点和专用链接服务所需的 Azure RBAC 权限。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 5/25/2021
ms.custom: template-concept
ms.openlocfilehash: 50b648c5419b995f9c808288895af49b12f29b86
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178317"
---
# <a name="azure-rbac-permissions-for-azure-private-link"></a>适用于 Azure 专用链接的 Azure RBAC 权限

对于任何组织而言，云资源的访问管理都是一项重要功能。 Azure 基于角色的访问控制 (Azure RBAC) 管理 Azure 资源的访问和操作。

若要部署专用终结点或专用链接服务，用户必须已分配有内置角色，例如： 

* [所有者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#owner)
* [参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#contributor)
* [网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#network-contributor)

可以通过创建一个具有以下各部分所述权限的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json)来提供更精细的访问。

> [!IMPORTANT]
> 本文列出了用于创建专用终结点或专用链接服务的特定权限。 确保添加与你要通过专用链接授予访问权限的服务相关的特定权限，例如 Azure SQL 的 Microsoft.SQL 参与者角色。 有关内置角色的详细信息，请参阅[基于角色的访问控制](../role-based-access-control/built-in-roles.md)。

要部署的 Microsoft.Network 和特定的资源提供程序（例如，Sql-dmo）必须在订阅级别进行注册：

![image](https://user-images.githubusercontent.com/20302679/129105527-b946eee9-038a-46ef-b446-be371eb23ca9.png)

## <a name="private-endpoint"></a>专用终结点

本部分列出了部署专用终结点所需的精细权限。

| 操作                                                              | 说明                                                                      |
| ---------                                                           | -------------                                                                 |
| Microsoft.Resources/deployments/*                                   | 创建和管理部署                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | 读取资源组的资源                                     |
| Microsoft.Network/virtualNetworks/read                              | 读取虚拟网络定义                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | 读取虚拟网络子网定义                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | 创建虚拟网络子网，或更新现有的虚拟网络子网|
| Microsoft.Network/virtualNetworks/subnets/join/action               | 加入虚拟网络                                                       |
| Microsoft.Network/privateEndpoints/read                             | 读取专用终结点资源                                             |
| Microsoft.Network/privateEndpoints/write                            | 创建新的专用终结点，或更新现有的专用终结点       |
| Microsoft.Network/locations/availablePrivateEndpointTypes/read      | 读取可用的专用终结点资源                                     |

下面是上述权限的 JSON 格式。 输入自己的 roleName、description 和 assignableScopes：

```JSON
{
 "properties": {
   "roleName": "Role Name",
   "description": "Description",
   "assignableScopes": [
     "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
   ],
   "permissions": [
     {
       "actions": [
         "Microsoft.Resources/deployments/*",
         "Microsoft.Resources/subscriptions/resourceGroups/read",
         "Microsoft.Network/virtualNetworks/read",
         "Microsoft.Network/virtualNetworks/subnets/read",
         "Microsoft.Network/virtualNetworks/subnets/write",
         "Microsoft.Network/virtualNetworks/subnets/join/action",
         "Microsoft.Network/privateEndpoints/read",
         "Microsoft.Network/privateEndpoints/write",
         "Microsoft.Network/locations/availablePrivateEndpointTypes/read"
       ],
       "notActions": [],
       "dataActions": [],
       "notDataActions": []
     }
   ]
 }
}
```

## <a name="private-link-service"></a>专用链接服务

本部分列出了部署专用链接服务所需的精细权限。

| 操作 | 说明   |
| --------- | ------------- |
| Microsoft.Resources/deployments/*                                   | 创建和管理部署                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | 读取资源组的资源                                     |
| Microsoft.Network/virtualNetworks/read                              | 读取虚拟网络定义                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | 读取虚拟网络子网定义                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | 创建虚拟网络子网，或更新现有的虚拟网络子网|
| Microsoft.Network/privateLinkServices/read | 读取专用链接服务资源|
| Microsoft.Network/privateLinkServices/write | 创建新的专用链接服务，或更新现有的专用链接服务|
| Microsoft.Network/privateLinkServices/privateEndpointConnections/read | 读取专用终结点连接定义 |
| Microsoft.Network/privateLinkServices/privateEndpointConnections/write | 创建新的专用终结点连接，或更新现有的专用终结点连接|
| Microsoft.Network/networkSecurityGroups/join/action | 加入网络安全组 |
| Microsoft.Network/loadBalancers/read | 读取负载均衡器定义 |
| Microsoft.Network/loadBalancers/write | 创建负载均衡器，或更新现有的负载均衡器 |

```JSON
{
  "properties": {
    "roleName": "Role Name",
    "description": "Description",
    "assignableScopes": [
      "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/write",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/privateLinkServices/read",
          "Microsoft.Network/privateLinkServices/write",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/read",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/write",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Network/loadBalancers/read",
          "Microsoft.Network/loadBalancers/write"
        ],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

## <a name="next-steps"></a>后续步骤

有关 Azure 专用链接中的专用终结点和专用链接服务的详细信息，请参阅：

- [什么是 Azure 专用终结点？](private-endpoint-overview.md)
- [什么是 Azure 专用链接服务？](private-link-service-overview.md)
