---
title: Azure 存储中 Azure 角色分配条件的安全注意事项（预览版）
titleSuffix: Azure Storage
description: Azure 角色分配条件和 Azure 基于特性的访问控制 (Azure ABAC) 的安全注意事项。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 46be6327052aa280b9d23bc05764550a05739c5e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627912"
---
# <a name="security-considerations-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Azure 存储中 Azure 角色分配条件的安全注意事项（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

若要使用 [Azure 基于特性的访问控制 (Azure ABAC)](storage-auth-abac.md) 来全面保护资源，还必须保护 [Azure 角色分配条件](../../role-based-access-control/conditions-format.md)中使用的[特性](storage-auth-abac-attributes.md)。 例如，如果条件基于文件路径，则你应该注意到，如果主体拥有不受限制的权限，可以重命名文件路径，则访问权限可能会遭到恶意利用。

本文介绍应在角色分配条件中考虑到的安全注意事项。

## <a name="use-of-other-authorization-mechanisms"></a>其他授权机制的使用

仅当使用 Azure RBAC 进行授权时才评估角色分配条件。 如果允许使用备用授权方法进行访问，则可以绕过这些条件：
- [共享密钥](/rest/api/storageservices/authorize-with-shared-key)授权
- [帐户共享访问签名](/rest/api/storageservices/create-account-sas) (SAS)
- [服务 SAS](/rest/api/storageservices/create-service-sas)。

同理，在采用[分层命名空间](../blobs/data-lake-storage-namespace.md) (HNS) 的存储帐户中使用[访问控制列表 (ACL)](../blobs/data-lake-storage-access-control.md) 授予访问权限时，将不会评估条件。

可以通过针对存储帐户[禁用共享密钥授权](shared-key-authorization-prevent.md)，来防止发生共享密钥、帐户级 SAS 和服务级 SAS 授权。 由于用户委托 SAS 依赖于 Azure RBAC，因此，在使用此授权方法时会评估角色分配条件。

> [!NOTE]
> 在 Data Lake Storage Gen2 中使用 ACL 授予访问权限时，将不会评估角色分配条件。 在这种情况下，必须规划好访问权限的范围，使其不会与通过 ACL 授予的访问权限重叠。

## <a name="securing-storage-attributes-used-in-conditions"></a>保护在条件中使用的存储特性

### <a name="blob-path"></a>Blob 路径

使用 Blob 路径作为条件的 @Resource 特性时，还应该防止用户在使用采用分层命名空间的帐户时通过重命名 Blob 来获取文件访问权限。 例如，如果你要创建一个基于 Blob 路径的条件，则还应限制用户访问以下操作：

| 操作 | 描述 |
| :--- | :--- |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | 此操作允许客户使用“路径创建”API 重命名文件。 |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | 此操作允许访问各种文件系统和路径操作。 |

### <a name="blob-index-tags"></a>Blob 索引标记

[Blob 索引标记](../blobs/storage-manage-find-blobs.md)用作存储中条件的自由格式特性。 如果使用这些标记创建任何访问条件，则还必须保护标记本身。 具体而言，`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` DataAction 允许用户修改存储对象的标记。 可以限制此操作，以防止用户通过操控标记键或值来获取对未经授权的对象的访问权限。

此外，如果在条件中使用了 Blob 索引标记，则在通过单独的操作更新数据和关联的索引标记的情况下，数据可能会易受攻击。 可以对 Blob 写入操作使用 `@Request` 条件来要求在同一更新操作中设置索引标记。 这种方法有助于从数据写入存储的那一刻起就开始保护数据。

#### <a name="tags-on-copied-blobs"></a>复制的 Blob 上的标记

默认情况下，在使用[复制 Blob](/rest/api/storageservices/Copy-Blob) API 或其任何变体时，Blob 索引标记不会从源 Blob 复制到目标。 若要在复制时保留 Blob 的访问权限范围，还应该复制标记。

#### <a name="tags-on-snapshots"></a>快照上的标记

无法修改 Blob 快照上的标记。 这意味着，必须在创建快照之前更新 Blob 上的标记。 如果修改某个基本 Blob 上的标记，则此 Blob 的快照上的标记将继续采用以前的值。

如果在创建快照之后修改某个基本 Blob 上的标记，则该基本 Blob 和快照的访问权限范围可能不同。

#### <a name="tags-on-blob-versions"></a>Blob 版本上的标记

通过[放置 Blob](/rest/api/storageservices/put-blob)、[放置块列表](/rest/api/storageservices/put-block-list)或[复制 Blob](/rest/api/storageservices/Copy-Blob) API 创建 Blob 版本时，不会复制 Blob 索引标记。 可以通过这些 API 的头指定标记。

可以在当前基本 Blob 和每个 Blob 版本上单独设置标记。 修改基本 Blob 上的标记时，不会更新以前版本上的标记。 如果你要使用标记更改某个 Blob 及其所有版本的访问权限范围，必须更新每个版本上的标记。

#### <a name="querying-and-filtering-limitations-for-versions-and-snapshots"></a>版本与快照的查询和筛选限制

使用标记查询和筛选容器中的 Blob 时，响应中只会包含基本 Blob。 不会包含具有所请求的键和值的 Blob 版本或快照。

## <a name="roles-and-permissions"></a>角色和权限

如果对 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)使用角色分配条件，应仔细检查该角色向主体授予的所有权限。

### <a name="inherited-role-assignments"></a>继承的角色分配

可为管理组、订阅、资源组、存储帐户或容器配置角色分配，并在每个级别按此顺序继承这些角色分配。 Azure RBAC 采用累加模型，因此，有效权限是每个级别的角色分配的总和。 如果通过多个角色分配为某个主体分配了相同的权限，则会在每个级别根据每个分配单独评估使用该权限执行的操作的访问权限。

由于条件是作为角色分配中的条件实现的，因此任何无条件角色分配都可能允许用户绕过该条件。 假设你将“存储 Blob 数据参与者”角色分配给了订阅中的某个存储帐户的用户，但只将某个条件添加到了该存储帐户的分配。 在这种情况下，该用户可以在订阅级别通过角色分配不受限制地访问该存储帐户。

正因如此，应该针对整个资源层次结构中的所有角色分配一致性地应用条件。

## <a name="other-considerations"></a>其他注意事项

### <a name="condition-operations-that-write-blobs"></a>写入 Blob 的条件操作

许多写入 Blob 的操作需要 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` 或 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` 权限。 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)和[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)等内置角色会向安全主体授予这两种权限。

在这些角色上定义角色分配条件时，应该对这两种权限使用相同的条件，以确保对写入操作实施一致的访问限制。

### <a name="behavior-for-copy-blob-and-copy-blob-from-url"></a>“复制 Blob”和“从 URL 复制 Blob”的行为

对于[复制 Blob](/rest/api/storageservices/Copy-Blob) 和[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 操作，只会针对目标 Blob 评估使用 Blob 路径作为 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 操作及其子操作中的特性的 `@Request` 条件。

对于源 Blob 上的条件，将评估 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 操作上的 `@Resource` 条件。

### <a name="behavior-for-get-page-ranges"></a>“获取页面范围”的行为

对于[获取页面范围](/rest/api/storageservices/get-page-ranges)操作，只会针对目标 Blob 评估使用 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags` 作为 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 操作及其子操作上的特性的 `@Resource` 条件。

条件不适用于对 API 中 `prevsnapshot` URI 参数指定的 Blob 的访问权限。

## <a name="see-also"></a>另请参阅

- [使用 Azure 角色分配条件授予对 Blob 的访问权限（预览版）](storage-auth-abac.md)
- [Azure 存储中 Azure 角色分配条件的操作和特性（预览版）](storage-auth-abac-attributes.md)
- [什么是 Azure 基于特性的访问控制 (Azure ABAC)（预览版）？](../../role-based-access-control/conditions-overview.md)
