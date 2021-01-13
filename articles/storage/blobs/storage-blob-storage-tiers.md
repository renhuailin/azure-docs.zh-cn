---
title: Azure Blob 存储的访问层 - 热、冷和存档
description: 了解 Azure Blob 存储的热、冷和存档访问层。 查看支持分层的存储帐户。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 570fd7f356969bbd37ef6b661334501a062c36df
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165682"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Azure Blob 存储的访问层 - 热、冷和存档

Azure 存储提供不同的访问层，使你能够以最经济高效的方式存储 blob 对象数据。 可用的访问层包括：

- **热**：适用于存储经常访问的数据。
- **冷**：适用于存储不常访问且存储时间至少为 30 天的数据。
- **存档** 优化，用于存储在至少180天内很少访问和存储的数据，并按小时顺序提供灵活的延迟要求。

以下注意事项适用于不同的访问层：

- 可以在上载期间或之后在 blob 上设置访问层。
- 在帐户级别只能设置热和冷访问层。 存档访问层只能在 blob 级别设置。
- 冷访问层中的数据略有降低的可用性，但仍具有高耐用性、检索延迟以及与热数据类似的吞吐量特征。 对于冷数据，使用略微降低的可用性和更高的访问成本是与热数据相比降低总体存储成本的可接受折衷方案。 有关详细信息，请参阅[存储的 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)。
- 存档访问层中的数据脱机存储。 存档层提供最低的存储成本，同时还提供最高的访问成本和延迟。
- "热" 和 "冷" 层支持所有冗余选项。 存档层仅支持 LRS、GRS 和 GRS。
- 数据存储限制是在帐户级别设置的，而不是按访问层设置的。 可以选择在一个层中用完所有存储配额，也可以分散用于三个层。

存储在云中的数据以指数速度增长。 若要针对不断增加的存储需求来管理成本，可以根据属性（如访问频率和计划保留期）整理数据以优化成本。 存储在云中的数据可能根据其生成方式、处理方式以及在生存期内的访问方式而有所不同。 某些数据在其整个生存期中都会受到积极的访问和修改。 某些数据则在生存期早期会受到频繁访问，随着数据变旧，访问会极大地减少。 某些数据在云中保持空闲状态，并且在存储后很少（如果有）被访问。

这些数据访问方案的每一个都受益于针对特定访问模式进行了优化的差异化访问层。 Azure Blob 存储采用热、冷和存档访问层，通过单独的定价模型来满足对差异化访问层的这种需要。

下面的工具和客户端库都支持 blob 级别的分层和存档存储。

- Azure 门户
- PowerShell
- Azure CLI 工具
- .NET 客户端库
- Java 客户端库
- Python 客户端库
- Node.js 客户端库

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>支持分层的存储帐户

在 "热"、"冷" 和 "存档" 之间的对象存储数据分层支持在 Blob 存储和常规用途 v2 (GPv2) 帐户中。 常规用途 v1 (GPv1) 帐户不支持分层。 可以通过 Azure 门户轻松地将现有的 GPv1 或 Blob 存储帐户转换为 GPv2 帐户。 GPv2 为 Blob、文件和队列提供新的定价与功能。 某些功能和价格削减仅在 GPv2 帐户中提供。 某些工作负荷的价格在 GPv2 中可能比在 GPv1 中更高。 有关详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

Blob 存储和 GPv2 帐户在帐户级别公开“访问层”属性。 使用此属性，可以为未在对象级别显式设置的任何 blob 指定默认访问层。 对于显式设置了层的对象，帐户层将不适用。 存档层仅适用于对象级别。 你可以随时在访问层之间切换。

使用 GPv2 而不是 Blob 存储帐户进行分层。 GPv2 支持 Blob 存储帐户支持的所有功能，此外还有更多的功能。 Blob 存储和 GPv2 之间的定价几乎完全相同，但一些新功能和价格削减仅适用于 GPv2 帐户。

GPv1 和 GPv2 帐户的定价结构不同，客户在决定使用 GPv2 帐户之前，应仔细评估这二者。 只需单击一下，即可轻松地将现有的 Blob 存储或 GPv1 帐户转换为 GPv2 帐户。 有关详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

## <a name="hot-access-tier"></a>热访问层

热访问层的存储成本高于冷存储和存档层，但访问成本最低。 热访问层的示例使用方案包括：

- 处于活动使用状态或预期会频繁地读取和写入数据
- 暂存用于处理和最终迁移到冷访问层的数据

## <a name="cool-access-tier"></a>冷访问层

与热存储相比，冷访问层的存储成本较低，访问成本较高。 此层适用于将要保留在冷层中至少 30 天的数据。 冷访问层的示例使用方案包括：

- 短期备份和灾难恢复
- 较旧的数据不经常使用，但在被访问时应立即可用
- 需要更有效地存储的大型数据集，同时收集更多的数据以供将来处理

## <a name="archive-access-tier"></a>存档访问层

与热层和冷层相比，存档访问层的存储成本最低，但数据检索费用较高。 存档层中的数据必须至少保留 180 天，否则需要支付提前删除费。 根据指定的解除冻结优先级，存档层中的数据可能需要几个小时来检索。 对于小对象，高优先级解除冻结可能会在一小时内从存档中检索对象。 若要了解详细信息，请参阅[从存档层解冻 Blob 数据](storage-blob-rehydration.md)。

Blob 在存档存储中时，blob 数据处于脱机状态，无法读取或修改。 若要在存档中读取或下载 Blob，必须首先将其解除冻结到联机层。 不能创建存档存储中 Blob 的快照。 但是，Blob 元数据会保持联机和可用状态，因而可列出 Blob 及其属性、元数据和 blob 索引标记。 不允许在存档时设置或修改 blob 元数据。 但是，您可以设置和修改 blob 索引标记。 对于存档中的 blob，唯一有效的操作是 [获取 Blob 属性](/rest/api/storageservices/get-blob-properties)、 [获取 blob 元数据](/rest/api/storageservices/get-blob-metadata)、 [设置 Blob 标记](/rest/api/storageservices/set-blob-tags)、 [获取 Blob 标记](/rest/api/storageservices/get-blob-tags)、 [按标记查找 blob](/rest/api/storageservices/find-blobs-by-tags)、 [列出 blob](/rest/api/storageservices/list-blobs)、 [设置 Blob 层](/rest/api/storageservices/set-blob-tier)、 [复制 blob](/rest/api/storageservices/copy-blob)和 [删除 blob](/rest/api/storageservices/delete-blob)。

存档访问层的示例使用方案包括：

- 长期备份、辅助备份和存档数据集
- 原始 (原始) 必须保留的数据，即使在将其处理为最终可用格式后也是如此
- 需要长时间存储并且几乎不能访问的相容性和存档数据

> [!NOTE]
> ZRS、GZRS 或 GZRS 帐户不支持存档层。

## <a name="account-level-tiering"></a>帐户级分层

所有三个访问层中的 Blob 可以在同一帐户中共存。 如果 Blob 没有显式分配的层，则会从帐户访问层设置推断相应的层。 如果访问层来自帐户，则你可以看到“推断的访问层”Blob 属性已设置为“true”，而“访问层”Blob 属性与帐户层匹配。  在 Azure 门户中，Blob 访问层的“推断访问层”属性显示为“热(推断)”或“冷(推断)”。 

更改帐户访问层适用于帐户中存储的未设置显式层的所有“推断访问层”对象。 如果将帐户层从热切换为冷，则只按 GPv2 帐户中没有设置层的所有 Blob 的写入操作次数（以 10,000 次为单位）收费。 不会在 Blob 存储帐户中对此更改收费。 如果在 Blob 存储或 GPv2 帐户中从冷切换为热，则会按读取操作次数（以 10,000 次为单位）和数据检索量（以 GB 为单位）收费。

只有热访问层和冷访问层可以设置为默认帐户访问层。 只能在对象级别设置存档层。 在 blob 上传时，可以将所选的访问层指定为 "热"、"冷" 或 "存档"，而不考虑默认的帐户层。 使用此功能可以将数据直接写入存档层，从而从在 Blob 存储中创建数据的那一刻起就实现了节省成本。

## <a name="blob-level-tiering"></a>Blob 级别分层

Blob 级分层允许使用 "[设置 Blob 层](/rest/api/storageservices/set-blob-tier)" 操作或[生命周期管理](#blob-lifecycle-management)功能，将数据上传到所选的访问层，使用 " [Put Blob](/rest/api/storageservices/put-blob) " 或 " [put 块列表](/rest/api/storageservices/put-block-list)" 操作并在对象级别更改数据层。 可以将数据上传到所需的访问层，然后在使用模式更改时轻松地在热、冷或存档层之间更改 Blob 访问层，不需在帐户之间移动数据。 所有层更改请求会立即发生，热和冷之间的层更改是即时的。 解除冻结存档层中的 blob 可能需要几个小时。

上次 Blob 层更改的时间通过 Blob 属性“访问层更改时间”公开。 覆盖热层或冷层中的 blob 时，除非在创建时显式设置了新的 blob 访问层，否则新创建的 blob 将继承被覆盖的 blob 的层的属性。 如果 Blob 位于存档层中，则无法被覆盖，因此在这种情况下，不允许上传相同的 Blob。

> [!NOTE]
> 存档存储和 Blob 级别分层仅支持块 Blob。

### <a name="blob-lifecycle-management"></a>Blob 生命周期管理

Blob 存储生命周期管理提供了一种基于规则的丰富策略，可用于将数据转换为最佳访问层并使数据在其生命周期结束时过期。 若要了解详细信息，请参阅 [通过自动化 Azure Blob 存储访问层优化成本](storage-lifecycle-management-concepts.md) 。

> [!NOTE]
> 存储在块 Blob 存储帐户（高级性能）中的数据目前无法使用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)或使用 Azure Blob 存储生命周期管理分层到热、冷或存档访问层。
> 若要移动数据，必须使用[通过 URL 放置块 API](/rest/api/storageservices/put-block-from-url) 或支持此 API 的 AzCopy 版本，将块 Blob 存储帐户中的 Blob 同步复制到其他帐户中的热访问层。
> **通过 URL 放置块** API 同步复制服务器上的数据，这意味着只有在所有数据都从原服务器位置移动到目标位置后，调用才会完成。

### <a name="blob-level-tiering-billing"></a>Blob 级别分层计费

当在层之间上传或移动 blob 时，它将在上传或层更改时立即按相应的费率收费。

将 blob 移到更冷的层（热->冷、热->存档或冷->存档）时，操作按目标层写入操作计费，具体说来就是按目标层的写入操作次数（以 10,000 次为单位）和数据写入量（以 GB 为单位）收费。

将 Blob 移到更暖的层（存档->冷、存档->热或冷->热）时，操作按从源层读取计费，具体说来就是按源层的读取操作次数（以 10,000 次为单位）和数据检索量（以 GB 为单位）收费。 从冷层或存档层移出的任何 blob 的[早期删除](#cool-and-archive-early-deletion)费用也可能适用。 [存档层中的解除冻结数据](storage-blob-rehydration.md) 需要一段时间，数据将按存档价格计费，直到数据联机并且 blob 层更改为 "热" 或 "冷"。

下表总结了如何对层更改进行计费。

| | **(操作 + 访问) 写入费用** | **读取 (操作 + 访问) 费用** |
| ---- | ----- | ----- |
| **设置 Blob 层** | 热 > 超酷<br> 热 > 存档<br> 冷 > 存档 | 存档-> 超酷<br> 存档-> 热<br> 冷 > 热

### <a name="cool-and-archive-early-deletion"></a>“冷”层和“存档”层提前删除

移到冷层（仅限 GPv2 帐户）中的 Blob 会有一个 30 天的冷层提前删除期限。 移到存档层中的 Blob 会有一个 180 天的存档提前删除期限。 此项费用按比例计算。 例如，如果将某个 Blob 移到存档层，然后在 45 天后将其删除或移到热层，则需支付相当于将该 Blob 存储在存档层中 135（180 减 45）天的提前删除费用。

在 "冷" 和 "存档" 层之间移动时，有一些详细信息：

1. 如果根据存储帐户的默认访问层将 blob 推断为 "冷"，并将 blob 移到 "存档"，则不会收取早期删除费用。
1. 如果将 blob 显式移动到 "冷" 层，然后将其移动到 "存档"，则会应用早期删除费用。

如果没有任何访问层发生更改，则使用 **最后修改** 的 blob 属性来计算早期的删除时间。 否则，请在访问层上次修改为冷或存档时使用，方法是查看 blob 属性： **访问层-更改时间**。 有关 Blob 属性的详细信息，请参阅[获取 Blob 属性](/rest/api/storageservices/get-blob-properties)。

## <a name="comparing-block-blob-storage-options"></a>比较块 Blob 存储选项

下表对高级性能块 blob 存储与热、冷、存档访问层进行了比较。

|                                           | **高级性能**   | **热存储层** | **冷存储层**       | **存档存储层**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **可用性**                          | 99.9%                     | 99.9%        | 99%                 | Offline           |
| **可用性** <br> （RA-GRS 读取）  | 空值                       | 99.99%       | 99.9%               | Offline           |
| 使用费                         | 存储费用较高，访问和事务费用较低 | 存储费用较高，访问和事务费用较低 | 存储费用较低，访问和事务费用较高 | 存储费用最低，访问和事务费用最高 |
| 最短存储持续时间              | 空值                       | 空值          | 30 天<sup>1</sup> | 180 天
| **延迟** <br> （距第一字节时间） | 一位数的毫秒数 | 毫秒 | 毫秒        | 小时<sup>2</sup> |

<sup>1</sup> GPv2 帐户冷层中的对象的最短保留期为 30 天。 Blob 存储帐户的冷层没有最短保留期。

<sup>2</sup> 存档存储当前支持两个解除冻结优先级：高和标准，提供不同的检索延迟和成本。 有关详细信息，请参阅[从存档层解冻 Blob 数据](storage-blob-rehydration.md)。

> [!NOTE]
> Blob 存储帐户支持与常规用途 v2 存储帐户相同的性能和可伸缩性目标。 有关详细信息，请参阅 [Blob 存储可伸缩性和性能目标](scalability-targets.md)。

## <a name="pricing-and-billing"></a>定价和计费

所有存储帐户都根据每个 blob 的层使用块 blob 存储的定价模型。 请记住以下计费注意事项：

- **存储成本**：除了存储的数据量，存储数据的成本将因访问层而异。 层越冷，单 GB 成本越低。
- **数据访问成本**：层越冷，数据访问费用越高。 对于冷访问层和存档访问层中的数据，需要按 GB 支付读取方面的数据访问费用。
- **事务成本**：层越冷，每个层的按事务收费越高。
- **异地复制数据传输费用**：此费用仅适用于配置了异地复制的帐户，包括 GRS 和 GRS。 异地复制数据传输会产生每 GB 费用。
- **出站数据传输成本**：出站数据传输（传出 Azure 区域的数据）会按每 GB 产生带宽使用费，与通用存储帐户一致。
- **更改访问层**：更改帐户访问层将导致未设置显式层的所有 blob 的层更改费用。 有关更改单个 blob 的访问层的信息，请参阅 [blob 级别分层计费](#blob-level-tiering-billing)。

    如果在启用了版本控制的情况下更改了 blob 的访问层，或者 blob 具有快照，则可能会产生额外费用。 有关启用了版本控制的 blob 的信息，请参阅 blob 版本控制文档中的 [定价和计费](versioning-overview.md#pricing-and-billing) 。 有关包含快照的 blob 的信息，请参阅 blob 快照文档中的 [定价和计费](snapshots-overview.md#pricing-and-billing) 。

> [!NOTE]
> 有关块 blob 定价的详细信息，请参阅 [块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。 有关出站数据传输费用的详细信息，请参阅 [带宽定价详细](https://azure.microsoft.com/pricing/details/bandwidth/) 信息页。

## <a name="availability"></a>可用性

选择区域中提供了不同的访问层和 blob 级别分层。 如需完整列表，请参阅 [Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。

## <a name="next-steps"></a>后续步骤

了解如何跨访问层管理 blob 和帐户。

- [如何管理 Azure 存储帐户中 blob 的层](manage-access-tier.md)
- [如何管理 Azure 存储帐户的默认帐户访问层](../common/manage-account-default-access-tier.md)
- [通过自动执行 Azure Blob 存储访问层来优化成本](storage-lifecycle-management-concepts.md)
