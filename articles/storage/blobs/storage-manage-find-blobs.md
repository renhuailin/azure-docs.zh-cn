---
title: 通过 Blob 索引标记管理和查找 Azure Blob 数据
description: 了解如何使用 Blob 索引标记对 Blob 对象进行分类、管理和查询。
author: normesta
ms.author: normesta
ms.date: 06/14/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: c4ff918be67d74d536159ebbd3e707c1d7e68e8b
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730741"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags"></a>通过 Blob 索引标记管理和查找 Azure Blob 数据

随着数据集的变大，在大量数据中找到特定对象可能会很困难。 Blob 索引标记使用键值索引标记属性来提供数据管理和发现功能。 可在单个容器内或在存储帐户中的所有容器之间分类和查找对象。 随着数据需求的变化，可更新对象索引标记来对其进行动态分类。 对象可利用其当前的容器组织就地保留。

借助 Blob 索引标记，你能够：

- 使用键值索引标记对 Blob 进行动态分类
- 在整个存储帐户中快速查找带有标记的特定 Blob
- 根据索引标记的计算来指定 Blob API 的条件行为
- 将索引标记用于功能上的高级控件（例如 [Blob 生命周期管理](storage-lifecycle-management-concepts.md)）

思考这样一种场景：你的存储帐户中有数百万个 Blob，许多不同的应用程序对其进行访问。 你想要从单个项目中查找所有相关数据。 由于数据可分散在具有不同命名约定的多个容器中，因此你不确定哪些在查找范围内。 不过，你的应用程序会基于其项目上传包含标记的所有数据。 你可将 `Project = Contoso` 用作发现条件，而不是搜索数百万个 Blob，再对名称和属性进行比较。 Blob 索引会筛选你的整个存储帐户中的所有容器来快速查找，并且只从 `Project = Contoso` 范围一个包含 50 个 Blob 的集。

若要首先查看有关如何使用 Blob 索引的示例，请参阅[使用 Blob 索引标记来管理和查找数据](storage-blob-index-how-to.md)。

## <a name="blob-index-tags-and-data-management"></a>Blob 索引标记和数据管理

容器和 Blob 名称的前缀为一维分类。 通过 Blob 索引标记，可对 [Blob 数据类型（块、追加或页）](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)进行多维分类。 Azure Blob 存储会本机索引多维分类，让你能够快速找到数据。

假设你的存储帐户中有以下 5 个 Blob：

- container1/transaction.csv
- container2/campaign.docx
- photos/bannerphoto.png
- archives/completed/2019review.pdf
- logs/2020/01/01/logfile.txt

这些 Blob 使用“容器/虚拟文件夹/Blob 名称”这一前缀进行分隔。 你可在这五个 Blob 上设置索引标记属性 `Project = Contoso`，在将它们分类到一起的同时保留当前的前缀结构。 添加索引标记后，就能使用索引来筛选和查找数据，从而消除了移动数据的需求。

## <a name="setting-blob-index-tags"></a>设置 Blob 索引标记

Blob 索引标记是可应用于存储帐户中的新对象或现有对象的键值属性。 可使用[放置 Blob](/rest/api/storageservices/put-blob)、[放置块列表](/rest/api/storageservices/put-block-list)或[复制 Blob](/rest/api/storageservices/copy-blob) 操作和可选 `x-ms-tags` 标头在上传过程中指定索引标记。 如果存储帐户中已有 Blob，可调用[设置 Blob 标记](/rest/api/storageservices/set-blob-tags)在请求正文中传递一个具有索引标记的格式化 XML 文档。

> [!IMPORTANT]
> 设置 Blob 索引标记的操作可由[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)来执行，也可由具有共享访问签名且有权访问 Blob 的标记（SAS 权限 `t`）的任何人来执行。
>
> 此外，具有 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 权限的 RBAC 用户也可执行此操作。

可在 Blob 上应用一个标记来描述完成数据处理的时间。

> "processedDate" = '2020-01-01'

也可在 Blob 上应用多个标记，使数据更具描述性。

> "Project" = 'Contoso'  
> "Classified" = 'True'  
> "Status" = 'Unprocessed'  
> "Priority" = '01'

若要修改现有索引标记属性，请检索现有标记属性，修改这些属性，然后通过[设置 Blob 标记](/rest/api/storageservices/set-blob-tags)操作替换它们。 若要从 Blob 中删除所有索引标记，请在未指定任何标记属性的情况下调用`Set Blob Tags`操作。 Blob 索引标记是 Blob 数据内容的子资源，因此`Set Blob Tags`不会修改任何基础内容，也不会更改 Blob 的上次修改时间或 eTag。 可创建或修改所有当前基础 Blob 和历史版本的索引标记。 但是，不能修改快照或已软删除的 Blob 上的标记。

Blob 索引标记存在以下限制：

- 每个 Blob 最多可以有 10 个 Blob 索引标记
- 标记键必须介于 1 至 128 个字符之间
- 标记值必须介于 0 至 256 个字符之间
- 标记键和值都区分大小写
- 标记键和值仅支持字符串数据类型。 任何数字、日期、时间或特殊字符均保存为字符串
- 标记键和值必须遵循以下命名规则：
  - 字母数字字符：
    - **a** 至 **z**（小写字母）
    - **A** 至 **Z**（大写字母）
    - **0** 至 **9**（数字）
  - 有效的特殊字符：空格、加号、减号、句点、冒号、等号、下划线、正斜杠 (` +-.:=_/`)

## <a name="getting-and-listing-blob-index-tags"></a>获取和列出 Blob 索引标记

Blob 索引标记作为子资源与 Blob 数据一起存储，可独立于基础 Blob 数据内容对其进行检索。 可通过[获取 Blob 标记](/rest/api/storageservices/get-blob-tags)操作检索单个 Blob 的 Blob 索引标记。 带 `include:tags` 参数的[列出 Blob](/rest/api/storageservices/list-blobs) 操作将返回容器中的所有 Blob 及其 Blob 索引标记。

> [!IMPORTANT]
> 获取和列出 Blob 索引标记的操作可由[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)来执行，也可由具有共享访问签名且有权访问 Blob 的标记（SAS 权限 `t`）的任何人来执行。
>
> 此外，具有 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 权限的 RBAC 用户也可执行此操作。

对于至少有一个 Blob 索引标记的任何 Blob，在[列出 Blob](/rest/api/storageservices/list-blobs)、[获取 Blob](/rest/api/storageservices/get-blob) 和[获取 Blob 属性](/rest/api/storageservices/get-blob-properties)操作中会返回 `x-ms-tag-count`，用于指示 Blob 上索引标记的计数。

## <a name="finding-data-using-blob-index-tags"></a>使用 Blob 索引标记查找数据

索引引擎将键值属性公开为多维索引。 设置索引标记后，它们存在于 Blob 上，可立即检索。 可能需要一段时间才会更新 Blob 索引。 更新 Blob 索引后，可使用 Blob 存储提供的本机查询和发现功能。

通过[按标记查找 Blob](/rest/api/storageservices/find-blobs-by-tags) 操作，可获取一组经过筛选的 Blob，其索引标记与给定的查询表达式匹配。 `Find Blobs by Tags` 支持在存储帐户内的所有容器之间进行筛选，你也可将筛选范围限定为单个容器。 索引标记键和值都是字符串，因此关系运算符使用字典顺序排序。

> [!IMPORTANT]
> 使用 Blob 索引标记查找数据的操作可由[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)来执行，也可由具有共享访问签名且有权按标记查找 Blob（SAS 权限 `f`）的任何人来执行。
>
> 此外，具有 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` 权限的 RBAC 用户也可执行此操作。

Blob 索引筛选需遵守以下条件：

- 应使用双引号 (") 将标记键引起来
- 应使用单引号 (') 将标记值和容器名称引起来
- @ 字符仅适用于对指定容器名称进行筛选（例如 `@container = 'ContainerName'`）
- 应用筛选器时对字符串进行字典顺序排序
- 对同一键的同侧范围操作无效（例如 `"Rank" > '10' AND "Rank" >= '15'`）
- 使用 REST 创建筛选器表达式时，应对字符进行 URI 编码
- 针对使用单个标记（例如 StoreID = "100"）的相等匹配优化了标记查询。  使用包含 >、>=、<、<= 的单个标记的范围查询也是高效的。 使用 AND 和多个标记的任何查询都不会有那么高的效率。  例如，Cost > "01" AND Cost <= "100" 是高效的。 Cost > "01 AND StoreID = "2" 的效率没有那么高。

下表显示了 `Find Blobs by Tags` 的所有有效运算符：

|  运算符  |  说明  | 示例 |
|------------|---------------|---------|
|     =      |     Equal     | `"Status" = 'In Progress'` |
|     >      |  大于 | `"Date" > '2018-06-18'` |
|     >=     |  大于或等于 | `"Priority" >= '5'` |
|     <      |  小于   | `"Age" < '32'` |
|     <=     |  小于或等于  | `"Company" <= 'Contoso'` |
|    AND     |  逻辑与  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | 范围限定为特定容器 | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> 设置和查询标记时，应熟悉字典顺序。
>
> - 数字排在字母之前。 数字根据首位数进行排序。
> - 大写字母排在小写字母之前。
> - 符号不是标准符号。 某些符号排在数值之前。 其他符号排在字母之前或之后。

## <a name="conditional-blob-operations-with-blob-index-tags"></a>带有 Blob 索引标记的条件 Blob 操作

在 REST 2019-10-10 及更高版本中，大多数 [Blob 服务 API](/rest/api/storageservices/operations-on-blobs) 现在都支持条件标头 `x-ms-if-tags`，因此仅在符合指定的 Blob 索引条件时，操作才会成功。 如果不符合该条件，将收到`error 412: The condition specified using HTTP conditional header(s) is not met`。

`x-ms-if-tags` 标头可与其他现有的 HTTP 条件标头（If-Match 和 If-None-Match 等）组合。 如果请求中提供了多个条件标头，则其评估结果必须都为 true，操作才能成功。 所有条件标头都与逻辑 AND 有效地组合在一起。

下表显示了条件操作的有效运算符：

|  运算符  |  说明  | 示例 |
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
> 还有两个运算符（不等于和逻辑 OR），它们可用于针对 Blob 操作的条件 `x-ms-if-tags` 标头，但在`Find Blobs by Tags` 操作中不存在。

## <a name="platform-integrations-with-blob-index-tags"></a>带有 Blob 索引标记的平台集成

Blob 索引标记不仅有助于对 Blob 数据进行分类、管理和搜索，还可提供与其他 Blob 存储功能（如[生命周期管理](storage-lifecycle-management-concepts.md)）的集成。

### <a name="lifecycle-management"></a>生命周期管理

通过在生命周期管理中使用 `blobIndexMatch` 作为规则筛选器，可根据应用于 Blob 的索引标记将数据移到较冷的层或删除数据。 可在规则中更加细化且仅当 Blob 与指定的标记条件匹配时才移动或删除它们。

可将 Blob 索引匹配设置为生命周期规则中的独立筛选器集，以对带标记的数据应用操作。 也可将前缀和 Blob 索引进行组合来匹配更具体的数据集。 在生命周期规则中指定多个筛选器会应用逻辑 AND 运算。 仅当所有筛选条件都匹配时，此操作才适用。

以下示例生命周期管理规则适用于名为 videofiles 容器中的块 blob。 仅当数据与 Blob 索引标记条件 `"Status" == 'Processed' AND "Source" == 'RAW'` 匹配时，该规则才将 Blob 分层到存档存储。

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Azure 门户中生命周期管理的 Blob 索引匹配规则示例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

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

可使用下列方法之一来授予对 Blob 索引标记的访问权限：

- 使用 Azure 基于角色的访问控制 (Azure RBAC) 授予对 Azure Active Directory (Azure AD) 安全主体的权限。 使用 Azure AD 实现更高级别的安全性和易用性。 有关通过 Blob 操作使用 Azure AD 的详细信息，请参阅[授权访问 Azure 存储中的数据](../common/authorize-data-access.md)。
- 使用共享访问签名 (SAS) 委托对 Blob 索引的访问权限。 有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。
- 使用帐户访问密钥来通过共享密钥授权操作。 有关详细信息，请参阅[通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key)。

Blob 索引标记是 Blob 数据的子资源。 具有读取或写入 Blob 的权限或 SAS 令牌的用户可能无权访问 Blob 索引标记。

### <a name="role-based-access-control"></a>基于角色的访问控制

可向使用 [Azure AD 标识](../common/authorize-data-access.md)的调用方授予以下权限来对 Blob 索引标记执行操作。

| Blob 索引标记操作                                          | Azure RBAC 操作                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [设置 Blob 标记](/rest/api/storageservices/set-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write    |
| [获取 Blob 标记](/rest/api/storageservices/get-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read     |
| [按标记查找 Blob](/rest/api/storageservices/find-blobs-by-tags) | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action |

要执行索引标记操作，需具备与基础 Blob 数据分开的其他权限。 向[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)授予这三个 Blob 索引标记操作的权限。 向[存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)仅授予`Find Blobs by Tags` 和`Get Blob Tags`操作的权限。

### <a name="sas-permissions"></a>SAS 权限

可向使用[共享访问签名 (SAS)](../common/storage-sas-overview.md) 的调用方授予范围内的权限来对 Blob 索引标记执行操作。

#### <a name="blob-sas"></a>Blob SAS

可在 Blob SAS 中授予以下权限来允许访问 Blob 索引标记。 仅有 Blob 读取和写入权限不足以能够读取或写入其索引标记。

| 权限 | URI 符号 | 允许的操作                |
|------------|------------|-----------------------------------|
| 索引标记 |     t      | 获取和设置 Blob 的索引标记 |

#### <a name="container-sas"></a>容器 SAS

可在容器 SAS 中授予以下权限来允许筛选 Blob 标记。 `Blob List` 权限不足以允许按索引标记筛选 Blob。

| 权限 | URI 符号 | 允许的操作         |
|------------|------------|----------------------------|
| 索引标记 |     f      | 查找带有索引标记的 Blob |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>在元数据和 Blob 索引标记之间选择

通过 Blob 索引标记和元数据，都能够将任意用户定义的键值属性与 Blob 资源一起存储。 可直接检索和设置这两者，无需返回或更改 Blob 的内容。 元数据和索引标记都可使用。

只有索引标记会自动索引，并可由本机 Blob 存储服务进行搜索。 无法在本机索引或搜索元数据。 必须使用单独的服务，例如 [Azure 搜索](../../search/search-blob-ai-integration.md)。 Blob 索引标记具有额外的允许读取、筛选和写入的权限，这些权限与基础 Blob 数据分开。 元数据使用与 Blob 相同的权限，并通过[获取 Blob](/rest/api/storageservices/get-blob) 和[获取 Blob 属性](/rest/api/storageservices/get-blob-properties)操作返回为 HTTP 标头。 Blob 索引标记使用 [Microsoft 管理的密钥](../common/storage-service-encryption.md)进行静态加密。 元数据使用为 Blob 数据指定的同一加密密钥进行静态加密。

下表总结了元数据和 Blob 索引标记之间的差异：

|              |   Metadata   |   Blob 索引标记  |
|--------------|--------------|--------------------|
| **限制**      | 无数值限制，总共 8 KB，不区分大小写 | 每个 Blob 最多 10 个标记，每个标记 768 个字节，区分大小写 |
| **更新**    | 不可用于存档层，`Set Blob Metadata`会替换所有现有元数据，`Set Blob Metadata`会更改 Blob 的上次修改时间 | 可用于存档层，`Set Blob Tags`会替换所有现有标记，但`Set Blob Tags`不更改 Blob 的上次修改时间 |
| **存储**     | 与 Blob 数据存储在一起 | Blob 数据的子资源 |
| **索引和查询** | 必须使用单独的服务，例如 Azure 搜索 | 索引和查询功能内置于 Blob 存储中 |
| **加密** | 使用 Blob 数据所用的加密密钥进行静态加密 | 使用 Microsoft 管理的加密密钥进行静态加密 |
| **定价** | 元数据的大小包含在 Blob 的存储成本中 | 每个索引标记的成本固定 |
| **标头响应** | 元数据在`Get Blob` 和`Get Blob Properties`中返回为标头 | 标记计数由`Get Blob` 或`Get Blob Properties`返回，而标记仅由`Get Blob Tags`和`List Blobs` 返回 |
| **权限**  | 对 Blob 数据的读取或写入权限扩展到元数据 | 读取、筛选或写入索引标记需要额外权限 |
| **命名** | 元数据名称必须遵循 C# 标识符的命名规则 | Blob 索引标记支持更大范围的字母数字字符 |

## <a name="pricing"></a>定价

按存储帐户中每月索引标记平均数收取费用。 索引引擎不会产生任何费用。 设置 Blob 标记、获取 Blob 标记和查找 Blob 标记的请求是按当前各自的事务速率收费的。 请注意，在执行按标记查找 Blob 事务时，使用的列表事务数等于请求中的子句数。 例如，查询 (StoreID = 100) 是一个列表事务。  查询 (StoreID = 100 AND SKU = 10010) 是两个列表事务。 请查看[块 blob 定价了解详细信息](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability-and-storage-account-support"></a>区域可用性和存储帐户支持

Blob 索引标记仅在禁用了分层命名空间 (HNS) 的常规用途 v2 帐户上可用。 不支持常规用途 v1 帐户，但你可以将任何常规用途 v1 帐户升级到常规用途 v2 帐户。

高级存储帐户不支持索引标记。 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

Blob 索引标记目前在所有公共区域提供。

若要开始，请参阅[使用 Blob 索引标记管理和查找数据](storage-blob-index-how-to.md)。

> [!IMPORTANT]
> 必须先注册订阅，然后才能在存储帐户上使用 Blob 索引。 请查看本文的[条件和已知问题](#conditions-and-known-issues)部分。

## <a name="conditions-and-known-issues"></a>条件和已知问题

本部分介绍已知问题和条件。

- 仅支持常规用途 v2 帐户。 不支持高级块 blob、旧的 blob 和启用了分层命名空间的帐户。 不支持常规用途 v1 帐户。
- 上传带有索引标记的页 blob 不会保留标记。 请在上传页 blob 后设置标记。
- 当筛选范围限定为单个容器时，只有在筛选表达式中的所有索引标记都是相等性检查 (key=value) 时，才能传递 `@container`。
- 将范围运算符与 `AND` 条件一起使用时，只能指定同一索引标记键名称 (`"Age" > '013' AND "Age" < '100'`)。
- 如果启用了版本控制，仍可在当前版本上使用索引标记。 对于以前的版本，会为版本保留索引标记，但不会传递到 Blob 索引引擎。 不能通过查询索引标记来检索以前的版本。
- 没有 API 用于确定索引标记是否已索引。
- 生命周期管理仅支持带有 Blob 索引匹配的相等性检查。
- `Copy Blob` 不会将 Blob 索引标记从源 Blob 复制到新的目标 Blob。 可指定要在复制操作中应用到目标 Blob 的标记。

## <a name="faq"></a>常见问题解答

**Blob 索引可帮助筛选和查询 Blob 中的内容吗？**

否，如果需要在 Blob 数据内搜索，请使用查询加速或 Azure 搜索。

**对于索引标记值是否有任何要求？**

Blob 索引标记仅支持字符串数据类型，且查询按字典顺序返回结果。 对于数字，是零填充数字。 对于日期和时间，则存储为符合 ISO 8601 的格式。

**Blob 索引标记和 Azure 资源管理器标记是否相关？**

否，资源管理器标记可帮助组织控制平面资源，例如订阅、资源组和存储帐户。 而索引标记在数据平面提供 Blob 管理和发现功能。

## <a name="next-steps"></a>后续步骤

有关如何使用 Blob 索引的示例，请参阅[使用 Blob 索引管理和查找数据](storage-blob-index-how-to.md)。

了解[生命周期管理](storage-lifecycle-management-concepts.md)和设置带有 Blob 索引匹配的规则。
