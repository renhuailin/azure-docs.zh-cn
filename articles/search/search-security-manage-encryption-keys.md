---
title: 使用客户管理的密钥加密数据
titleSuffix: Azure Cognitive Search
description: 使用在 Azure Key Vault 中创建和管理的密钥，来补充 Azure 认知搜索中基于索引和同义词映射的服务器端加密。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 5513e4f26f6b2fed17f406f43110eb358179ce79
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232819"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>在 Azure 认知搜索中配置客户管理的密钥以用于数据加密

Azure 认知搜索自动使用[服务管理的密钥](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)加密内容。 如果需要更多保护，可以使用在 Azure Key Vault 中创建和管理的密钥，通过一个额外的加密层来补充默认加密。 可加密的对象包括索引、同义词列表、索引器、数据源和技能组。

本文将指导你完成设置客户管理的密钥加密的步骤。 下面是要记住的一些要点：

+ 客户管理的密钥加密依赖于 [Azure 密钥保管库](../key-vault/general/overview.md)。 你可以创建自己的加密密钥并将其存储在 Key Vault 中，或使用 Azure Key Vault 的 API 来生成加密密钥。

+ 创建对象时，将会基于每个对象启用客户管理的密钥加密。 无法加密已存在的内容。

加密的计算开销高于解密，因此只会加密敏感内容。 这包括索引和同义词列表中的所有内容。 对于索引器、数据源和技能组，只会加密存储了连接字符串、说明、密钥和用户输入的字段。 例如，技能组有认知服务密钥，而某些技能接受用户输入（例如自定义实体）。 技能中的密钥和用户输入将会加密。

## <a name="double-encryption"></a>双重加密

双重加密是客户管理的密钥 (CMK) 加密的扩展。 CMK 加密适用于写入到数据磁盘的长期存储。 术语“双重加密”是指对（写入到临时磁盘的内容的）短期存储进行额外加密。 无需任何配置。 将 CMK 应用于对象时，会自动调用双重加密。

虽然双重加密在所有区域都可用，但支持分两个阶段推出。 第一次推出是在 2020 年 8 月，包括下面列出的五个区域。 第二次推出是在 2021 年 5 月，将双重加密扩展到所有剩余区域。 如果在旧版本的服务上使用 CMK 并希望进行双重加密，则需要在选择的区域中创建新的搜索服务。

| 区域 | 服务创建日期 |
|--------|-----------------------|
| 美国西部 2 | 2020 年 8 月 1 日之后 |
| 美国东部 | 2020 年 8 月 1 日之后 |
| 美国中南部  | 2020 年 8 月 1 日之后 |
| US Gov 弗吉尼亚州  | 2020 年 8 月 1 日之后 |
| US Gov 亚利桑那州  | 2020 年 8 月 1 日之后 |
| [所有其他受支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=search#select-product) | 2021 年 5 月 13 日之后 |

## <a name="prerequisites"></a>先决条件

本方案中使用了以下工具和服务。

+ [可计费层](search-sku-tier.md#tier-descriptions)（任何区域中的“基本”层或更高层）上的 [Azure 认知搜索](search-create-service-portal.md)。
+ [Azure Key Vault](../key-vault/general/overview.md)，你可以使用 [Azure 门户](../key-vault//general/quick-create-portal.md)、[Azure CLI](../key-vault//general/quick-create-cli.md) 或 [Azure PowerShell](../key-vault//general/quick-create-powershell.md) 来创建密钥保管库。 与 Azure 认知搜索位于同一订阅中。 密钥保管库必须启用“软删除”和“清除保护”。 
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)。 如果没有，请[设置新租户](../active-directory/develop/quickstart-create-new-tenant.md)。

你应该有一个可创建加密的对象的搜索应用程序。 你需要将密钥保管库密钥和 Active Directory 注册信息引用到此代码中。 此代码可以是某个工作应用，也可以是原型代码，例如 [C# 代码示例 DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)。

> [!TIP]
> 可以使用 [Postman](search-get-started-rest.md) 或 [Visual Studio Code](search-get-started-vs-code.md) 或 [Azure PowerShell](search-get-started-powershell.md) 来调用 REST API，以便创建包含加密密钥参数的索引和同义词映射。 也可以使用 Azure SDK。 不支持在门户中将密钥添加到索引或同义词映射。

## <a name="key-vault-tips"></a>密钥保管库提示

如果你不熟悉 Azure 密钥保管库，请查看以下快速入门来了解基本任务：[使用 PowerShell 在 Azure 密钥保管库中设置和检索机密](../key-vault/secrets/quick-create-powershell.md)。 下面是有关使用密钥保管库的一些提示：

+ 使用所需数量的密钥保管库。 管理的密钥可以位于不同的密钥保管库中。 一个搜索服务可有多个已加密的对象，每个对象通过不同密钥保管库中存储的不同客户管理的加密密钥进行加密。

+ 在密钥保管库上[启用日志记录](../key-vault/general/logging.md)，以便可以监视密钥使用情况。

+ 请记得在密钥保管库密钥和 Active Directory 应用程序机密的例行轮换期间以及注册期间遵循严格的过程。 在删除旧机密和密钥之前，始终更新所有[已加密内容](search-security-get-encryption-keys.md)以使用新机密和密钥。 如果缺少此步骤，你的内容将无法解密。

## <a name="1---enable-purge-protection"></a>1- 启用清除保护

首先，请确保在密钥保管库上启用[软删除](../key-vault/general/soft-delete-overview.md)和[清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)。 删除你的 Azure Key Vault 密钥后，无人可以检索你的数据，这是使用客户管理的密钥进行的加密的本质决定的。 

若要防止意外删除 Key Vault 密钥造成数据丢失，必须在密钥保管库上启用“软删除”和“清除保护”。 默认情况下会启用“软删除”，因此，只有在你特意禁用了此功能时才会遇到问题。 “清除保护”在默认情况下未启用，但它是认知搜索中客户管理的密钥加密所必需的。 

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

如果 Azure 密钥保管库中已包含你要使用的密钥，请跳过密钥生成步骤，但要收集密钥标识符。 创建加密的对象时需要此信息。

1. [登录到 Azure 门户](https://portal.azure.com)并打开 Key Vault 概述页。

1. 选择左侧的“密钥”，然后选择“+ 生成/导入”。 

1. 在“创建密钥”窗格中，从“选项”列表中，选择要用于创建密钥的方法。 可以 **生成** 新密钥、**上传** 现有密钥，或使用“备份还原”选择密钥的备份。

1. 输入密钥的 **名称**，并根据需要选择其他密钥属性。

1. 选择“创建”以开始部署。

1. 请记下密钥标识符 - 它由 **密钥值 URI**、**密钥名称** 和 **密钥版本** 构成。 稍后需要使用该标识符在 Azure 认知搜索中定义加密的索引。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="创建新的 Key Vault 密钥":::

## <a name="3---register-an-app"></a>3 - 注册应用

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

## <a name="4---grant-permissions"></a>4 - 授予权限

此步骤将在 Key Vault 中创建一个访问策略。 此策略为已注册到 Active Directory 的应用程序提供使用客户管理的密钥的权限。

可在任意给定时间撤销访问权限。 撤销后，使用该 Key Vault 的任何搜索服务索引或同义词映射都将不可用。 以后还原 Key Vault 访问权限会还原索引/同义词映射访问权限。 有关详细信息，请参阅[保护对 Key Vault 的访问](../key-vault/general/security-features.md)。

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

若要在索引、同义词映射、索引器、数据源或技能组上添加客户管理的密钥，请使用[搜索 REST API](/rest/api/searchservice/) 或 Azure SDK 创建已启用加密的对象。 门户不会公开同义词映射或加密属性。 

1. 调用“创建”API 以指定 encryptionKey 属性：

   + [创建索引](/rest/api/searchservice/create-index)
   + [创建同义词映射](/rest/api/searchservice/create-synonym-map)
   + [创建索引器](/rest/api/searchservice/create-indexer)
   + [创建数据源](/rest/api/searchservice/create-data-source)
   + [创建技能组](/rest/api/searchservice/create-skillset)。

1. 将 encryptionKey 构造插入到对象定义中。 此属性是一级属性，其级别与名称和说明相同。 [下面的示例](#rest-examples)显示了属性放置情况。 如果使用相同的密钥保管库、密钥和版本，可将相同的 encryptionKey 构造粘贴到要启用加密的每个对象中。

   以下 JSON 示例显示了 encryptionKey，以及 Azure Active Directory 中 Azure 密钥保管库和应用程序注册的占位符值：

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

在搜索服务上创建加密的对象后，可以像使用其他任何相同类型的对象一样使用它。 加密对于用户和开发人员是透明的。

> [!Note]
> 这些 Key Vault 详细信息都不被视为机密，在 Azure 门户中浏览到相关的 Azure Key Vault 密钥页即可轻松检索这些信息。

## <a name="rest-examples"></a>REST 示例

本部分显示了多个对象的 JSON，使你知道要在对象定义中的哪个位置查找 `encryptionKey`。

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
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true}
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

### <a name="data-source-encryption"></a>数据源加密

使用[创建数据源 (REST API)](/rest/api/searchservice/create-data-source) 创建加密的数据源。 使用 `encryptionKey` 属性指定要使用的加密密钥。

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

### <a name="skillset-encryption"></a>技能组加密

使用[创建技能组 REST API](/rest/api/searchservice/create-skillset) 创建加密的技能组。 使用 `encryptionKey` 属性指定要使用的加密密钥。

```json
{
    "name": "skillset1",
    "skills":  [ omitted for brevity ],
    "cognitiveServices": { omitted for brevity },
      "knowledgeStore":  { omitted for brevity  },
    "encryptionKey": (optional) { 
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "accessCredentials": {
            "applicationId": "00000000-0000-0000-0000-000000000000",
            "applicationSecret": "myApplicationSecret"}
    }
}
```

现在，可以发送技能组创建请求，然后开始正常使用技能组。

### <a name="indexer-encryption"></a>索引器加密

使用[创建索引器 REST API](/rest/api/searchservice/create-indexer) 创建加密的索引器。 使用 `encryptionKey` 属性指定要使用的加密密钥。

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

你也许能够实现一种更简单的访问密钥保管库密钥的方法，具体取决于租户配置和身份验证要求。 如果不创建并使用 Active Directory 应用程序，可以通过为搜索服务启用系统管理的标识来使搜索服务成为受信任的服务，或者将用户分配的托管标识分配给搜索服务。 然后，使用受信任的搜索服务或用户分配的托管标识作为安全主体（而不是使用 AD 注册的应用程序）来访问密钥保管库密钥。

这两种方法都让你可以省略应用程序注册和应用程序机密的步骤，并简化加密密钥定义。

### <a name="system-assigned-managed-identity"></a>系统分配的托管标识

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

### <a name="user-assigned-managed-identity-preview"></a>用户分配的托管标识（预览版）

> [!IMPORTANT] 
> 用户分配的托管标识支持目前以公共预览版提供，使用它需要遵守[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 
> REST API 版本 2021-04-30-Preview 和[管理 REST API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) 提供此功能。

将用户分配的托管标识分配给搜索服务可使搜索服务能够向 Azure 密钥保管库进行身份验证，而无需在代码中存储凭据（ApplicationID 或 ApplicationSecret）。 此类托管标识的生命周期与搜索服务的生命周期无关。 若要详细了解托管标识工作原理，请参阅[什么是 Azure 资源托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。

1. 如果尚未创建用户分配的托管标识，则需要创建一个。 若要创建用户分配的托管标识，请执行以下步骤：

    1. 登录 [Azure 门户](https://portal.azure.com/)。
    1. 选择“+ 创建新资源”。
    1. 在“搜索服务和市场”搜索栏中，搜索“用户分配的托管标识”，然后选择“创建”。
    1. 为标识提供描述性名称。

1. 接下来，将用户分配的托管标识分配给搜索服务。 可以使用 [2021-04-01-preview](/rest/api/searchmanagement/management-api-versions) 管理 API 完成此操作。

    标识属性采用一个类型以及一个或多个完全限定的用户分配标识：
    
    * type 是用于资源的标识类型。 类型“SystemAssigned, UserAssigned”包含系统创建的一个标识，以及用户分配的一组标识。 类型“None”将删除服务中的所有标识。
    * userAssignedIdentities 包含用户分配的托管标识的详细信息。
        * 用户分配的托管标识的格式： 
            * /subscriptions/订阅 ID/resourcegroups/资源组名称/providers/Microsoft.ManagedIdentity/userAssignedIdentities/托管标识名称  
    
    关于如何将用户分配的托管标识分配给搜索服务的示例：
    
    ```http
    PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
    Content-Type: application/json

    {
      "location": "[region]",
      "sku": {
        "name": "[sku]"
      },
      "properties": {
        "replicaCount": [replica count],
        "partitionCount": [partition count],
        "hostingMode": "default"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]": {}
        }
      }
    } 
    ```

1. 在 Azure 密钥保管库中设置访问策略时，请选择用户分配的托管标识作为主体（而不要选择 AD 注册的应用程序）。 按照“授予访问密钥权限”步骤中的说明，分配相同的权限（多个“获取”、“包装”、“解包”）。

1. 使用省略了 Active Directory 属性的 `encryptionKey` 的简化构造，并添加标识属性。 确保使用 2021-04-30-preview REST API 版本。

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://[key vault name].vault.azure.net",
        "keyVaultKeyName": "[key vault key name]",
        "keyVaultKeyVersion": "[key vault key version]",
        "identity" : { 
            "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
            "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
        }
      }
    }
    ```

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
