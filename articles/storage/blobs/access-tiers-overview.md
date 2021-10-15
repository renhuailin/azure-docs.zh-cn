---
title: Blob 数据的热访问层、冷访问层和存档访问层
titleSuffix: Azure Storage
description: Azure 存储提供不同的访问层，以便你可以基于使用方式，以最具成本效益的方式存储 Blob 数据。 了解 Blob 存储的热访问层、冷访问层和存档访问层。
author: tamram
ms.author: tamram
ms.date: 09/29/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: ee0a9714f305a63ae3f25628f1bd9e9b14ce6e01
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293491"
---
# <a name="hot-cool-and-archive-access-tiers-for-blob-data"></a>Blob 数据的热访问层、冷访问层和存档访问层

存储在云中的数据以指数速度增长。 要管理不断扩展的存储需求产生的成本，根据访问频率和保留期整理数据将很有帮助。 Azure 存储提供不同的访问层，以便你可以基于使用方式，以最具成本效益的方式存储 Blob 数据。 Azure 存储访问层包括：

- 热层 - 适用于存储经常访问或修改的数据的联机层。 热层的存储成本最高，但访问成本最低。
- 冷层 - 适用于存储不经常访问或修改的数据的联机层。 冷层中的数据应至少存储 30 天。 与热层相比，冷层的存储成本较低，访问成本较高。
- 存档层 - 适用于存储极少访问且延迟要求（以小时计）不严格的数据的脱机层。 存档层中的数据应至少存储 180 天。

Azure 存储容量限制在帐户级别设置，而不是根据访问层设置。 可以选择在一个层中最大化容量使用率，也可以在两个或更多层中分布容量。

## <a name="online-access-tiers"></a>联机访问层

当你的数据存储在联机访问层（热层或冷层）中时，用户可以立即访问它。 热层是正在使用的数据的最佳选择，而冷层适用于不常访问但仍必须可用于读取和写入的数据。

热层的示例使用方案包括：

- 处于活跃使用状态或预期会频繁读取和写入的数据。
- 分阶段进行处理，并最终迁移至冷访问层的数据。

冷访问层的使用方案包括：

- 短期数据备份和灾难恢复。
- 不经常使用但应立即可用的较旧数据集。
- 需要经济高效地进行存储并且要收集其他数据以进行处理的大型数据集。

冷层中的数据的可用性略低，但提供与热层相同的高持久性、检索延迟和吞吐量特征。 对于冷层中的数据，略低的可用性和较高的访问成本（与热层相比）对于更低的整体存储成本而言是可接受的折衷。 有关详细信息，请参阅[存储的 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)。

如果在删除常规用途 v2 帐户冷层中的某个 Blob 或将其移动到其他层时时间不足 30 天，则会受到提前删除惩罚。 此项费用按比例计算。 例如，如果将某个 Blob 移到冷层，然后在 21 天后将其删除，则需支付相当于将该 Blob 存储在冷层中 9（30 减 21）天的提前删除费用。

热层和冷层支持所有冗余配置。 若要详细了解 Azure 存储中的数据冗余选项，请参阅 [Azure 存储冗余](../common/storage-redundancy.md)。  

## <a name="archive-access-tier"></a>存档访问层

存档层是用于存储极少访问的数据的脱机层。 与热层和冷层相比，存档访问层的存储成本最低，但数据检索成本和延迟较高。 存档访问层的示例使用方案包括：

- 长期备份、辅助备份和存档数据集
- 必须保留的原始数据，即使它已处理成最终可用的形式
- 需要长时间存储并且几乎不访问的合规性和存档数据

存档层中的数据必须至少保留 180 天，否则需要支付提前删除费。 例如，如果将某个 Blob 移到存档层，然后在 45 天后将其删除或移到热层，则需支付相当于将该 Blob 存储在存档层中 135（180 减 45）天的提前删除费用。

当 Blob 位于存档层时，它无法读取或修改。 若要读取或下载存档层中的 Blob，必须先将 Blob 解除冻结到联机层（热层或冷层）。 存档层中的数据可能最多需要 15 小时才能解除冻结。 有关 Blob 解除冻结的详细信息，请参阅[存档层中的 Blob 解除冻结概述](archive-rehydrate-overview.md)。

存档 Blob 的元数据仍可供读取访问，以便你可以列出 Blob 及其属性、元数据和索引标记。 存档层中的 Blob 的元数据是只读的，但可以读取或写入 Blob 索引标记。 存档 Blob 不支持快照。

存档层中的 Blob 支持以下操作：

- [获取 Blob 属性](/rest/api/storageservices/get-blob-properties)
- [获取 Blob 元数据](/rest/api/storageservices/get-blob-metadata)
- [设置 Blob 标记](/rest/api/storageservices/set-blob-tags)
- [获取 Blob 标记](/rest/api/storageservices/get-blob-tags)
- [按标记查找 Blob](/rest/api/storageservices/find-blobs-by-tags)
- [列出 Blob](/rest/api/storageservices/list-blobs)
- [设置 Blob 层](/rest/api/storageservices/set-blob-tier)
- [复制 Blob](/rest/api/storageservices/copy-blob)
- [从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url)
- [删除 Blob](/rest/api/storageservices/delete-blob)

> [!NOTE]
> ZRS、GZRS 或 RA-GZRS 帐户不支持存档层。 支持从 LRS 迁移到 GRS，前提是帐户设置为 LRS 时，没有 blob 被移动到存档层。 如果在帐户成为 LRS 后的 30 天内执行更新，并且在帐户设置为 LRS 时没有 Blob 被移动到存档层，则可以将该帐户移回到 GRS。

## <a name="default-account-access-tier-setting"></a>默认帐户访问层设置

存储帐户有一个默认访问层设置，表示创建新的 Blob 的联机层。 默认访问层设置可以设置为热层或冷层。 用户可以在上传 Blob 或更改其层时覆盖单个 Blob 的默认设置。

默认情况下，新存储帐户的默认访问层设置为热层。 创建存储帐户时或创建存储帐户后，可以更改默认访问层设置。 如果不更改此设置，则默认情况下会将 Blob 上传到热层。

如果 Blob 没有显式分配的层，则会从默认帐户访问层设置推断相应的层。 如果 Blob 的访问层是根据默认帐户访问层设置推断得出的，则 Azure 门户会将访问层显示为“热(推断)”或“冷(推断)”。

更改默认帐户访问层设置适用于帐户中尚未显式设置访问层的所有 Blob。 如果在常规用途 v2 帐户中将默认帐户访问层设置从热切换为冷，则按推断访问层的所有 Blob 的写入操作次数（以 10,000 次为单位）收费。 不会在 Blob 存储帐户中对此更改收费。 如果在常规用途 v2 或 Blob 存储帐户中从冷切换为热，则会按读取操作次数（以 10,000 次为单位）和数据检索量（以 GB 为单位）收费。

> [!NOTE]
> 存档层不支持作为存储帐户的默认访问层。

## <a name="setting-or-changing-a-blobs-tier"></a>设置或更改 Blob 的层

若要在创建 Blob 时显式设置其层，请在上传 Blob 时指定层。

创建 Blob 后，可以通过以下两种方式之一更改其层：

- 直接或通过[生命周期管理](#blob-lifecycle-management)策略调用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)操作。 将 Blob 的层从较热的层更改为较冷的层时，调用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)通常是最佳选择。
- 调用[复制 Blob](/rest/api/storageservices/copy-blob) 操作以将 Blob 从一个层复制到另一个层。 对于大多数将 Blob 从存档层解除冻结到联机层或将 Blob 从冷层移动到热层的方案中，建议调用[复制 Blob](/rest/api/storageservices/copy-blob)。 如果源 Blob 的所需存储间隔尚未过去，则可以通过复制 Blob 来避免提前删除惩罚。 但是，复制 Blob 会导致产生两个 Blob，即源 Blob 和目标 Blob 的容量费用。

将 Blob 的层从热层更改为冷层或存档层是即时的，就像从冷层更改为热层一样。 将 Blob 从存档层解除冻结到热层或冷层可能最多需要 15 小时。

在冷层和存档层之间移动 Blob 时，请注意以下几点：

- 如果根据存储帐户的默认访问层将 Blob 推断为冷层，并将 Blob 移动到存档层，则不会收取提前删除费用。
- 如果将 Blob 显式移动到冷层，然后将其移动到存档层，则将收取提前删除费用。

## <a name="blob-lifecycle-management"></a>Blob 生命周期管理

Blob 存储生命周期管理提供基于规则的策略，用于在满足指定条件时将数据转移到所需的访问层。 还可以使用生命周期管理在生命周期结束时使数据过期。 请参阅[通过自动执行 Azure Blob 存储访问层来优化成本](./lifecycle-management-overview.md)来了解详细信息。

> [!NOTE]
> 存储在高级块 Blob 存储帐户中的数据无法使用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)或使用 Azure Blob 存储生命周期管理分层到热、冷或存档访问层。 若要移动数据，必须使用[通过 URL 放置块 API](/rest/api/storageservices/put-block-from-url) 或支持此 API 的 AzCopy 版本，将块 Blob 存储帐户中的 Blob 同步复制到其他帐户中的热层。 **通过 URL 放置块** API 同步复制服务器上的数据，这意味着只有在所有数据都从原服务器位置移动到目标位置后，调用才会完成。

## <a name="summary-of-access-tier-options"></a>访问层选项摘要

下表汇总了热、冷和存档访问层的功能。

|  | **热存储层** | **冷存储层** | **存档存储层** |
|--|--|--|--|
| **可用性** | 99.9% | 99% | Offline |
| **可用性** <br> （RA-GRS 读取） | 99.99% | 99.9% | Offline |
| 使用费 | 存储成本较高，但访问和事务成本较低 | 存储成本较低，但访问和事务成本较高 | 存储成本最低，但访问和事务成本最高 |
| **建议的最短数据保留期** | 空值 | 30 天<sup>1</sup> | 180 天 |
| **延迟** <br> （距第一字节时间） | 毫秒 | 毫秒 | 小时<sup>2</sup> |
| **受支持的冗余配置** | 全部 | 全部 | 仅限 LRS、GRS 和 RA-GRS<sup>3</sup> |

<sup>1</sup> 常规用途 v2 帐户冷层中的对象的最短保留期为 30 天。 对于 Blob 存储帐户，冷层没有最短保留期。

<sup>2</sup> 从存档层解除冻结 Blob 时，可以选择标准或高解除冻结优先级选项。 每个选项的检索延迟和成本有所不同。 有关详细信息，请参阅[存档层中的 Blob 解除冻结概述](archive-rehydrate-overview.md)。

<sup>3</sup> 有关 Azure 存储中的冗余配置的详细信息，请参阅 [Azure 存储冗余](../common/storage-redundancy.md)。

## <a name="pricing-and-billing"></a>定价和计费

所有存储帐户使用的定价模型都适用于块 Blob 存储，具体取决于 Blob 的层。 请记住以下部分中所述的计费注意事项。

有关块 Blob 定价的详细信息，请参阅[块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

### <a name="storage-capacity-costs"></a>存储容量成本

除了存储的数据量，存储数据的成本将因访问层而异。 层越冷，每 GB 容量成本越低。

### <a name="data-access-costs"></a>数据访问成本

层越冷，数据访问费用越高。 对于冷访问层和存档访问层中的数据，需要按 GB 支付读取方面的数据访问费用。

### <a name="transaction-costs"></a>事务成本

层越冷，每个层的按事务收费越高。

### <a name="geo-replication-data-transfer-costs"></a>异地复制数据传输成本

此费用仅适用于配置了异地复制的帐户，包括 GRS 和 RA-GRS。 异地复制数据传输会产生每 GB 费用。

### <a name="outbound-data-transfer-costs"></a>出站数据传输成本

出站数据传输（从 Azure 区域传输出的数据）需按每 GB 的带宽使用量计费。 有关出站数据传输费用的详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)页。

### <a name="changing-the-default-account-access-tier"></a>更改默认帐户访问层

更改帐户访问层会导致尚未显式设置层的所有 Blob 产生层更改费。 有关详细信息，请参阅以下部分：[更改 Blob 的访问层](#changing-a-blobs-access-tier)。

### <a name="changing-a-blobs-access-tier"></a>更改 Blob 的访问层

更改 Blob 的层时，请记住以下计费影响：

- 在各个层之间上传或移动 blob 时，系统会在上传时或层发生更改时立即按相应的费率收费。
- 将 Blob 移到更冷的层（热到冷、热到存档或冷到存档）时，操作按目标层写入操作计费，具体来说就是按目标层的写入操作次数（以 10,000 次为单位）和数据写入量（以 GB 为单位）收费。
- 将 Blob 移到更暖的层（存档到冷、存档到热或冷到热）时，操作按从源层读取计费，具体来说就是按源层的读取操作次数（以 10,000 次为单位）和数据检索量（以 GB 为单位）收费。 也可能还会收取从池或存档层移出的任何 Blob 的早期删除费用。
- 从存档层解除冻结某个 Blob 时，该 Blob 的数据将按存档数据收费，直到数据还原且 Blob 的层更改为热层或冷层为止。

下表总结了如何对层更改进行计费。

| | 写入费用（操作 + 访问） | 读取费用（操作 + 访问） |
| ---- | ----- | ----- |
| “设置 Blob 层”操作 | 热到冷<br> 热到存档<br> 冷到存档 | 存档到冷<br> 存档到热<br> 冷到热

当版本控制已启用或 Blob 包含快照时，更改 Blob 的访问层可能会产生额外费用。 有关启用了版本控制的 blob 的信息，请参阅 blob 版本控制文档中的[定价和计费](versioning-overview.md#pricing-and-billing)。 有关包含快照的 Blob 的信息，请参阅 Blob 快照文档中的[定价和计费](snapshots-overview.md#pricing-and-billing)。

## <a name="feature-support"></a>功能支持

下表显示你的帐户如何支持此功能，以及启用某些功能后对支持的影响。

| 存储帐户类型 | Blob 存储（默认支持） | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|--|--|--|--|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| 高级块 blob | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

有关功能支持（按区域）的信息，请参阅[可用的 Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。

## <a name="next-steps"></a>后续步骤

- [如何管理 Azure 存储帐户中 Blob 的层](manage-access-tier.md)
- [如何管理 Azure 存储帐户的默认帐户访问层](../common/manage-account-default-access-tier.md)
- [通过自动执行 Azure Blob 存储访问层来优化成本](./lifecycle-management-overview.md)
