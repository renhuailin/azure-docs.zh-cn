---
title: 查找加密密钥信息
titleSuffix: Azure Cognitive Search
description: 检索索引或同义词映射中使用的加密密钥名称和版本，以便可以在 Azure Key Vault 中管理密钥。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/21/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f34d653a698a7ef2ee3dee21d46345ed9a7301a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003811"
---
# <a name="find-encrypted-objects-and-information"></a>查找加密的对象和信息

在 Azure 认知搜索中，需在 Azure Key Vault 中创建、存储和管理客户管理的加密密钥。 如需确定对象是否经过加密或者 Azure Key Vault 中使用的密钥名称或版本，可以使用 REST API 或 Azure SDK 从搜索服务的对象定义中检索 encryptionKey 属性。

未使用客户管理的密钥加密的对象将具有空的 encryptionKey 属性。 否则，你可能会看到类似于以下示例的定义。

```json
"encryptionKey": {
"keyVaultUri": "https://demokeyvault.vault.azure.net",
"keyVaultKeyName": "myEncryptionKey",
"keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
"accessCredentials": {
    "applicationId": "00000000-0000-0000-0000-000000000000",
    "applicationSecret": "myApplicationSecret"
    }
}
```

所有加密对象的 encryptionKey 构造都是相同的。 这是第一级属性，与对象名称和说明属于同一级别。

## <a name="get-the-admin-api-key"></a>获取管理 API 密钥

你需要先提供管理员 API 密钥，才能从搜索服务中检索对象定义。 查询对象定义和元数据的请求需要用到管理员 API 密钥。 获取管理 API 密钥的最简单的方法是通过门户获取。

1. 登录到 [Azure 门户](https://portal.azure.com/)，然后打开搜索服务概览页面。

1. 在左侧，单击“密钥”并复制管理 API。 索引和同义词映射检索需要使用管理密钥。

为完成剩余步骤，请切换到 PowerShell 和 REST API。 门户不会显示任何对象的加密密钥信息。

## <a name="retrieve-object-properties"></a>检索对象属性

使用 PowerShell 和 REST 运行以下命令来设置变量及获取对象定义。 

或者，你也可以使用适用于 [.NET](/dotnet/api/azure.search.documents.indexes.searchindexclient.getindexes)、[Python](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient)、[JavaScript](/javascript/api/@azure/search-documents/searchindexclient) 和 [Java](/java/api/com.azure.search.documents.indexes.searchindexclient.getindex) 的 Azure SDK。

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>后续步骤

建议在 Azure Key Vault 上[启用日志记录](../key-vault/general/logging.md)，以便监视密钥的使用情况。

若要详细了解如何使用 Azure 密钥或配置客户托管的加密：

+ [快速入门：使用 PowerShell 在 Azure Key Vault 中设置和检索机密](../key-vault/secrets/quick-create-powershell.md)

+ [在 Azure 认知服务中配置客户管理的密钥以进行数据加密](search-security-manage-encryption-keys.md)
