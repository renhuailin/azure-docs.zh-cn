---
title: 恢复已删除的认知服务资源
titleSuffix: Azure Cognitive Services
description: 本文介绍如何恢复已删除的认知服务资源。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: nitinme
ms.openlocfilehash: 4dce35c1713a4dcb4880080d1f28ed124a2209be
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546070"
---
# <a name="recover-deleted-cognitive-services-resources"></a>恢复已删除的认知服务资源

本文介绍如何恢复已删除的认知服务资源。 本文还介绍了如何清除已删除的资源。

> [!NOTE]
> 本文的说明同时适用于多服务资源及单服务资源。 多服务资源支持使用单个密钥和终结点访问多个认知服务。 另一方面，单服务资源仅支持访问已创建该资源的特定认知服务。

## <a name="prerequisites"></a>先决条件

* 要恢复的资源必须是在过去 48 小时内被删除的。
* 已被清除的资源不可恢复。 已清除资源无法恢复。
* 在尝试恢复已删除的资源之前，请确保该帐户的资源组存在。 如果资源组已删除，则必须重新创建它。 无法恢复资源组。 有关详细信息，请参阅 [管理资源组](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 如果被删除的资源需要使用客户管理的 Azure Key Vault 密钥，并且该密钥库也已被删除，那么在恢复认知服务资源之前，必须优先恢复密钥库。 有关详细信息，请参阅 [Azure Key Vault 恢复概述](../key-vault/general/key-vault-recovery.md)。
* 如果被删除的资源需要使用客户管理的存储，并且该存储帐户也已被删除，那么在恢复认知服务资源之前，必须先恢复存储帐户。 有关说明，请参阅[恢复已删除的存储帐户](../storage/common/storage-account-recover.md)。

订阅必须具有清除资源的 `Microsoft.CognitiveServices/locations/resourceGroups/deletedAccounts/delete` 权限，例如[认知服务参与者](../role-based-access-control/built-in-roles.md#cognitive-services-contributor)或[参与者](../role-based-access-control/built-in-roles.md#contributor)。 

## <a name="recover-a-deleted-resource"></a>恢复已删除的资源 

若要恢复已删除的认知服务资源，请使用以下命令。 如有适用，请替换：

* `{subscriptionID}` 替换为 Azure 订阅 ID
* `{resourceGroup}` 替换为资源组
* `{resourceName}` 替换为资源名称
* `{location}` 替换为资源位置

### <a name="using-the-rest-api"></a>使用 REST API

使用以下 `PUT` 命令：

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}?Api-Version=2021-04-30
```

在请求正文部分，使用以下 JSON 格式：

```json
{ 
  "location": "{location}", 
   "properties": { 
        "restore": true 
    } 
} 
```

### <a name="using-powershell"></a>使用 PowerShell

恢复资源，请使用以下命令： 

```powershell
New-AzResource -Location {location} -Properties @{restore=$true} -ResourceId /subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}   -ApiVersion 2021-04-30 
```

如需查找已删除资源的名称，可以使用以下命令获取已删除资源名称的列表： 

```powershell
Get-AzResource -ResourceId /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/deletedAccounts -ApiVersion 2021-04-30 
```

### <a name="using-the-azure-cli"></a>使用 Azure CLI

```azurecli-interactive
az resource create --subscription {subscriptionID} -g {resourceGroup} -n {resourceName} --location {location} --namespace Microsoft.CognitiveServices --resource-type accounts --properties "{\"restore\": true}"
```

## <a name="purge-a-deleted-resource"></a>清除已删除的资源 

删除资源后，在未来的 48 小时内将无法创建另一个同名资源。 若要创建同名资源，需清除已删除的资源。

清除已删除的认知服务资源，请使用以下命令。 如有适用，请替换：

* `{subscriptionID}` 替换为 Azure 订阅 ID
* `{resourceGroup}` 替换为资源组
* `{resourceName}` 替换为资源名称
* `{location}` 替换为资源位置

> [!NOTE]
> 清除资源后，该资源将被永久删除且无法恢复。 将丢失与该资源关联的所有数据和密钥。

### <a name="using-the-rest-api"></a>使用 REST API

使用以下 `DELETE` 命令：

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}?Api-Version=2021-04-30`
```

### <a name="using-powershell"></a>使用 PowerShell

```powershell
Remove-AzResource -ResourceId /subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}  -ApiVersion 2021-04-30`
```

### <a name="using-the-azure-cli"></a>使用 Azure CLI

```azurecli-interactive
az resource delete --ids /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}
```

## <a name="see-also"></a>另请参阅
* [使用 Azure 门户创建新资源](cognitive-services-apis-create-account.md)
* [使用 Azure CLI 创建新资源](cognitive-services-apis-create-account-cli.md)
* [使用客户端库创建新资源](cognitive-services-apis-create-account-client-library.md)
* [使用 ARM 模板创建新资源](create-account-resource-manager-template.md)
