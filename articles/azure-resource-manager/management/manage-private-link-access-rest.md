---
title: 管理资源管理专用链接
description: 使用 REST API 管理现有资源管理专用链接
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 376fa600cce4479a0ed2c04e9d3d7b3fbeb9aeaa
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227285"
---
# <a name="manage-resource-management-private-links-with-rest-api"></a>使用 REST API 管理资源管理专用链接

本文介绍如何使用现有的资源管理专用链接。 它描述了用于获取和删除现有资源的 REST API 操作。

如果需要创建资源管理专用链接，请参阅[使用门户创建专用链接以管理 Azure 资源](create-private-link-access-portal.md)或[使用 REST API 创建专用链接以管理 Azure 资源](create-private-link-access-rest.md)。

## <a name="resource-management-private-links"></a>资源管理专用链接

若要获取特定的资源管理专用链接，请发送以下请求：

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01 
```

操作返回：

```json
{
  "properties": {
    "privateEndpointConnections": []
  },
  "id": {rmplResourceId},
  "name": {rmplName},
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
  "location": {region}
}
```

若要获取订阅中的所有资源管理专用链接，请使用：

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.Authorization/resourceManagementPrivateLinks?api-version=2020-05-01
```

操作返回：

```json
[
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  },
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  }
]
```

若要删除特定的资源管理专用链接，请使用：

```http
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

操作返回：`Status 200 OK`。

## <a name="private-link-association"></a>专用链接关联

若要获取管理组的特定专用链接关联，请使用：

```http
GET
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations?api-version=2020-05-01 
```

操作返回：

```json
{
  "value": [
    {
      "properties": {
        "privateLink": {rmplResourceID},
        "tenantId": {tenantId},
        "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
      },
      "id": {plaResourceId},
      "type": "Microsoft.Authorization/privateLinkAssociations",
      "name": {plaName}
    }
  ]
}
```

若要删除专用链接关联，请使用：

```http
DELETE 
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations/{plaID}?api-version=2020-05-01
```

操作返回：`Status 200 OK`。

## <a name="private-endpoints"></a>专用终结点

若要获取订阅中的所有专用终结点，请使用：

```http
GET 
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/privateEndpoints?api-version=2020-04-01
```

操作返回：

```json
{
  "value": [
    {
      "name": {privateEndpointName},
      "id": {privateEndpointResourceId},
      "etag": {etag},
      "type": "Microsoft.Network/privateEndpoints",
      "location": {region},
      "properties": {
        "provisioningState": "Updating",
        "resourceGuid": {GUID},
        "privateLinkServiceConnections": [
          {
            "name": {connectionName},
            "id": {connectionResourceId},
            "etag": {etag},
            "properties": {
              "provisioningState": "Succeeded",
              "privateLinkServiceId": {rmplResourceId},
              "groupIds": [
                "ResourceManagement"
              ],
              "privateLinkServiceConnectionState": {
                "status": "Approved",
                "description": "",
                "actionsRequired": "None"
              }
            },
            "type": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections"
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": {subnetResourceId}
        },
        "networkInterfaces": [
          {
            "id": {networkInterfaceResourceId}
          }
        ],
        "customDnsConfigs": [
          {
            "fqdn": "management.azure.com",
            "ipAddresses": [
              "10.0.0.4"
            ]
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

* 若要详细了解专用链接，请参阅 [Azure 专用链接](../../private-link/index.yml)。
* 若要创建资源管理专用链接，请参阅[使用门户创建专用链接以管理 Azure 资源](create-private-link-access-portal.md)或[使用 REST API 创建专用链接以管理 Azure 资源](create-private-link-access-rest.md)。