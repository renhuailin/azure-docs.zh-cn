---
title: 使用客户管理的密钥进行静态加密
titleSuffix: Azure Cognitive Search
description: 使用在 Azure Key Vault 中创建和管理的密钥，来补充 Azure 认知搜索中基于索引和同义词映射的服务器端加密。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 6b1079797f1a753fa8362d6e920f3394087d7e9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119282"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>在 Azure 认知搜索中配置客户管理的密钥以用于数据加密

Azure 认知搜索会自动使用[服务托管的密钥](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)对已编制索引的内容进行静态加密。 如果需要更多保护，可以使用在 Azure Key Vault 中创建和管理的密钥，通过一个额外的加密层来补充默认加密。 本文将指导你完成设置客户管理的密钥加密的步骤。

客户管理的密钥加密依赖于 [Azure Key Vault](../key-vault/general/overview.md)。 你可以创建自己的加密密钥并将其存储在 Key Vault 中，或使用 Azure Key Vault 的 API 来生成加密密钥。 使用 Azure Key Vault，还可以在[启用日志记录](../key-vault/general/logging.md)的情况下审核密钥使用情况。  

使用客户管理的密钥进行的加密是在创建单个索引或同义词映射时应用于这些对象的，而不是在搜索服务级别本身上指定的。 只有新对象才能加密。 无法加密已存在的内容。

密钥不需要全部位于同一密钥保管库中。 单个搜索服务可以托管多个已加密的索引或同义词映射（每个都使用其自己的已存储在不同密钥保管库中的客户管理加密密钥进行加密）。 还可以在同一服务中托管未使用客户管理的密钥加密的索引和同义词映射。

>[!Important]
> 如果实施客户管理的密钥，请确保在对密钥保管库密钥以及 Active Directory 应用程序机密进行例行轮换和注册期间遵循严格的过程。 在删除旧机密和密钥之前，始终更新所有加密内容以使用新机密和密钥。 如果缺少此步骤，你的内容将无法解密。

## <a name="double-encryption"></a>双重加密

对于 2020 年 8 月 1 日之后在特定区域创建的服务，客户管理的密钥加密的范围包括目前在以下区域提供的临时磁盘（实现[完全双重加密](search-security-overview.md#double-encryption)）： 

+ 美国西部 2
+ 美国东部
+ 美国中南部
+ US Gov 弗吉尼亚州
+ US Gov 亚利桑那州

如果使用的是其他区域或 8 月 1 日之前创建的服务，则托管密钥加密仅限于数据磁盘，不包括服务使用的临时磁盘。

## <a name="prerequisites"></a>先决条件

本方案中使用了以下工具和服务。

+ [可计费层](search-sku-tier.md#tier-descriptions)（任何区域中的“基本”层或更高层）上的 [Azure 认知搜索](search-create-service-portal.md)。
+ [Azure Key Vault](../key-vault/general/overview.md)，你可以使用 [Azure 门户](../key-vault//general/quick-create-portal.md)、[Azure CLI](../key-vault//general/quick-create-cli.md) 或 [Azure PowerShell](../key-vault//general/quick-create-powershell.md) 来创建密钥保管库。 与 Azure 认知搜索位于同一订阅中。 密钥保管库必须启用“软删除”和“清除保护”。 
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)。 如果没有，请[设置新租户](../active-directory/develop/quickstart-create-new-tenant.md)。

你应该有一个可创建加密的对象的搜索应用程序。 你需要将密钥保管库密钥和 Active Directory 注册信息引用到此代码中。 此代码可以是某个工作应用，也可以是原型代码，例如 [C# 代码示例 DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)。

> [!TIP]
> 可以使用 [Postman](search-get-started-rest.md) 或 [Visual Studio Code](search-get-started-vs-code.md) 或 [Azure PowerShell](./search-get-started-powershell.md) 来调用 REST API，以便创建包含加密密钥参数的索引和同义词映射。 目前，门户尚不支持将键添加到索引或同义词映射。

## <a name="1---enable-key-recovery"></a>1 - 启用密钥恢复

删除你的 Azure Key Vault 密钥后，无人可以检索你的数据，这是使用客户管理的密钥进行的加密的本质决定的。 若要防止意外删除 Key Vault 密钥造成数据丢失，必须在密钥保管库上启用“软删除”和“清除保护”。 默认情况下会启用“软删除”，因此，只有在你特意禁用了此功能时才会遇到问题。 “清除保护”在默认情况下未启用，但它是认知搜索中客户管理的密钥加密所必需的。 有关详细信息，请参阅[软删除](../key-vault/general/soft-delete-overview.md)和[清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)概述。

可以使用门户、PowerShell 或 Azure CLI 命令设置这两项属性。

### <a name="using-azure-portal"></a>使用 Azure 门户

1. [登录到 Azure 门户](https://portal.azure.com)并打开 Key Vault 概述页。

1. 在“概述”页的“概要”下，启用“软删除”和“清除保护”。   

### <a name="using-powershell"></a>使用 PowerShell

1. 运行 `Connect-AzAccount` 以设置你的 Azure 凭据。

1. 运行以下命令以连接到密钥保管库，并使用有效名称替换 `<vault_name>`：

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault 在创建时便启用了“软删除”。 如果在保管库上禁用了此功能，请运行以下命令：

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. 启用“清除保护”：

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. 保存更新：

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>使用 Azure CLI

+ 如果已[安装 Azure CLI](/cli/azure/install-azure-cli)，则可运行以下命令来启用所需的属性。

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 - 在 Key Vault 中创建密钥

如果你在 Azure Key Vault 中已经有一个密钥，请跳过此步骤。

1. [登录到 Azure 门户](https://portal.azure.com)并打开 Key Vault 概述页。

1. 选择左侧的“密钥”，然后选择“+ 生成/导入”。 

1. 在“创建密钥”窗格中，从“选项”列表中，选择要用于创建密钥的方法。 可以 **生成** 新密钥、**上传** 现有密钥，或使用“备份还原”选择密钥的备份。

1. 输入密钥的 **名称**，并根据需要选择其他密钥属性。

1. 选择“创建”以开始部署。

1. 请记下密钥标识符 - 它由 **密钥值 URI**、**密钥名称** 和 **密钥版本** 构成。 稍后需要使用该标识符在 Azure 认知搜索中定义加密的索引。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="创建新的 Key Vault 密钥":::

## <a name="3---register-an-app-in-active-directory"></a>3 - 在 Active Directory 中注册应用

1. 在 [Azure 门户](https://portal.azure.com)中，查找你的订阅的 Azure Active Directory 资源。

1. 在左侧的“管理”下选择“应用注册”，然后选择“新建注册”  。

1. 为注册提供一个名称，该名称可以是与搜索应用程序名称类似的名称。 选择“注册”。

1. 创建应用注册后，复制应用程序 ID。 需要将此字符串提供给应用程序。 

   如果要单步执行 [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)，请将以下值粘贴到 appsettings.json 文件中。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="“概要”部分的应用程序 ID":::

1. 接下来，在左侧选择“证书和机密”。

1. 选择“新建客户端机密”。 为机密指定显示名称并选择“添加”。

1. 复制应用程序密钥。 如果要单步执行此示例，请将以下值粘贴到 appsettings.json 文件中。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="应用程序密码":::

## <a name="4---grant-key-access-permissions"></a>4 - 授予密钥访问权限

此步骤将在 Key Vault 中创建一个访问策略。 此策略为已注册到 Active Directory 的应用程序提供使用客户管理的密钥的权限。

可在任意给定时间撤销访问权限。 撤销后，使用该 Key Vault 的任何搜索服务索引或同义词映射都将不可用。 以后还原 Key Vault 访问权限会还原索引/同义词映射访问权限。 有关详细信息，请参阅[保护对 Key Vault 的访问](../key-vault/general/secure-your-key-vault.md)。

1. 在 Azure 门户中打开密钥保管库“概述”页。 

1. 选择左侧的“访问策略”，然后选择“+ 添加访问策略”。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="添加新的 Key Vault 访问策略":::

1. 选择“选择主体”，然后选择注册到 Active Directory 的应用程序。 可以按名称进行搜索。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="选择 Key Vault 访问策略主体":::

1. 在“密钥权限”中选择“获取”、“解包密钥”和“包装密钥”。

1. 在“机密权限”中，选择“获取”。

1. 在“证书权限”中，选择“获取”。

1. 选择“添加”，然后选择“保存”。

> [!Important]
> Azure 认知搜索中已加密的内容配置为使用特定 **版本** 的特定 Azure Key Vault 密钥。 如果你更改密钥或版本，必须先将索引或同义词映射更新为使用新的密钥/版本，**然后** 删除以前的密钥/版本。 否则会使该索引或同义词映射变得不可用，因为在失去密钥访问权限后无法解密内容。

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 - 加密内容

若要在索引、数据源、技能组、索引器或同义词映射上添加客户管理的密钥，必须使用[搜索 REST API](/rest/api/searchservice/) 或 SDK。 门户不会公开同义词映射或加密属性。 使用有效的 API 时，索引、数据源、技能组、索引器和同义词映射支持顶级 encryptionKey 属性。

此示例使用 REST API，其中包含 Azure Key Vault 和 Azure Active Directory 的值：

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> 这些 Key Vault 详细信息都不被视为机密，在 Azure 门户中浏览到相关的 Azure Key Vault 密钥页即可轻松检索这些信息。

## <a name="example-index-encryption"></a>示例：索引加密

按照[创建索引（Azure 认知搜索 REST API）](/rest/api/searchservice/create-index)一文的说明创建加密的索引。 使用 `encryptionKey` 属性指定要使用的加密密钥。
> [!Note]
> 这些 Key Vault 详细信息都不被视为机密，在 Azure 门户中浏览到相关的 Azure Key Vault 密钥页即可轻松检索这些信息。

## <a name="rest-examples"></a>REST 示例

此部分展示已加密索引和同义词映射的完整 JSON

### <a name="index-encryption"></a>索引加密

[创建索引 (REST API)](/rest/api/searchservice/create-index) 中详述了如何通过 REST API 创建新索引。这里，唯一的差别在于需要将加密密钥详细信息指定为索引定义的一部分：

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

现在，可以发送索引创建请求，然后开始正常使用索引。

### <a name="synonym-map-encryption"></a>同义词映射加密

按照[创建同义词映射（Azure 认知搜索 REST API）](/rest/api/searchservice/create-synonym-map)一文的说明创建加密的同义词映射。 使用 `encryptionKey` 属性指定要使用的加密密钥。

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

现在，可以发送同义词映射创建请求，然后开始正常使用同义词映射。

## <a name="example-data-source-encryption"></a>示例：数据源加密

按照[创建数据源（Azure 认知搜索 REST API）](/rest/api/searchservice/create-data-source)一文的说明创建加密的数据源。 使用 `encryptionKey` 属性指定要使用的加密密钥。

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

现在，可以发送数据源创建请求，然后开始正常使用数据源。

## <a name="example-skillset-encryption"></a>示例：技能组加密

按照[创建技能组（Azure 认知搜索 REST API）](/rest/api/searchservice/create-skillset)一文的说明创建加密的技能组。 使用 `encryptionKey` 属性指定要使用的加密密钥。

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

现在，可以发送技能组创建请求，然后开始正常使用技能组。

## <a name="example-indexer-encryption"></a>示例：索引器加密

按照[创建索引器（Azure 认知搜索 REST API）](/rest/api/searchservice/create-indexer)一文的说明创建加密的索引器。 使用 `encryptionKey` 属性指定要使用的加密密钥。

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

现在，可以发送索引器创建请求，然后开始正常使用索引器。

>[!Important]
> 尽管无法将 `encryptionKey` 添加到现有的搜索索引或同义词映射，但可以通过为三项密钥保管库详细信息的任何一项提供不同的值，来更新该属性（例如，更新密钥版本）。 更改为新的 Key Vault 密钥或新的密钥版本时，必须先将使用该密钥的任何搜索索引或同义词映射更新为使用新的密钥/版本，然后删除以前的密钥/版本。 否则会使该索引或同义词映射变得不可用，因为在失去密钥访问权限后无法解密内容。 不过，以后还原 Key Vault 访问权限时会还原内容访问权限。

## <a name="simpler-alternative-trusted-service"></a>更简单的替代方法：受信服务

你也许能够实现一种更简单的访问密钥保管库密钥的方法，具体取决于租户配置和身份验证要求。 可以通过为搜索服务启用系统管理的标识（而不是创建并使用 Active Directory 应用程序），使搜索服务成为受信任的服务。 然后，你可以使用受信任的搜索服务作为安全主体（而不是使用 AD 注册的应用程序）来访问密钥保管库密钥。

此方法允许你省略用于应用程序注册和应用程序机密的步骤，将加密密钥定义简化成密钥保管库组件（URI、保管库名称、密钥版本）。

一般情况下，搜索服务可以使用托管标识向 Azure Key Vault 进行身份验证，而无需在代码中存储凭据（ApplicationID 或 ApplicationSecret）。 此类托管标识的生命周期与只包含一个托管标识的搜索服务的生命周期密切相关。 若要详细了解托管标识工作原理，请参阅[什么是 Azure 资源托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。

1. 使搜索服务成为受信任的服务。
   ![打开系统分配的托管标识](./media/search-managed-identities/turn-on-system-assigned-identity.png "打开系统分配的托管标识")

1. 在 Azure Key Vault 中设置访问策略时，请选择受信任的搜索服务作为主体（而不是选择 AD 注册的应用程序）。 按照“授予访问密钥权限”步骤中的说明，分配相同的权限（多个“获取”、“包装”、“解包”）。

1. 使用 `encryptionKey` 的简化构造，其中省略了 Active Directory 属性。

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

有一些情况会导致你无法采用这种简化方法，其中包括：

+ 无法直接授予搜索服务对 Key Vault 的访问权限（例如，如果搜索服务与 Azure Key Vault 位于不同的 Active Directory 租户中）。

+ 必须使用单个搜索服务来托管多个已加密的索引/同义词映射，其中每个索引/同义词映射使用不同 Key Vault 中的不同密钥，而每个 Key Vault 必须使用 **不同的标识** 进行身份验证。 由于一个搜索服务只能有一个托管标识，因此，如果需要多个标识，则无法对你的方案使用简化的方法。  

## <a name="work-with-encrypted-content"></a>使用加密内容

使用客户管理的密钥加密时，你会注意到，由于额外的加密/解密工作，索引编制和查询会出现相应的延迟。 Azure 认知搜索不记录加密活动，但你可以通过密钥保管库日志记录监视密钥访问。 建议在配置密钥保管库的过程中[启用日志记录](../key-vault/general/logging.md)。

应每隔一段时间就进行密钥轮换。 每当轮换密钥时，请务必遵循此顺序：

1. [确定索引或同义词映射所使用的密钥](search-security-get-encryption-keys.md)。
1. [在密钥保管库中创建新密钥](../key-vault/keys/quick-create-portal.md)，但保持原始密钥可用。
1. [更新索引或同义词映射上的 encryptionKey 属性](/rest/api/searchservice/update-index)以使用新值。 只有最初创建时带有此属性的对象才能更新为使用其他值。
1. 禁用或删除密钥保管库中的上一个密钥。 监视密钥访问以验证是否正在使用新密钥。

出于性能方面的原因，搜索服务最多会缓存此密钥几个小时。 如果在不提供新密钥的情况下禁用或删除该密钥，则查询将暂时性地继续工作，直到缓存过期。 但是，一旦搜索服务无法解密内容，你就会收到以下消息：“访问被禁止。 使用的查询键可能已被撤消 - 请重试。” 

## <a name="next-steps"></a>后续步骤

如果你不熟悉 Azure 安全体系结构，请查看 [Azure 安全文档](../security/index.yml)，具体而言，是以下文章：

> [!div class="nextstepaction"]
> [静态数据加密](../security/fundamentals/encryption-atrest.md)