---
title: 对象复制概述
titleSuffix: Azure Storage
description: 对象复制在源存储帐户和目标帐户之间异步复制块 blob。 使用对象复制可最大程度地降低读取请求延迟，提高计算工作负载的效率，优化数据分发，并最大限度地降低成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dce411e6359a760f50dd4bbbbeba369e2a67f386
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254206"
---
# <a name="object-replication-for-block-blobs"></a>块 blob 的对象复制

对象复制在源存储帐户和目标帐户之间异步复制块 blob。 对象复制支持的部分方案包括：

- **最大程度地降低延迟。** 通过使客户端可以从物理位置更接近的区域使用数据，对象复制可以降低读取请求的延迟。
- **提高计算工作负载的效率。** 通过对象复制，计算工作负载可以在不同区域中处理相同的块 blob 集。
- **优化数据分发。** 您可以在单个位置处理或分析数据，然后仅将结果复制到其他区域。
- **优化成本。** 复制数据后，可以使用生命周期管理策略将数据转移到存档层，从而降低成本。

下图显示了对象复制如何将块 blob 从一个区域中的源存储帐户复制到两个不同区域中的目标帐户。

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="显示对象复制工作方式的图":::

若要了解如何配置对象复制，请参阅[配置对象复制](object-replication-configure.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>对象复制的先决条件

对象复制还需要启用以下 Azure 存储功能：

- [更改源](storage-blob-change-feed.md)：必须在源帐户上启用。 若要了解如何启用更改源，请参阅[启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)。
- [Blob 版本控制](versioning-overview.md)：必须同时在源帐户和目标帐户上启用。 若要了解如何启用版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。

启用更改源和 blob 版本控制可能会产生额外的成本。 有关更多详细信息，请参阅 [Azure 存储定价页](https://azure.microsoft.com/pricing/details/storage/)。

只有常规用途 v2 存储帐户支持对象复制。 源帐户和目标帐户都必须是常规用途 v2。 对象复制仅支持块 blob；不支持追加 blob 和页 blob。

## <a name="how-object-replication-works"></a>对象复制的工作原理

对象复制根据你配置的规则异步复制容器中的块 blob。 blob 的内容、与 blob 关联的任何版本以及 blob 的元数据和属性都从源容器复制到目标容器。

> [!IMPORTANT]
> 因为块 blob 数据是以异步方式复制的，所以源帐户和目标帐户不会立即同步。目前没有关于将数据复制到目标帐户所需时间的 SLA。 你可以在源 blob 上检查复制状态，确定复制是否已完成。 有关详细信息，请参阅[查看 blob 的复制状态](object-replication-configure.md#check-the-replication-status-of-a-blob)。

### <a name="blob-versioning"></a>Blob 版本控制

对象复制要求同时在源帐户和目标帐户上启用 blob 版本控制。 当源帐户中复制的 blob 被修改时，源帐户中将创建一个新的 blob 版本来反映修改前 blob 以前的状态。 源帐户中（或基 blob）的当前版本反映了最新的更新。 更新的当前版本和新的早期版本都将复制到目标帐户。 要详细了解写入操作如何影响 blob 版本，请参阅[对写入操作进行版本控制](versioning-overview.md#versioning-on-write-operations)。

删除源帐户中的 blob 时，将在先前版本中捕获 blob 的当前版本，然后将其删除。 即使删除了当前版本，该 blob 的所有早期版本仍然存在。 此状态将被复制到目标帐户。 要详细了解删除操作如何影响 blob 版本，请参阅[对删除操作进行版本控制](versioning-overview.md#versioning-on-delete-operations)。

### <a name="snapshots"></a>快照

对象复制不支持 blob 快照。 源帐户中 blob 上的任何快照都不会复制到目标帐户。

### <a name="blob-tiering"></a>Blob 分层

如果源帐户和目标帐户位于热层或冷层，则支持对象复制。 源帐户和目标帐户可能位于不同的层级。 但如果源帐户或目标帐户中的 blob 已移至存档层，则对象复制将失败。 有关 blob 层的详细信息，请参阅 [Azure Blob 存储的访问层 - 热、冷和存档](storage-blob-storage-tiers.md)。

### <a name="immutable-blobs"></a>不可变 blob

Azure Blob 存储的不可变性策略包括基于时间的保留策略和法定保留。 当不可变性策略对目标帐户生效时，对象复制可能会受到影响。 有关不可变性策略的详细信息，请参阅[使用不可变存储来存储业务关键型 blob 数据](immutable-storage-overview.md)。

如果容器级不可变性策略对目标帐户中的容器有效，并且源容器中的对象被更新或删除，则对源容器执行的操作可能会成功，但对目标容器执行该操作将会失败。 若要详细了解范围设为容器的不可变性策略禁止哪些操作，请参阅[使用容器级范围的方案](immutable-storage-overview.md#scenarios-with-container-level-scope)。

如果版本级不可变性策略对目标帐户中的 blob 版本有效，并且源容器中的 blob 版本被删除或更新，则对源对象执行的操作可能会成功，但对目标对象执行该操作将会失败。 若要详细了解范围设为容器的不可变性策略禁止哪些操作，请参阅[使用版本级范围的方案](immutable-storage-overview.md#scenarios-with-version-level-scope)。

## <a name="object-replication-policies-and-rules"></a>对象复制策略和规则

配置对象复制时，需要创建复制策略，以指定源存储帐户和目标帐户。 复制策略包括一个或多个规则，用于指定源容器和目标容器，并指明复制源容器中的哪些块 blob。

配置对象复制以后，Azure 存储会定期检查源帐户的更改源，并将所有写入或删除操作以异步方式复制到目标帐户。 复制延迟取决于要复制的块 blob 的大小。

### <a name="replication-policies"></a>复制策略

配置对象复制时，需要通过 Azure 存储资源提供程序针对源帐户和目标帐户创建复制策略。 复制策略通过策略 ID 进行标识。 源帐户和目标帐户的策略必须具有相同的策略 ID，才能进行复制。

一个源帐户最多可以复制到两个目标帐户，每个目标帐户使用一个策略。 类似地，一个帐户可以作为不超过两个复制策略的目标帐户。

源帐户和目标帐户可以位于相同区域，也可以位于不同区域。 它们还可以位于不同的订阅以及不同的 Azure Active Directory (Azure AD) 租户中。 只能为每个源帐户/目标帐户对创建一个复制策略。

### <a name="replication-rules"></a>复制规则

复制规则指定 Azure 存储如何将 blob 从源容器复制到目标容器。 可以为每个复制策略最多指定 10 个复制规则。 每个复制规则定义一个源和目标容器，每个源和目标容器只能在一个规则中使用，这意味着最多可以有 10 个源容器和 10 个目标容器参与一个复制策略。

创建复制规则时，默认情况下仅复制随后添加到源容器的新块 blob。 你可以指定复制新的和现有的块 blob，也可以定义自定义复制范围，以复制从指定的时间开始创建的块 blob。

也可以将一个或多个筛选器指定为复制规则的一部分，以按前缀筛选块 blob。 指定前缀时，只会将源容器中与该前缀匹配的 blob 复制到目标容器。

源和目标容器必须都存在，然后才能在规则中指定它们。 创建复制策略后，不允许将操作写入目标容器。 任何尝试写入目标容器的操作都会失败，错误代码为 409（冲突）。 若要写入为其配置了复制规则的目标容器，则必须删除为该容器配置的规则，或者删除复制策略。 当复制策略处于活动状态时，允许对目标容器执行读取和删除操作。

你可以针对目标容器中的 blob 调用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)操作，将其移动到存档层。 有关存档层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="replication-status"></a>复制状态

你可以在源帐户中查看 blob 的复制状态。 有关详细信息，请参阅[查看 blob 的复制状态](object-replication-configure.md#check-the-replication-status-of-a-blob)。

如果源帐户中 blob 的复制状态指示失败，则请调查以下可能的原因：

- 请确保已在目标帐户上配置对象复制策略。
- 请验证目标容器是否仍然存在。
- 如果在写入操作中已使用客户提供的密钥对源 blob 进行了加密，则对象复制将失败。 有关客户提供的密钥的详细信息，请参阅[在对 Blob 存储的请求中提供加密密钥](encryption-customer-provided-keys.md)。

## <a name="billing"></a>计费

对于源帐户和目标帐户的读写事务，对象复制会产生额外的成本，在将数据从源帐户复制到目标帐户时，会产生出站费用，处理更改源时，还会产生读取费用。

## <a name="next-steps"></a>后续步骤

- [配置对象复制](object-replication-configure.md)
- [Azure Blob 存储中的更改源支持](storage-blob-change-feed.md)
- [启用和管理 blob 版本控制](versioning-enable.md)
