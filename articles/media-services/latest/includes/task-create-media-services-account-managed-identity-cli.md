---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 72dc27bf1600d298d41a034b081b216d3d77c94c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103129"
---
<!--Create a media services account -->

以下 Azure CLI 命令创建新的媒体服务帐户。 将下列值替换为你要使用的名称：`your-media-services-account-name`、`your-storage-account-name` 和 `your-resource-group-name`。 该命令假定你已创建了资源组和存储帐户。 

它使用 `--mi-system-assigned` 标志为媒体服务帐户提供系统分配的托管标识。

```azurecli-interactive

az ams account create --name <your-media-services-account-name> --resource-group <your-resource-group-name> --mi-system-assigned --storage-account <your-storage-account-name>

```

示例 JSON 响应：

```json
{
  "encryption": {
    "keyVaultProperties": null,
    "type": "SystemKey"
  },
  "id": "/subscriptions/00000000-0000-0000-0000-00000000/resourceGroups/your-resource-group/providers/Microsoft.Media/mediaservices/your-media-services-account-name",
  "identity": {
    "principalId": "00000000-0000-0000-0000-00000000",
    "tenantId": "00000000-0000-0000-0000-00000000",
    "type": "SystemAssigned"
  },
  "location": "your-region",
  "mediaServiceId": "00000000-0000-0000-0000-00000000",
  "name": "your-media-services-account-name",
  "resourceGroup": "your-resource-group",
  "storageAccounts": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-00000000/resourceGroups/mediatest1rg/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
      "resourceGroup": "your-resource-group",
      "type": "Primary"
    }
  ],
  "storageAuthentication": "System",
  "systemData": {
    "createdAt": "2021-05-14T21:25:12.3492071Z",
    "createdBy": "you@example.com",
    "createdByType": "User",
    "lastModifiedAt": "2021-05-14T21:25:12.3492071Z",
    "lastModifiedBy": "you@example.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```
