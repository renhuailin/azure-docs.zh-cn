---
title: Azure 存储中 Azure 角色分配条件的操作和属性（预览版）
titleSuffix: Azure Storage
description: Azure 存储中 Azure 角色分配条件和 Azure 基于属性的访问控制 (Azure ABAC) 的支持操作和属性。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 791e533cc1f0dafa8724faa8cc3a46d1f8c770d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665996"
---
# <a name="actions-and-attributes-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Azure 存储中 Azure 角色分配条件的操作和属性（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍了支持的属性字典，可在每个 Azure 存储 [DataAction](../../role-based-access-control/role-definitions.md#dataactions) 的 Azure 角色分配条件中使用。 有关受特定权限或 DataAction 影响的 Blob 服务操作的列表，请参阅 [针对 Blob 服务操作的权限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-blob-service-operations)。

若要了解角色分配条件格式，请参阅 [Azure 角色分配条件格式和语法](../../role-based-access-control/conditions-format.md)

## <a name="suboperations"></a>子操作

多个存储服务操作可以与单个权限或 DataAction 相关联。 但是，与同一权限相关联的每个操作都可能支持不同的参数。 “子操作”能够区分需要相同权限但支持不同条件属性集的服务操作。 因此，通过使用子操作，可以指定一个条件来访问支持给定参数的操作的子集。 然后，可以对不支持该参数的相同操作使用另一个操作访问条件。

例如，对于数十个不同的服务操作，`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` 操作是必需的。 其中一些操作可以接受 blob 索引标记作为请求参数，而其他操作则不能。 对于接受 blob 索引标记作为参数的操作，可以在请求条件中使用 blob 索引标记。 但是，如果在 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` 操作上定义了此类条件，则不接受标记作为请求参数的所有操作都无法评估此条件，并且将无法通过授权访问检查。

在这种情况下，可以使用可选子操作 `Blob.Write.WithTagHeaders` 来将条件仅应用于支持 blob 索引标记作为请求参数的操作。

同样，只有选择 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` 操作上的操作才能将支持 blob 索引标记作为访问的先决条件。 此操作子集由 `Blob.Read.WithTagConditions` 子操作标识。

> [!NOTE]
> Blob 还支持存储用户定义的任意键-值元数据。 尽管元数据与 Blob 索引标记类似，但你也必须将 Blob 索引标记与条件配合使用。 有关详细信息，请参阅 [通过 Blob 索引（预览版）管理和查找 Azure Blob 存储上的数据](../blobs/storage-manage-find-blobs.md)。

在此预览版中，存储帐户支持以下子操作：

> [!div class="mx-tableFixed"]
> | DataAction | 子操作 | Display name | 说明 |
> | :--- | :--- | :--- | :--- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `Blob.Read.WithTagConditions` | 支持标记条件的 Blob 读取操作 | 包括 REST 操作：获取 Blob、获取 Blob 元数据、获取 Blob 属性、获取块列表、获取页面范围、查询 Blob 内容。 |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` <br/> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `Blob.Write.WithTagHeaders` | 包含可选标记的内容的 Blob 写入 | 包括 REST 操作：放置 Blob、复制 Blob、从 URL 复制 Blob 和放置块列表。 |

## <a name="actions-and-suboperations"></a>操作和子操作

下表列出了 Azure 存储中条件的支持操作和子操作。

> [!div class="mx-tableFixed"]
> | Display name | 说明 | DataAction |
> | --- | --- | --- |
> | 删除 blob | 用于删除 blob 的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` |
> | 读取 blob | 用于读取 blob 的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
> | 从具有标记条件的 blob 中读取内容  | REST 操作：获取 Blob、获取 Blob 元数据、获取 Blob 属性、获取块列表、获取页面范围和查询 Blob 内容。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>“子操作”<br/>`Blob.Read.WithTagConditions` |
> | 写入到 blob | 用于写入到 blob 的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` |
> | 写入到具有 blob 索引标记的 blob | REST 操作：放置 Blob、放置块列表、复制 Blob 和从 URL 复制 Blob。 |`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>“子操作”<br/>`Blob.Write.WithTagHeaders` |
> | 创建 blob 或快照，或追加数据 | 用于创建 blob 的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` |
> | 将内容写入到具有 blob 索引标记的 blob | REST 操作：放置 Blob、放置块列表、复制 Blob 和从 URL 复制 Blob。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>“子操作”<br/>`Blob.Write.WithTagHeaders` |
> | 删除 blob 的版本 | 用于删除 blob 的版本的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` |
> | 更改 blob 的所有权 | 用于更改 blob 的所有权的DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` |
> | 修改 blob 的权限 | 用于修改 blob 的权限的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` |
> | 重命名文件或目录 | 用于重命名文件或目录的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` |
> | 永久删除 blob 替代软删除 | 用于永久删除 blob 替代软删除的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` |
> | 具有 HNS 的帐户的所有数据操作 | 用于具有 HNS 的存储帐户的所有数据操作的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` |
> | 读取 blob 索引标记 | 用于读取 blob 索引标记的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` |
> | 写入 blob 索引标记 | 用于写入 blob 索引标记的 DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` |

## <a name="attributes"></a>属性

下表列出了 Azure 存储中条件的支持属性的说明。

> [!div class="mx-tableFixed"]
> | Display name | 说明 | 属性 |
> | --- | --- | --- |
> | 容器名称| 存储容器或文件系统的名称。 想要检查容器名称时使用。 | `containers:name` |
> | Blob 路径 | 虚拟目录、blob、文件夹或文件资源的路径。 想要检查 blob 路径中的 blob 名称或文件夹时使用。 | `blobs:path` |
> | blob 索引标记 [键] | blob 资源上的索引标记。 可以与 blob 资源一起存储的任意用户定义的键值属性。 想要检查 blob 索引标记中的键时使用。 | `tags&$keys$&` |
> | blob 索引标记 [键中的值] | blob 资源上的索引标记。 可以与 blob 资源一起存储的任意用户定义的键值属性。 想要检查 blob 索引标记中的键（区分大小写）和值时使用。 | `tags:`*keyname*`<$key_case_sensitive$>` |

> [!NOTE]
> 除非另有说明，否则列出的属性和值视为不区分大小写。

> [!NOTE]
> 为 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path` 属性指定条件时，值不应包含容器名称或前面的“/”字符。 使用不带任何 URL 编码的路径字符。

> [!NOTE]
> 对于具有 [分层命名空间](../blobs/data-lake-storage-namespace.md)(HNS) 的Data Lake Storage Gen2 存储帐户，不支持 blob 索引标记。 不应在启用了 HNS 的存储帐户上使用索引标记创建角色分配条件。

## <a name="attributes-available-for-each-action"></a>可用于每个操作的属性

下表列出了根据目标操作可在条件表达式中使用哪些属性。 如果针对单个条件选择多个操作，则能为条件选择的属性可能较少，因为这些属性必须在所选操作中可用。

> [!div class="mx-tableFixed"]
> | DataAction | 属性 | 类型 | 适用于 |
> | --- | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>“子操作”<br/>`Blob.Read.WithTagConditions` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>“子操作”<br/>`Blob.Write.WithTagHeaders` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>“子操作”<br/>`Blob.Write.WithTagHeaders` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` | `containers:name` | 字符串 | ResourceAttributeOnly |
> |  | `blobs:path` | 字符串 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |

## <a name="see-also"></a>另请参阅

- [示例 Azure 角色分配条件（预览版）](storage-auth-abac-examples.md)
- [Azure 角色分配条件格式和语法（预览版）](../../role-based-access-control/conditions-format.md)
- [什么是 Azure 基于属性的访问控制 (Azure ABAC)？（预览版）](../../role-based-access-control/conditions-overview.md)
