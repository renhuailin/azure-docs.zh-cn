---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f1b77e4cee809d7ddb7bbbf7de6c5ac483b150e3
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110060091"
---
<!-- Create a resource group -->

使用以下命令来创建资源组。 将 `your-resource-group-name` 更改为要为你的资源组指定的名称。

将 `your-region` 更改为用于存储媒体服务帐户的媒体和元数据记录的地理区域。 此区域用于处理和流式传输媒体。

```azurecli-interactive
az group create --name <your-resource-group-name> --location <your-region>
```

示例 JSON 响应：

```json
{
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name",
  "location": "your-region",
  "managedBy": null,
  "name": "your-resource-group-name",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}
```
