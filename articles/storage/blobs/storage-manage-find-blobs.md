---
title: " (预览) ，管理和查找包含 Blob 索引标记的 Azure Blob 数据"
description: 了解如何使用 blob 索引标记来分类、管理和查询 blob 对象。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/19/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 4f84c3c2f6fc671a8cb6ac70313361540e3dd815
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523274"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a> (预览) ，管理和查找包含 Blob 索引标记的 Azure Blob 数据

数据集变得更大时，在数据的汪洋大海中查找特定对象可能会很困难。 Blob 索引标记通过使用键-值索引标记特性提供数据管理和发现功能。 你可以分类和查找单个容器中的对象，也可以在存储帐户中的所有容器中查找对象。 随着数据需求的变化，可以通过更新索引标记来动态分类对象。 对象可以与当前容器组织保持原样。

Blob 索引标记允许：

- 使用键/值索引标记对 blob 进行动态分类
- 在整个存储帐户中快速查找特定标记 blob
- 基于索引标记的计算指定 blob Api 的条件行为
- 将索引标记用于功能上的高级控件，如 [blob 生命周期管理](storage-lifecycle-management-concepts.md)

考虑这样一种情况：存储帐户中有数百万个 blob，由许多不同的应用程序访问。 要从单个项目中查找所有相关数据。 你不能确定数据在范围内，因为数据可跨多个容器，具有不同的命名约定。 但是，应用程序会基于其项目上传包含标记的所有数据。 你可以使用作为发现条件，而不是搜索数百万个 blob 和比较名称和属性 `Project = Contoso` 。 Blob 索引将筛选整个存储帐户中的所有容器，以便快速查找并只返回来自的 50 blob 集 `Project = Contoso` 。

若要开始使用 blob 索引的示例，请参阅 [使用 blob 索引标记来管理和查找数据](storage-blob-index-how-to.md)。

## <a name="blob-index-tags-and-data-management"></a>Blob 索引标记和数据管理

容器和 blob 名称前缀为一维分类。 Blob 索引标记允许 [ (块、追加或页) 的 blob 数据类型 ](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)进行多维分类。 多维分类由 Azure Blob 存储本机建立索引，以便您可以快速查找数据。

请考虑存储帐户中的以下五个 blob：

- *container1/transaction.csv*
- *container2/campaign.docx*
- *照片/bannerphoto.png*
- *存档/完成/2019review.pdf*
- *日志/2020/01/01/logfile.txt*


这些 blob 使用 *容器/虚拟文件夹/blob 名称* 的前缀进行分隔。 你可以将 `Project = Contoso` 这五个 blob 上的索引标记属性设置为在一起，同时保留其当前的前缀组织。 添加索引标记后，无需通过公开使用索引筛选和查找数据的功能来移动数据。

## <a name="setting-blob-index-tags"></a>设置 blob 索引标记

Blob 索引标记是可应用于存储帐户中的新对象或现有对象的键-值属性。 你可以使用 [Put blob](/rest/api/storageservices/put-blob)、 [put 块列表](/rest/api/storageservices/put-block-list)或 [复制 Blob](/rest/api/storageservices/copy-blob) 操作和可选标头在上载过程中指定索引标记 `x-ms-tags` 。 如果你的存储帐户中已有 blob，请调用 [Set Blob 标记，将](/rest/api/storageservices/set-blob-tags) 带格式的 XML 文档传递到请求正文中的索引标记。

> [!IMPORTANT]
> 设置 blob 索引标记可以由 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 以及具有共享访问签名的任何人执行，该共享访问签名有权访问 blob 的标记， (`t` SAS 权限) 。
>
> 此外，具有权限的 RBAC 用户 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 可以执行此操作。

你可以在 blob 上应用单个标记来描述你的数据处理完成的时间。

> "processedDate" = "2020-01-01"

可以在 blob 上应用多个标记，以便更好地说明数据。

> "项目" = "Contoso"  
> "已分类" = "True"  
> "状态" = "未处理"  
> "Priority" = "01"

若要修改现有的索引标记特性，请检索现有标记特性，修改标记特性，并将替换为 " [设置 Blob 标记](/rest/api/storageservices/set-blob-tags) " 操作。 若要从 blob 中删除所有索引标记，请调用 `Set Blob Tags` 未指定标记特性的操作。 Blob 索引标记是 blob 数据内容的 subresource， `Set Blob Tags` 不会修改任何基础内容，也不会更改 blob 的上次修改时间或 eTag。 您可以为所有当前的基本 blob 和以前的版本创建或修改索引标记。 但是，不能修改快照或软删除 blob 上的标记。

以下限制适用于 blob 索引标记：

- 每个 blob 最多可以有10个 blob 索引标记
- 标记键的长度必须介于1到128个字符之间
- 标记值必须介于0到256个字符之间
- 标记键和值区分大小写
- 标记键和值仅支持字符串数据类型。 任何数字、日期、时间或特殊字符均保存为字符串
- 标记键和值必须符合以下命名规则：
  - 字母数字字符：
    - **a** 至 **z**（小写字母）
    - **A** 至 **Z**（大写字母）
    - **0** 至 **9**（数字）
  - 有效的特殊字符：空格、加、减、句点、冒号、等于、下划线、正斜杠 (` +-.:=_/`) 

## <a name="getting-and-listing-blob-index-tags"></a>获取和列出 blob 索引标记

Blob 索引标记作为 subresource 与 blob 数据一起存储，可独立于基础 blob 数据内容进行检索。 可以使用 [获取 Blob 标记](/rest/api/storageservices/get-blob-tags) 操作检索单个 blob 的 blob 索引标记。 带参数的 [列表 blob](/rest/api/storageservices/list-blobs) 操作 `include:tags` 还会返回容器中的所有 blob 及其 blob 索引标记。

> [!IMPORTANT]
> 获取和列出 blob 索引标记可以由 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 以及具有共享访问签名的任何人执行，该共享访问签名有权访问 blob 的标记， (`t` SAS 权限) 。
>
> 此外，具有权限的 RBAC 用户 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 可以执行此操作。

对于至少具有一个 blob 索引标记的任何 blob，将 `x-ms-tag-count` 在 [列表 blob](/rest/api/storageservices/list-blobs)、 [获取 Blob](/rest/api/storageservices/get-blob)和 [获取 blob 属性](/rest/api/storageservices/get-blob-properties) 操作中返回，用于指示 blob 上索引标记的计数。

## <a name="finding-data-using-blob-index-tags"></a>使用 blob 索引标记查找数据

索引引擎将键-值特性公开为多维索引。 设置索引标记后，它们存在于 blob 上，可以立即检索。 可能需要一段时间才能更新 blob 索引。 更新 blob 索引后，可以使用 Blob 存储提供的本机查询和发现功能。

[通过 "通过标记查找 blob](/rest/api/storageservices/find-blobs-by-tags) " 操作，你可以获取一组经过筛选的 blob，其索引标记与给定的查询表达式匹配。 `Find Blobs by Tags` 支持在存储帐户中的所有容器之间进行筛选，也可将筛选范围限定为单个容器。 由于所有索引标记键和值都是字符串，因此关系运算符使用字典排序。

> [!IMPORTANT]
> 使用 blob 索引标记查找数据的方式可由 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 以及具有共享访问签名的任何人执行，该共享访问签名有权按标记查找 blob (`f` SAS 权限) 。
>
> 此外，具有权限的 RBAC 用户 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` 可以执行此操作。

以下条件适用于 blob 索引筛选：

- 标记键应括在双引号中 ( ") 
- 标记值和容器名称应括在单引号中 ( ") 
- 仅允许在特定容器名称上进行筛选的 @ 字符 (例如， `@container = 'ContainerName'`) 
- 筛选器应用于字符串的字典排序
- 同一键上的相同面范围操作 (无效，例如 `"Rank" > '10' AND "Rank" >= '15'`) 
- 使用 REST 创建筛选器表达式时，应对字符进行 URI 编码

下表显示了的所有有效运算符 `Find Blobs by Tags` ：

|  操作员  |  说明  | 示例 |
|------------|---------------|---------|
|     =      |     Equal     | `"Status" = 'In Progress'` |
|     >      |  大于 | `"Date" > '2018-06-18'` |
|     >=     |  大于或等于 | `"Priority" >= '5'` |
|     <      |  小于   | `"Age" < '32'` |
|     <=     |  小于或等于  | `"Company" <= 'Contoso'` |
|    AND     |  逻辑与  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | 作用域到特定容器 | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> 对标记设置和查询时，请熟悉按字典排序。
>
> - 数字排在字母之前。 数字根据第一个数字进行排序。
> - 大写字母排在小写字母之前。
> - 符号不是标准符号。 某些符号在数值之前进行排序。 其他符号在字母之前或之后排序。

## <a name="conditional-blob-operations-with-blob-index-tags"></a>带有 blob 索引标记的条件 blob 操作

在 REST 版本2019-10-10 及更高版本中，大多数 [blob 服务 api](/rest/api/storageservices/operations-on-blobs) 现在支持条件标头， `x-ms-if-tags` 因此，仅当满足指定的 blob 索引条件时，操作才会成功。 如果不满足条件，则会获得 `error 412: The condition specified using HTTP conditional header(s) is not met` 。

`x-ms-if-tags`标头可以与其他现有 HTTP 条件标头组合在一起 (if-match、if-match，等等) 。 如果请求中提供了多个条件标头，则它们都必须评估为 true，操作才能成功。 所有条件标头都与逻辑 AND 组合在一起。

下表显示了条件运算的有效运算符：

|  操作员  |  说明  | 示例 |
|------------|---------------|---------|
|     =      |     等于     | `"Status" = 'In Progress'` |
|     <>     |   不等于   | `"Status" <> 'Done'` |
|     >      |  大于 | `"Date" > '2018-06-18'` |
|     >=     |  大于或等于 | `"Priority" >= '5'` |
|     <      |  小于   | `"Age" < '32'` |
|     <=     |  小于或等于  | `"Company" <= 'Contoso'` |
|    AND     |  逻辑与  | `"Rank" >= '010' AND "Rank" < '100'` |
|     OR     | 逻辑或   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> 在 blob 操作的条件标头中，有两个附加的运算符（不等于和逻辑或）允许， `x-ms-if-tags` 但操作中不存在 `Find Blobs by Tags` 。

## <a name="platform-integrations-with-blob-index-tags"></a>具有 blob 索引标记的平台集成

Blob 索引标记不仅有助于对 blob 数据进行分类、管理和搜索，还可提供与其他 Blob 存储功能（如 [生命周期管理](storage-lifecycle-management-concepts.md)）的集成。

### <a name="lifecycle-management"></a>生命周期管理

使用 `blobIndexMatch` 作为生命周期管理中的规则筛选器，可将数据移到冷层，或基于应用于 blob 的索引标记删除数据。 你可以在规则中更详细地了解规则，并且仅当 blob 与指定的标记条件匹配时才移动或删除它们。

您可以将 blob 索引匹配设置为生命周期规则中的独立筛选器集，以便对标记的数据应用操作。 也可以组合使用前缀和 blob 索引来匹配更具体的数据集。 在生命周期规则中指定多个筛选器将应用逻辑 AND 运算。 仅当 *所有* 筛选条件匹配时，此操作才适用。

以下示例生命周期管理规则适用于名为 *videofiles* 的容器中的块 blob。 仅当数据与的 blob 索引标记条件匹配时，才将规则层用于存档存储 `"Status" == 'Processed' AND "Source" == 'RAW'` 。

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Azure 门户中的生命周期管理的 Blob 索引匹配规则示例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>权限和授权

您可以使用以下方法之一来授予对 blob 索引标记的访问权限：

- 使用 azure RBAC)  (azure 基于角色的访问控制授予 Azure Active Directory (Azure AD) 安全主体的权限。 使用 Azure AD 以实现优异的安全性和易用性。 有关将 Azure AD 与 blob 操作一起使用的详细信息，请参阅 [使用 Azure Active Directory 授予对 blob 和队列的访问权限](../common/storage-auth-aad.md)。
- 使用共享访问签名 (SAS) 委托对 blob 索引的访问。 有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。
- 使用帐户访问密钥向使用共享密钥的操作授权。 有关详细信息，请参阅[通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key)。

Blob 索引标记是 blob 数据的 subresource。 具有权限的用户或用于读取或写入 blob 的 SAS 令牌可能无权访问 blob 索引标记。

### <a name="role-based-access-control"></a>基于角色的访问控制

可以向使用 [Azure AD 标识](../common/storage-auth-aad.md) 的调用方授予对 blob 索引标记进行操作的以下权限。

| Blob 索引标记操作                                          | Azure RBAC 操作                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [设置 Blob 标记](/rest/api/storageservices/set-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write    |
| [获取 Blob 标记](/rest/api/storageservices/get-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read     |
| [按标记查找 Blob](/rest/api/storageservices/find-blobs-by-tags) | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action |

索引标记操作需要额外的权限，与基础 blob 数据分离。 为所有三个 Blob 索引标记操作授予 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 角色的权限。 [存储 Blob 数据读取器](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)仅为 `Find Blobs by Tags` 和操作授予权限 `Get Blob Tags` 。

### <a name="sas-permissions"></a>SAS 权限

使用 [共享访问签名 (SAS) ](../common/storage-sas-overview.md) 的调用方可被授予作用域内的权限，以便对 blob 索引标记进行操作。

#### <a name="blob-sas"></a>Blob SAS

可以在 blob SAS 中授予以下权限以允许访问 blob 索引标记。 仅 Blob 读取和写入权限不足以允许读取或写入其索引标记。

| 权限 | URI 符号 | 允许的操作                |
|------------|------------|-----------------------------------|
| 索引标记 |     T      | 获取和设置 blob 的索引标记 |

#### <a name="container-sas"></a>容器 SAS

可以在容器 SAS 中授予下列权限，以允许对 blob 标记进行筛选。 `Blob List`权限不足，无法按索引标记允许筛选 blob。

| 权限 | URI 符号 | 允许的操作         |
|------------|------------|----------------------------|
| 索引标记 |     F      | 查找带有索引标记的 blob |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>在元数据和 blob 索引标记之间选择

Blob 索引标记和元数据都可以将任意用户定义的键/值属性与 blob 资源一起存储。 两者都可以直接检索和设置，而无需返回或更改 blob 的内容。 可以使用元数据和索引标记。

只有索引标记会自动编制索引，并由本机 Blob 存储服务进行搜索。 无法对元数据进行本机索引或搜索。 必须使用单独的服务，如 [Azure 搜索](../../search/search-blob-ai-integration.md)。 Blob 索引标记具有额外的读取、筛选和写入权限，与基础 blob 数据分离。 元数据使用与 blob 相同的权限，并按 " [获取 blob](/rest/api/storageservices/get-blob) " 和 " [获取 blob 属性](/rest/api/storageservices/get-blob-properties) " 操作返回为 HTTP 标头。 Blob 索引标记使用 [Microsoft 托管的密钥](../common/storage-service-encryption.md)进行静态加密。 使用为 blob 数据指定的同一加密密钥在静态上加密元数据。

下表总结了 metadata 和 blob 索引标记之间的差异：

|              |   元数据   |   Blob 索引标记  |
|--------------|--------------|--------------------|
| **限制**      | 无数值限制，总共 8 KB，不区分大小写 | 每个 blob 最大10个标记，每个标记768字节，区分大小写 |
| **更新**    | 在存档层上不允许，将 `Set Blob Metadata` 替换所有现有元数据，并 `Set Blob Metadata` 更改 blob 的上次修改时间 | 允许用于所有访问层， `Set Blob Tags` 替换所有现有标记， `Set Blob Tags` 不会更改 blob 的上次修改时间 |
| **存储**     | 与 blob 数据存储在一起 | Blob 数据的 Subresource |
| **索引 & 查询** | 必须使用单独的服务，如 Azure 搜索 | 索引和查询构建在 Blob 存储中的功能 |
| **加密** | 使用与 blob 数据相同的加密密钥加密的静态 | 使用 Microsoft 托管的加密密钥进行静态加密 |
| **定价** | 元数据的大小包含在 blob 的存储成本中 | 固定成本/索引标记 |
| **标头响应** | 作为和中的标头返回的元数据 `Get Blob``Get Blob Properties` | 标记计数由 `Get Blob` 或返回 `Get Blob Properties` ，标记仅由 `Get Blob Tags` 和返回 `List Blobs` |
| **权限**  | 对 blob 数据的读取或写入权限扩展到元数据 | 读取、筛选或写入索引标记需要其他权限 |
| **命名** | 元数据名称必须遵循 c # 标识符的命名规则 | Blob 索引标记支持的字母数字字符范围更广 |

## <a name="pricing"></a>定价

Blob 索引定价以公共预览版提供，并随时更改公开上市。 需要为存储帐户中每月的索引标记数付费。 索引引擎不会产生任何费用。 对 `Set Blob Tags` 、 `Get Blob Tags` 和的请求 `Find Blobs by Tags` 根据其各自的操作类型进行计费。 有关 [详细信息，请参阅阻止 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability-and-storage-account-support"></a>区域可用性和存储帐户支持

Blob 索引标记仅在常规用途 v2 (GPv2) 具有分层命名空间 (HNS) 禁用的帐户上可用。 常规用途不支持 (GPV1) 帐户，但你可以将任何 GPv1 帐户升级到 GPv2 帐户。

高级存储帐户不支持索引标记。 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

在公共预览版中，blob 索引标记仅在以下区域提供：

- 加拿大中部
- 加拿大东部
- 法国中部
- 法国南部

若要开始，请参阅 [使用 blob 索引标记来管理和查找数据](storage-blob-index-how-to.md)。

> [!IMPORTANT]
> 必须先注册订阅，然后才能在存储帐户上使用 blob 索引预览。 请参阅本文的 " [条件" 和 "已知问题](#conditions-and-known-issues) " 部分。

### <a name="register-your-subscription-preview"></a> (预览注册订阅) 

由于 blob 索引标记仅用于公共预览，因此你需要先注册你的订阅，然后才能使用该功能。 若要提交请求，请运行以下 PowerShell 或 CLI 命令。

#### <a name="register-by-using-powershell"></a>使用 PowerShell 注册

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>使用 Azure CLI 注册

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>条件和已知问题

本部分介绍 blob 索引标记公共预览版中的已知问题和条件。 此功能不应用于生产工作负荷，直到它达到公开上市 (GA) ，因为行为可能会发生变化。

- 对于预览版，你必须先注册你的订阅，然后才能将 blob 索引用于预览区域中的存储帐户。
- 预览版仅支持 GPv2 帐户。 不支持 Blob、BlockBlobStorage 和 HNS 启用的 DataLake Gen2 帐户。 不支持 GPv1 帐户。
- 上载包含索引标记的页 blob 不会保留标记。 上载页 blob 后设置标记。
- 当筛选的作用域限定为单个容器时， `@container` 只有在筛选表达式中的所有索引标记都是相等检查 (key = value) 时，才能传递。
- 将 range 运算符与条件一起使用时 `AND` ，只能指定同一索引标记键名称 (`"Age" > '013' AND "Age" < '100'`) 。
- 版本控制和 blob 索引不受支持。 Blob 索引标记保留用于版本，但不会传递到 blob 索引引擎。
- 没有用于确定索引标记是否已编制索引的 API。
- 不支持帐户故障转移。 Blob 索引在故障转移后可能不会正确更新。
- 生命周期管理仅支持具有 blob 索引匹配的相等性检查。
- `Copy Blob` 不会将 blob 索引标记从源 blob 复制到新的目标 blob。 你可以指定要在复制操作过程中应用于目标 blob 的标记。
- `Copy Blob` 在目标 blob 上应用了标记的另一个存储帐户中 (Async copy) 会导致 blob 索引引擎在筛选器集中返回 blob 及其标记。 `Copy Blob`从 URL (同步副本) 使用。
- 创建快照时，将保留标记。 但是，升级快照不受支持，并且可能导致空标记集。

## <a name="faq"></a>常见问题

**Blob 索引可以帮助我筛选和查询 blob 中的内容吗？**

不，如果需要在 blob 数据内搜索，请使用查询加速或 Azure 搜索。

**对于索引标记值是否有任何要求？**

Blob 索引标记仅支持字符串数据类型和查询，并按字典顺序返回结果。 对于数字，为零填充数字。 对于日期和时间，请将存储为符合 ISO 8601 的格式。

**Blob 索引标记和 Azure 资源管理器标记是相关的吗？**

不能，资源管理器标记有助于组织控制平面资源，如订阅、资源组和存储帐户。 索引标记提供数据平面的 blob 管理和发现。

## <a name="next-steps"></a>后续步骤

有关如何使用 blob 索引的示例，请参阅 [使用 blob 索引管理和查找数据](storage-blob-index-how-to.md)。

了解 [生命周期管理](storage-lifecycle-management-concepts.md) 并设置具有 blob 索引匹配的规则。