---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 8b4fd9356810b6ef44ea3401ac87c12c4f58b3d8
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103166"
---
<!-- ### Give a Media Services Managed identity access to a storage account. -->

以下命令为媒体服务托管标识授予对存储帐户的访问权限。

在下面的命令中，请将 `your-resource-group-name` 更改为资源组名称，将 `your-media-services-account-name` 更改为要使用的媒体服务帐户名称：

```azurecli-interactive
az ams account storage set-authentication --storage-auth ManagedIdentity --resource-group <your-resource-group_name> --account-name <your-media-services-account-name>

```

示例 JSON 响应：

```json
{
  "encryption": {
    "keyVaultProperties": null,
    "type": "SystemKey"
  },
  "id": "/subscriptions/00000000-0000-0000-00000000/resourceGroups/your-resource-group-name/providers/Microsoft.Media/mediaservices/your-storage-account-name",
  "identity": null,
  "location": "West US 2",
  "mediaServiceId": "00000000-0000-0000-00000000",
  "name": "your-media-services-account",
  "resourceGroup": "your-resource-group-name",
  "storageAccounts": [
    {
      "id": "/subscriptions/2b461b25-f7b4-4a22-90cc-d640a14b5471/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
      "resourceGroup": "your-resource-group-name",
      "type": "Primary"
    }
  ],
  "storageAuthentication": "ManagedIdentity",
  "systemData": {
    "createdAt": "2021-05-17T19:15:00.8850297Z",
    "createdBy": "you@example.com",
    "createdByType": "User",
    "lastModifiedAt": "2021-05-17T21:23:11.3863627Z",
    "lastModifiedBy": "you@example.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}

```
