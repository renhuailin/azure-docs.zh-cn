---
title: 通过专用链接管理资源
description: 将资源的管理访问限制为专用链接
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: eb7e81ef739fdb94bc91c65b079d5b76b22636c9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227283"
---
# <a name="use-rest-api-to-create-private-link-for-managing-azure-resources"></a>使用 REST API 创建用于管理 Azure 资源的专用链接

本文介绍如何使用 [Azure 专用链接](../../private-link/index.yml)来限制访问，以便管理订阅中的资源。

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>创建资源管理专用链接

若要创建资源管理专用链接，请发送以下请求：

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

在请求正文中，包括所需的资源位置：

```json
{
  "location":"{region}"
}
```

操作返回以下内容：

```json
{  
  "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
  "location": "{region}",
  "name": "{rmplName}",
  "properties": {
    "privateEndpointConnections": []
  },
  "resourceGroup": "{rgName}",
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks"
}
```

记下为新资源管理专用链接返回的 ID。 你将使用它来创建专用链接关联。

## <a name="create-private-link-association"></a>创建专用链接关联

若要创建专用链接关联，请使用：

```http
PUT
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupId}/providers/Microsoft.Authorization/privateLinkAssociations/{GUID}?api-version=2020-05-01 
```

在请求正文中包含以下内容：

```json
{
  "properties": {
    "privateLink": "/subscriptions/{subscription-id}/resourceGroups/{rg-name}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}",
    "publicNetworkAccess": "enabled"
  }
}
```

操作返回以下内容：

```json
{
  "id": {plaResourceId},
  "name": {plaName},
  "properties": {
    "privateLink": {rmplResourceId},
    "publicNetworkAccess": "Enabled",
    "tenantId": "{tenantId}",
    "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
  },
  "type": "Microsoft.Authorization/privateLinkAssociations"
}
```

## <a name="add-private-endpoint"></a>添加专用终结点

本文假设你已具有虚拟网络。 在将用于专用终结点的子网中，必须关闭专用终结点网络策略。 如果尚未关闭专用终结点网络策略，请参阅[禁用专用终结点的网络策略](../../private-link/disable-private-endpoint-network-policy.md)。

若要创建专用终结点，请使用以下操作：

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/privateEndpoints/{privateEndpointName}?api-version=2020-11-01
```

在请求正文中，将 `privateServiceLinkId` 设置为资源管理专用链接的 ID。 `groupIds` 必须包含 `ResourceManagement`。 专用终结点的位置必须与子网的位置相同。

```json
{
  "location": "westus2",
  "properties": {
    "privateLinkServiceConnections": [
      {
        "name": "{connection-name}",
        "properties": {
           "privateLinkServiceId": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
           "groupIds": [
              "ResourceManagement"
           ]
         }
      }
    ],
    "subnet": {
      "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
    }
  }
}
```

下一步操作取决于你使用的是自动批准还是手动批准。 有关批准的详细信息，请参阅[使用审批工作流访问专用链接资源](../../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)。

响应包括审批状态。

```json
"privateLinkServiceConnectionState": {
    "actionsRequired": "None",
    "description": "",
    "status": "Approved"
},
```

如果请求自动获得批准，可以继续进行下一部分。 如果请求需要手动批准，请等待网络管理员批准专用终结点连接。

## <a name="next-steps"></a>后续步骤

若要详细了解专用链接，请参阅 [Azure 专用链接](../../private-link/index.yml)。