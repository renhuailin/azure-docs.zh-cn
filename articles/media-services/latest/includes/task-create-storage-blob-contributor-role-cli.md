---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 4dae6dc7702781231965f7dc66423fde941f4185
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103169"
---
<!-- ### Create a Storage Blob Contributor role -->

以下命令创建存储 Blob 参与者角色。 

将 `assignee` 更改为 `principalId`。 该命令假定你已创建了资源组和存储帐户。  请将 `your-resource-group-name` 和 `your-storage-account-name` 用作 `scope` 值的一部分，如以下命令所示：

```azurecli-interactive
az role assignment create --assignee 00000000-0000-0000-000000000000 --role "Storage Blob Data Contributor" --scope "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/<your-resource-group-name>/providers/Microsoft.Storage/storageAccounts/<your-storage-account-name>"
```

示例 JSON 响应：

```json
{
  "canDelegate": null,
  "condition": null,
  "conditionVersion": null,
  "description": null,
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-000000000000",
  "name": "00000000-0000-0000-000000000000",
  "principalId": "00000000-0000-0000-000000000000",
  "principalType": "ServicePrincipal",
  "resourceGroup": "your-resource-group-name",
  "roleDefinitionId": "/subscriptions/00000000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-000000000000",
  "scope": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
  "type": "Microsoft.Authorization/roleAssignments"
}

```
