---
title: Blob 版本控制
titleSuffix: Azure Storage
description: Blob 存储版本控制会自动维护对象的先前版本，并通过时间戳识别它们。 如果错误地修改或删除了数据，则可以还原 blob 的先前版本以恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0641a7357a334847bc52c3089eb4cf36cc3e3175
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123467603"
---
# <a name="blob-versioning"></a>Blob 版本控制

可以启用 Blob 存储版本控制来自动维护对象的先前版本。 启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原 blob 的先前版本以恢复数据。

## <a name="recommended-data-protection-configuration"></a>推荐的数据保护配置

Blob 版本控制是针对 Blob 数据的综合性数据保护策略的一部分。 为实现对 Blob 数据的最佳保护，Microsoft 建议启用以下所有数据保护功能：

- Blob 版本控制，用于自动维护 Blob 的先前版本。 启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原 blob 的先前版本以恢复数据。 若要了解如何启用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。
- 容器软删除，用于还原已删除的容器。 若要了解如何启用容器软删除，请参阅[启用和管理容器的软删除](soft-delete-container-enable.md)。
- Blob 软删除，用于还原已删除的 Blob、快照或版本。 若要了解如何启用 blob 软删除，请参阅[启用和管理 blob 的软删除](soft-delete-blob-enable.md)。

若要详细了解 Microsoft 的数据保护建议，请参阅[数据保护概述](data-protection-overview.md)。

## <a name="how-blob-versioning-works"></a>Blob 版本控制的工作原理

版本在给定时间点捕获 blob 的状态。 每个版本都由一个版本 ID 标识。 为存储帐户启用 blob 版本控制后，首次创建 blob 时以及随后每次修改 blob 时，Azure 存储都会自动创建一个具有唯一 ID 的新版本。

版本 ID 可以标识当前版本或先前版本。 一个 blob 一次只能有一个当前版本。

创建一个新的 blob 时，存在一个版本，该版本就是当前版本。 修改现有 blob 时，当前版本将变为先前版本。 将创建一个新版本以捕获更新的状态，该新版本即为当前版本。 删除某个 blob 时，该 blob 的当前版本将成为先前版本，当前版本不复存在。 Blob 的任何先前版本都将保留。

下图显示了如何在写入操作中创建版本，以及如何将先前版本提升为当前版本：

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="显示 blob 版本控制工作原理的示意图":::

Blob 版本是不可变的。 不能修改现有 blob 版本的内容或元数据。

如果每个 blob 都具有大量版本，则会增加 blob 列表操作的延迟。 Microsoft 建议每个 Blob 保留的版本少于 1000 个。 可以使用生命周期管理来自动删除旧版本。 有关生命周期管理的详细信息，请参阅[通过自动执行 Azure Blob 存储访问层来优化成本](storage-lifecycle-management-concepts.md)。

Blob 版本控制可用于标准常规用途 v2、高级块 blob 和旧版 Blob 存储帐户。 当前不支持启用了分层命名空间以与 Azure Data Lake Storage Gen2 一起使用的存储帐户。

Azure 存储 REST API 2019-10-10 版及更高版本支持 blob 版本控制。

> [!IMPORTANT]
> Blob 版本控制无法帮助你恢复意外删除的存储帐户或容器。 若要防止意外删除存储帐户，请在存储帐户资源上配置一个锁定。 有关锁定存储帐户的详细信息，请参阅[将 Azure 资源管理器锁定应用于存储帐户](../common/lock-account-resource.md)。

### <a name="version-id"></a>版本 ID

每个 blob 版本都由一个唯一版本 ID 标识。 版本 ID 的值是更新 blob 时的时间戳。 创建版本时分配版本 ID。

可以通过提供 blob 的特定版本 ID 来对其执行读取或删除操作。 如果省略了版本 ID，则操作将针对当前版本进行。

调用写入操作来创建或修改 blob 时，Azure 存储将在响应中返回 x-ms-version-id 标头。 此标头包含由写入操作创建的 blob 的当前版本的版本 ID。

版本 ID 在版本的生存期内保持不变。

### <a name="versioning-on-write-operations"></a>写入操作时的版本控制

启用 blob 版本控制后，对 blob 的每次写入操作都会创建一个新版本。 写入操作包括[放置 Blob](/rest/api/storageservices/put-blob)、[放置块列表](/rest/api/storageservices/put-block-list)、[复制 Blob](/rest/api/storageservices/copy-blob) 和[设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)。

如果写入操作创建了新的 blob，则生成的 blob 为 blob 的当前版本。 如果写入操作修改了现有 blob，则当前版本将成为先前版本，并创建新的当前版本以捕获更新的 blob。

下图显示了写入操作对 blob 版本的影响。 为简单起见，本文中的示意图将版本 ID 显示为一个简单的整数值。 实际上，版本 ID 是一个时间戳。 当前版本显示为蓝色，先前版本显示为灰色。

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="显示写入操作如何影响接受版本控制的 blob 的示意图。":::

> [!NOTE]
> 如果 blob 是在为存储帐户启用版本控制之前创建的，则它没有版本 ID。 修改该 blob 时，修改后的 blob 将成为当前版本，并将创建一个版本来保存 blob 在更新前的状态。 为版本分配的版本 ID 是其创建时间。

为存储帐户启用 blob 版本控制后，除[放置块](/rest/api/storageservices/put-block)操作外，针对块 blob 的所有写入操作都会触发新版本的创建。

对于页 blob 和追加 blob，仅部分写入操作会触发版本的创建。 这些操作包括：

- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置块列表](/rest/api/storageservices/put-block-list)
- [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)
- [复制 Blob](/rest/api/storageservices/copy-blob)

以下操作不会触发创建新版本。 若要从这些操作捕获更改，请手动生成快照：

- [放置页](/rest/api/storageservices/put-page)（页 blob）
- [追加块](/rest/api/storageservices/append-block)（追加 blob）

Blob 的所有版本必须具有相同的 blob 类型。 如果 blob 具有先前版本，则除非先删除该 blob 及其所有版本，否则无法使用其他类型的 blob 覆盖此类型的 blob。

### <a name="versioning-on-delete-operations"></a>删除操作时的版本控制

当你在未指定版本 ID 的情况下调用[删除 Blob](/rest/api/storageservices/delete-blob) 操作时，当前版本将成为先前版本，也不再会有新的当前版本。 将保留该 blob 的所有现有先前版本。

下图显示了删除操作对接受版本控制的 blob 的影响：

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="显示删除接受版本控制的 blob 的示意图。":::

若要删除 blob 的特定版本，请在删除操作中提供该版本的 ID。 如果还为存储帐户启用了 blob 软删除，则在软删除保持期结束之前，该版本将保留在系统中。

将新数据写入 blob 时将创建 blob 的新当前版本。 任何现有版本不受影响，如下图所示。

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="显示在删除后重新创建接受版本控制的 blob 的示意图。":::

### <a name="access-tiers"></a>访问层级

可以通过调用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)操作将块 blob 的任何版本（包括当前版本）移到不同的 blob 访问层。 要利用较低的容量定价，可以将较早版本的 blob 移到冷访问层或存档访问层。 有关详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md)。

若要自动将块 blob 移到相应的层，请使用 blob 生命周期管理。 有关生命周期管理的详细信息，请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)。

## <a name="enable-or-disable-blob-versioning"></a>启用或禁用 blob 版本控制

若要了解如何启用或禁用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。

禁用 blob 版本控制不会删除现有的 blob、版本或快照。 关闭 blob 版本控制后，任何现有版本在存储帐户中仍可访问。 后续不会再创建新版本。

如果在存储帐户上禁用版本控制后创建或修改了 blob，则将覆盖该 blob 并创建新版本。 更新的 blob 不再是当前版本，并且没有版本 ID。 对该 blob 的所有后续更新都将覆盖其数据，而不会保存先前的状态。

禁用版本控制后，可以使用版本 ID 读取或删除版本。 禁用版本控制后，还可以列出 blob 的版本。

下图显示了禁用版本控制后，修改 blob 时如何创建未接受版本控制的 blob。 与 blob 关联的任何现有版本都将保持不变。

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="显示禁用版本控制后修改的基本 blob 的示意图。":::

## <a name="blob-versioning-and-soft-delete"></a>Blob 版本控制和软删除

Microsoft 建议为存储帐户启用版本控制和 Blob 软删除以实现最佳数据保护。 有关 blob 软删除的详细信息，请参阅 [Azure 存储 blob 的软删除](./soft-delete-blob-overview.md)。

### <a name="overwriting-a-blob"></a>覆盖 blob

如果为存储帐户同时启用 blob 版本控制和 blob 软删除，则覆盖某个 blob 将自动创建一个新版本。 系统不会软删除新版本，并且不会在软删除保留期到期时删除该版本。 不会创建软删除的快照。

### <a name="deleting-a-blob-or-version"></a>删除 blob 或版本

如果为存储帐户同时启用版本控制和软删除，则在删除某个 blob 时，该 blob 的当前版本将成为先前版本。 不会创建新版本，也不会创建软删除的快照。 软删除保持期不适用于已删除的 blob。

软删除为删除 blob 版本提供额外的保护。 删除 blob 的先前版本时，该版本将被软删除。 在软删除保持期内，将一直保留软删除版本，到期后该版本将被永久删除。

若要删除 blob 的先前版本，请调用“删除 Blob”操作并指定版本 ID。

下图显示了在删除 blob 或 blob 版本时会发生的情况。

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="显示删除已启用软删除的版本的示意图。":::

### <a name="restoring-a-soft-deleted-version"></a>还原软删除的版本

在软删除保持期内，可使用[撤消删除 Blob](/rest/api/storageservices/undelete-blob) 操作来还原软删除的版本。 “撤消删除 Blob”操作始终会还原 blob 的所有软删除版本。 无法只还原一个软删除版本。

使用“撤消删除 Blob”操作还原软删除的版本不会将任何版本提升为当前版本。 若要还原当前版本，请首先还原所有软删除的版本，然后使用[复制 Blob](/rest/api/storageservices/copy-blob) 操作将先前版本复制到新的当前版本。

下图显示了如何通过“撤销删除 Blob”操作还原软删除的 blob 版本，以及如何通过“复制 Blob”操作还原 blob 的当前版本 。

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="显示如何还原软删除的版本的示意图。":::

在软删除保留期结束之后，将永久删除任何软删除的 blob 版本。

## <a name="blob-versioning-and-blob-snapshots"></a>Blob 版本控制和 blob 快照

Blob 快照是在某一特定时间点拍摄的 blob 的只读副本。 Blob 快照和 blob 版本类似，但快照是由你或你的应用程序手动创建的，而 blob 版本是在为存储帐户启用 blob 版本控制后，在写入或删除操作中自动创建的。

> [!IMPORTANT]
> Microsoft 建议在启用 Blob 版本控制后，还应更新应用程序以停止为块 Blob 拍摄快照。 如果存储帐户启用了版本控制，则将按版本捕获和保留所有块 blob 更新和删除。 如果启用了 blob 版本控制，则拍摄快照不会向块 blob 数据提供任何额外的保护，并且可能会增加成本和应用程序复杂性。

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>启用版本控制时为 blob 拍摄快照

尽管不建议这样做，但也可以为接受版本控制的 blob 拍摄快照。 如果在启用版本控制时无法更新应用程序以停止为 blob 拍摄快照，则应用程序可以同时支持快照和版本。

为接受版本控制的 blob 拍摄快照时，会在创建快照的同时创建一个新版本。 在拍摄快照时，还会创建一个新的当前版本。

下图显示为接受版本控制的 blob 拍摄快照时会发生的情况。 在此示意图中，版本 ID 为 2 和 3 的 blob 版本和快照包含相同的数据。

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="显示接受版本控制的 blob 的快照的示意图。":::

## <a name="authorize-operations-on-blob-versions"></a>授予对 blob 版本的操作权限

可以使用以下方法之一来授予对 blob 版本的访问权限：

- 通过使用 Azure 基于角色的访问控制 (Azure RBAC) 向 Azure Active Directory (Azure AD) 安全主体授予权限。 Microsoft 建议使用 Azure AD 来实现更好的安全性和易用性。 有关通过 Blob 操作使用 Azure AD 的详细信息，请参阅[授权访问 Azure 存储中的数据](../common/authorize-data-access.md)。
- 通过使用共享访问签名 (SAS) 来委托对 blob 版本的访问权限。 为已签名的资源类型 `bv`（表示 blob 版本）指定版本 ID，以创建针对特定版本的操作的 SAS 令牌。 有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。
- 通过使用帐户访问密钥向具有共享密钥的 blob 版本授予操作权限。 有关详细信息，请参阅[通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key)。

Blob 版本控制旨在保护数据免遭意外或恶意删除。 为了增强保护，删除 blob 版本时需要特殊权限。 以下各节描述了删除 blob 版本所需的权限。

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>用于删除 blob 版本的 Azure RBAC 操作

下表显示了支持删除 blob 或 blob 版本的 Azure RBAC 操作。

| 说明 | Blob 服务操作 | 需要 Azure RBAC 数据操作 | Azure 内置角色支持 |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| 删除当前版本 | 删除 Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | 存储 Blob 数据参与者 |
| 删除先前版本 | 删除 Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action** | 存储 Blob 数据所有者 |

### <a name="shared-access-signature-sas-parameters"></a>共享访问签名 (SAS) 参数

Blob 版本的已签名资源为 `bv`。 有关详细信息，请参阅[创建服务 SAS](/rest/api/storageservices/create-service-sas) 或[创建用户委托 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下表显示了在 SAS 上删除 blob 版本时所需的权限。

| **权限** | **URI 符号** | **允许的操作** |
|----------------|----------------|------------------------|
| 删除         | x              | 删除 blob 版本。 |

## <a name="pricing-and-billing"></a>定价和计费

启用 blob 版本控制可能会导致帐户产生额外的数据存储费用。 在设计应用程序时，有必要了解在哪些情况下会产生这些费用，以便最大程度地减少费用。

Blob 版本和 blob 快照一样，按与活动数据相同的费率计费。 如何对版本进行计费取决于是否已为基本 blob 或其任何版本（或快照）显式设置层级。 有关 blob 层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md)。

如果尚未更改 blob 或版本的层级，则会针对该 blob、其版本及其拥有的任何快照中不重复的数据块进行计费。 有关详细信息，请参阅[在未显式设置 blob 层级时进行计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)。

如果已更改 blob 或版本的层级，则会对整个对象进行计费，而不考虑 blob 和版本是否最终在同一层级中。 有关详细信息，请参阅[在显式设置了 blob 层级时进行计费](#billing-when-the-blob-tier-has-been-explicitly-set)。

> [!NOTE]
> 为频繁覆盖的数据启用版本控制可能会增加存储容量费用，并增加列出操作期间的延迟。 若要缓解这些问题，请将频繁覆盖的数据存储在禁用了版本控制的单独存储帐户中。

有关 blob 快照的计费详细信息，请参阅 [Blob 快照](snapshots-overview.md)。

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>在未显式设置 blob 层级时进行计费

如果没有为基本 blob 或其任何版本显式设置 blob 层级，则会针对该 blob、其版本及其拥有的任何快照中不重复的块或页面进行计费。 对于跨 blob 及其版本共享的数据，只会收费一次。 当 blob 更新时，基本 blob 中的数据与其版本中存储的数据将会相异，并且会按块或页面对不重复的数据进行计费。

在替换块 Blob 中的某个块后，会将该块作为唯一块进行收费。 即使该块具有的块 ID 和数据与它在先前版本中所具有的 ID 和数据相同也是如此。 重新提交块后，它将与它在先前版本中的对应部分相异，并且你要为其数据付费。 对于使用相同数据更新的页 Blob 中的页面来说，情况也是如此。

Blob 存储无法确定两个块是否包含相同的数据。 每个上传和提交的块均被视为唯一的块，即使它具有相同的数据和块 ID 也是如此。 由于不重复的块会产生费用，因此请务必记住，在启用版本控制后更新 Blob 将导致产生更多不重复的块和额外费用。

启用 blob 版本控制后，请对块 blob 调用更新操作，以使它们更新尽可能少的块。 允许对块进行精细控制的写入操作是[放置块](/rest/api/storageservices/put-block)和[放置块列表](/rest/api/storageservices/put-block-list)。 另一方面，[放置 Blob](/rest/api/storageservices/put-blob) 操作会替换 blob 的全部内容，因此可能会导致额外的费用。

下列方案说明了未显式设置 blob 层级时块 Blob 及其版本将如何产生费用。

#### <a name="scenario-1"></a>方案 1

在方案 1 中，blob 具有先前版本。 自该版本创建以来，blob 尚未更新，因此仅针对不重复的块 1、2 和 3 产生费用。

![图 1 显示了如何对基本 blob 和先前版本中不重复的块进行计费。](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>方案 2

在方案 2 中，blob 中的一个块（图中的块 3）已更新。 即使更新的块包含相同的数据和 ID，它也与先前版本中的块 3 不同。 因此，帐户需要为四个块支付费用。

![图 2 显示了如何对基本 blob 和先前版本中不重复的块进行计费。](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>方案 3

在方案 3 中，已更新 blob，但未更新版本。 块 3 已替换为基础 blob 中的块 4，但先前版本仍反映块 3。 因此，帐户需要为四个块支付费用。

![图 3 显示了如何对基本 blob 和先前版本中不重复的块进行计费。](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>方案 4

在方案 4 中，已完全更新基本 Blob，并且其中不包含任何原始块。 因此，该帐户将为所有八个不重复的块 &mdash; 付费，其中四个在基本 blob 中，四个在先前版本中。 如果使用[放置 Blob](/rest/api/storageservices/put-blob) 操作写入 blob，则会发生这种情况，因为它会替换基本 blob 的全部内容。

![图 4 显示了如何对基本 blob 和先前版本中不重复的块进行计费。](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>在显式设置了 blob 层级时进行计费

如果为 blob 或版本（或快照）显式设置了 blob 层级，则会针对新层级中对象的完整内容长度计费，而不考虑它是否与原始层级中的对象共享块。 你还需要为原始层级中最早版本的完整内容长度付费。 对于保留在原始层级中的任何其他先前版本或快照，都将针对其可能共享的不重复的块进行计费，如[在未显式设置 blob 层级时进行计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

#### <a name="moving-a-blob-to-a-new-tier"></a>将 blob 移到新层级

下表描述了将 blob 或版本移动到新层级时的计费行为。

| 当针对以下项显式设置了 blob 层级时... | 你需要为以下项付费 |
|-|-|
| 具有先前版本的基本 blob | 新层级中的基本 blob 和原始层级中的最早版本，以及其他版本中的任何不重复的块。<sup>1</sup> |
| 具有先前版本和快照的基本 blob | 新层级中的基本 blob、原始层级中的最早版本、原始层级中的最早快照，以及其他版本或快照中的任何不重复的块<sup>1</sup>。 |
| 先前版本 | 新层级中的版本和原始层级中的基本 blob，以及其他版本中的任何不重复的块。<sup>1</sup> |

<sup>1</sup>如果有其他先前版本或快照尚未从其原始层级中移出，则会根据这些版本或快照包含的不重复的块的数量对它们进行收费，如[在未显式设置 blob 层级时进行计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

下图说明了当接受版本控制的 blob 移到另一个层级时，如何针对对象进行计费。

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="此图显示了当对接受版本控制的 blob 显式分层时，如何针对对象进行计费。":::

为 blob、版本或快照显式设置层级不能撤消。 如果你将 blob 移动到新层级，然后将其移回其原始层级，则即使该对象与原始层级中的其他对象共享块，也会针对该对象的完整内容长度向你收费。

显式设置 blob、版本或快照的层级的操作包括：

- [设置 Blob 层](/rest/api/storageservices/set-blob-tier)
- 在指定了层级的情况下[放置 Blob](/rest/api/storageservices/put-blob)
- 在指定了层级的情况下[放置块列表](/rest/api/storageservices/put-block-list)
- 在指定了层级的情况下[复制 Blob](/rest/api/storageservices/copy-blob)

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>在启用了软删除的情况下删除 blob

当启用了 blob 软删除时，如果你删除或覆盖已显式设置其层级的基本 blob，则软删除的 blob 的任何先前版本都将按完整内容长度进行计费。 有关如何结合使用 blob 版本控制和软删除的详细信息，请参阅 [Blob 版本控制和软删除](#blob-versioning-and-soft-delete)。

下表描述了软删除的 blob 的计费行为，具体取决于是启用还是禁用了版本控制。 启用版本控制后，软删除 blob 时将创建一个新版本。 禁用版本控制后，软删除 blob 时将创建软删除快照。

| 当你覆盖已显式设置了其层级的基本 blob 时... | 你需要为以下项付费 |
|-|-|
| 如果同时启用了 blob 软删除和版本控制 | 所有现有版本（按完整内容长度），不考虑层级。 |
| 如果启用了 blob 软删除但禁用了版本控制 | 所有现有软删除快照（按完整内容长度），不考虑层级。 |

## <a name="feature-support"></a>功能支持

此表显示了你的帐户如何支持此功能，以及当你启用某些功能时对支持的影响。 

| 存储帐户类型                | Blob 存储（默认支持）   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>    
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) |![否](../media/icons/no-icon.png)              | ![否](../media/icons/no-icon.png) | 
| 高级块 blob          | ![是](../media/icons/yes-icon.png) |![否](../media/icons/no-icon.png)              | ![否](../media/icons/no-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

## <a name="see-also"></a>另请参阅

- [启用和管理 blob 版本控制](versioning-enable.md)
- [创建 blob 的快照](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure 存储 Blob 的软删除](./soft-delete-blob-overview.md)