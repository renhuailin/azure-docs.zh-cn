---
title: blob 的软删除
titleSuffix: Azure Storage
description: Blob 的软删除可以保护数据，以便在应用程序或其他存储帐户用户错误地修改或删除数据后可以更轻松地恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 39dd221210b558a3b6ce59200aebaa4aa2278fb5
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688145"
---
# <a name="soft-delete-for-blobs"></a>blob 的软删除

Blob 软删除通过在系统中将已删除的数据保留指定的一段时间，在意外删除或覆盖单个 Blob、快照和版本时提供保护。 在保持期内，可以将软删除对象还原到它在删除时的状态。 在保持期到期后，对象将被永久删除。

> [!IMPORTANT]
> 在已启用分层命名空间功能的帐户中进行的软删除目前为预览版，并且在全球所有 Azure 区域内均可用。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
>
> 若要注册预览版，请参阅[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)。


## <a name="recommended-data-protection-configuration"></a>推荐的数据保护配置

Blob 软删除是针对 Blob 数据的综合性数据保护策略的一部分。 为实现对 Blob 数据的最佳保护，Microsoft 建议启用以下所有数据保护功能：

- 容器软删除，用于还原已删除的容器。 若要了解如何启用容器软删除，请参阅[启用和管理容器的软删除](soft-delete-container-enable.md)。
- Blob 版本控制，用于自动维护 blob 的先前版本。 启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原 blob 的先前版本以恢复数据。 若要了解如何启用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。
- Blob 软删除，用于还原已删除的 blob、快照或版本。 若要了解如何启用 blob 软删除，请参阅[启用和管理 blob 的软删除](soft-delete-blob-enable.md)。

若要详细了解 Microsoft 的数据保护建议，请参阅[数据保护概述](data-protection-overview.md)。

## <a name="how-blob-soft-delete-works"></a>Blob 软删除工作原理

为存储帐户启用 blob 软删除时，将为删除的对象指定一个保持期（1 至 365 天）。 保持期表示数据在被删除或覆盖后保持可用的时间。 保持期从对象被删除或覆盖的那一刻开始计算。

在保持期生效期间，可通过调用[撤销删除 Blob](/rest/api/storageservices/undelete-blob) 操作还原已删除的 blob 及其快照或还原已删除的版本。 下图显示了在启用 blob 软删除后，如何还原已删除的对象：

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="显示如何还原软删除 Blob 的示意图":::

可随时更改软删除保持期。 更新后的保持期仅适用于在保持期更改后删除的数据。 在保持期更改之前删除的任何数据均以数据被删除当下有效的保持期为准。

尝试删除某个软删除的对象不会影响其到期时间。

禁用 blob 软删除后，可以在软删除保持期结束之前继续访问和恢复存储帐户中的软删除对象。

Blob 版本控制可用于常规用途 v2、块 blob 和 Blob 存储帐户。 当前不支持使用分层命名空间的存储帐户。

Azure 存储 REST API 2017-07-29 版及更高版本支持 blob 软删除。

> [!IMPORTANT]
> 只能使用 blob 软删除还原单个 blob、快照、目录（位于分层命名空间）或版本。 若要还原容器及其内容，还必须为存储帐户启用容器软删除。 Microsoft 建议启用容器软删除和 blob 版本控制以及 blob 软删除，以确保对 blob 数据提供完整保护。 有关详细信息，请参阅[数据保护概述](data-protection-overview.md)。
>
> Blob 软删除并不能防止删除存储帐户。 若要防止存储帐户被删除，请在存储帐户资源上配置锁。 有关锁定存储帐户的详细信息，请参阅[将 Azure 资源管理器锁定应用于存储帐户](../common/lock-account-resource.md)。

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>启用软删除后，删除操作将作何处理

启用 blob 软删除后，删除 blob 会将该 blob 标记为软删除。 不会创建快照。 保持期到期后，软删除的 blob 将被永久删除。

如果 blob 包含快照，则除非连同快照一起删除，否则无法删除 blob。 删除 blob 及其快照后，blob 和快照都将标记为软删除。 不会创建新的快照。

可以在不删除基本 blob 的情况下删除一个或多个活动快照。 在这种情况下，快照被标记为软删除。

如果在启用了分层命名空间功能的帐户中删除目录，则系统会将目录及其所有内容标记为软删除。 

除非显式显示或列出，否则软删除对象不可见。 有关如何列出软删除对象的详细信息，请参阅[管理和还原软删除的 blob](soft-delete-blob-manage.md)。

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>启用软删除后，覆盖操作将作何处理

>[!IMPORTANT]
> 本部分不适用于具有分层命名空间的帐户。

调用[放置 Blob](/rest/api/storageservices/put-blob)、[放置块列表](/rest/api/storageservices/put-block-list)或[复制 Blob](/rest/api/storageservices/copy-blob) 等操作可覆盖 blob 中的数据。 启用 blob 软删除后，覆盖 blob 会自动创建写入操作前 blob 状态的软删除快照。 保持期到期后，软删除的快照将被永久删除。

除非显式显示或列出软删除对象，否则软删除快照不可见。 有关如何列出软删除对象的详细信息，请参阅[管理和还原软删除的 blob](soft-delete-blob-manage.md)。

若要保护复制操作，必须为目标存储帐户启用 blob 软删除。

Blob 软删除不能防止写入 blob 元数据或属性的操作。 更新 blob 的元数据或属性时，不会创建软删除快照。

Blob 软删除不会对存档层中的 blob 提供覆盖保护。 如果存档层中的 blob 被任何层中的新 blob 覆盖，则被覆盖的 blob 将被永久删除。

对于高级存储帐户，软删除快照不计入每个 blob 100 个快照的限制。

### <a name="restoring-soft-deleted-objects"></a>还原软删除的对象

可以通过在保持期内调用[“撤销删除 Blob”](/rest/api/storageservices/undelete-blob)操作还原软删除的 Blob 或目录（位于分层命名空间）。 “撤销删除 Blob”操作将还原 blob 以及任何与其关联的软删除快照。 将还原在保持期内删除的所有快照。

在具有分层命名空间的帐户中，“撤销删除 Blob”操作还可用于还原软删除的目录及其所有内容。 如果重命名包含软删除 Blob 的目录，则这些软删除的 Blob 会断开与目录的连接。 如果要还原这些 Blob，则必须将目录的名称还原为原始名称，或者创建一个使用原始目录名称的单独目录。 否则，在尝试还原这些软删除的 Blob 时，将收到错误。

对未被软删除的 blob 调用“撤销删除 Blob”将还原与该 blob 关联的任何软删除快照。 如果 blob 不包含快照且未被软删除，则调用“撤销删除 Blob”不起任何作用。

若要将软删除的快照提升到基本 blob，请先对基本 blob 调用“撤销删除 Blob”以还原 blob 及其快照。 接下来，将所需的快照复制到基本 blob 上。 也可将该快照复制到新的 blob 中。

在还原对象之前，无法读取软删除 blob 或快照中的数据。

有关如何还原软删除对象的详细信息，请参阅[管理和还原软删除的 blob](soft-delete-blob-manage.md)。

## <a name="blob-soft-delete-and-versioning"></a>Blob 软删除和版本控制

>[!IMPORTANT]
> 具有分层命名空间的帐户不支持版本控制。

如果在存储帐户上同时启用 blob 版本控制和 blob 软删除，则覆盖某个 blob 将自动创建一个新版本。 系统不会软删除新版本，并且不会在软删除保留期到期时删除该版本。 不会创建软删除的快照。 删除某个 blob 时，该 blob 的当前版本将成为先前版本，当前版本不复存在。 不会创建新版本，也不会创建软删除的快照。

同时启用软删除和版本控制可以防止 blob 版本被删除。 启用软删除后，删除某个版本会创建软删除版本。 在软删除保持期内，可使用“撤消删除 Blob”操作来还原软删除的版本。 “撤消删除 Blob”操作始终会还原 blob 的所有软删除版本。 无法只还原一个软删除版本。

在软删除保留期结束之后，将永久删除任何软删除的 blob 版本。

> [!NOTE]
> 启用版本控制后，对已删除的 blob 调用“撤消删除 Blob”操作会还原所有软删除版本或快照，但不会还原当前版本。 若要还原当前版本，请通过将先前版本复制到当前版本来提升它。

Microsoft 建议为你的存储帐户同时启用版本控制和 blob 软删除以实现最佳数据保护。 有关如何结合使用 Blob 版本控制和软删除的详细信息，请参阅 [Blob 版本控制和软删除](versioning-overview.md#blob-versioning-and-soft-delete)。

## <a name="blob-soft-delete-protection-by-operation"></a>Blob 软删除操作保护

下表描述了启用 blob 软删除（无论是否启用 blob 版本控制）后删除和写入操作的预期行为。 

### <a name="storage-account-no-hierarchical-namespace"></a>存储帐户（无分层命名空间）

| REST API 操作 | 已启用软删除 | 同时启用软删除和版本控制 |
|--|--|--|
| [删除存储帐户](/rest/api/storagerp/storageaccounts/delete) | 无更改。 已删除帐户中的容器和 blob 不可恢复。 | 无更改。 已删除帐户中的容器和 blob 不可恢复。 |
| [删除容器](/rest/api/storageservices/delete-container) | 无更改。 已删除容器中的 blob 不可恢复。 | 无更改。 已删除容器中的 blob 不可恢复。 |
| [删除 Blob](/rest/api/storageservices/delete-blob) | 如果用于删除 blob，该 blob 将标记为软删除。 <br /><br /> 如果用于删除 blob 快照，该快照将标记为软删除。 | 如果用于删除 blob，则当前版本将成为先前版本，并且会删除当前版本。 不会创建新版本，也不会创建软删除的快照。<br /><br /> 如果用于删除 blob 版本，该版本将标记为软删除。 |
| [取消删除 Blob](/rest/api/storageservices/undelete-blob) | 还原在保持期内删除的 blob 和所有快照。 | 还原在保持期内删除的 blob 和所有版本。 |
| [放置 Blob](/rest/api/storageservices/put-blob)<br />[放置块列表](/rest/api/storageservices/put-block-list)<br />[复制 Blob](/rest/api/storageservices/copy-blob)<br />[从 URL 复制 Blob](/rest/api/storageservices/copy-blob) | 如果对活动 blob 调用该操作，将自动生成操作之前的 blob 状态的快照。 <br /><br /> 如果对软删除的 blob 调用该操作，则仅当该 blob 被同一类型的 blob 所替换时，才会生成该 blob 先前状态的快照。 如果 blob 属于不同类型，则所有现有软删除数据将被永久删除。 | 将自动生成捕获该操作前 blob 的状态的新版本。 |
| [放置块](/rest/api/storageservices/put-block) | 如果用于将块提交到活动 blob 中，则不发生任何更改。<br /><br />如果用于将块提交到软删除的 blob 中，将创建新的 blob 并自动生成快照，以捕获软删除 blob 的状态。 | 无更改。 |
| [放置页](/rest/api/storageservices/put-page)<br />[从 URL 放置页](/rest/api/storageservices/put-page-from-url) | 无更改。 通过该操作覆盖或清除的页 blob 数据不会保存，且不可恢复。 | 无更改。 通过该操作覆盖或清除的页 blob 数据不会保存，且不可恢复。 |
| [追加块](/rest/api/storageservices/append-block)<br />[通过 URL 追加块](/rest/api/storageservices/append-block-from-url) | 无更改。 | 无更改。 |
| [设置 Blob 属性](/rest/api/storageservices/set-blob-properties) | 无更改。 被覆盖的 blob 属性不可恢复。 | 无更改。 被覆盖的 blob 属性不可恢复。 |
| [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata) | 无更改。 被覆盖的 blob 元数据不可恢复。 | 将自动生成捕获该操作前 blob 的状态的新版本。 |
| [设置 Blob 层](/rest/api/storageservices/set-blob-tier) | 基本 blob 被移至新层。 任何活动或软删除的快照将保留在原始层中。 不会创建软删除的快照。 | 基本 blob 被移至新层。 任何活动或软删除的版本将保留在原始层中。 不会创建新的版本。 |

### <a name="storage-account-hierarchical-namespace"></a>存储帐户（分层命名空间）

|**REST API 操作**|**已启用软删除**|
|---|---|
|[路径 - 删除](/rest/api/storageservices/datalakestoragegen2/path/delete) |将创建软删除的 Blob 或目录。 软删除的对象将在保持期后删除。|
|[删除 Blob](/rest/api/storageservices/delete-blob)|将创建软删除对象。 软删除的对象将在保持期后删除。 包含快照的 Blob 和快照不支持软删除。|
|[路径 - 创建](/rest/api/storageservices/datalakestoragegen2/path/create)，用于重命名 Blob 或目录 | 现有目标 Blob 或空目录将被软删除，系统会使用数据源来进行替换。 软删除的对象将在保持期后删除。|

## <a name="pricing-and-billing"></a>定价和计费

所有软删除数据按与活动数据相同的费率计费。 不会对在保持期到期后永久删除的数据计费。

启用软删除时，Microsoft 建议使用较短的保持期，以便更好地了解因该功能产生的费用变动。 建议的最短保持期为七天。

为频繁覆盖的数据启用软删除可能会导致在列出 Blob 时存储容量费用增加且延迟增加。 可以通过将频繁覆盖的数据存储在禁用了软删除的单独存储帐户中来缓解这种额外的成本和延迟问题。

覆盖或删除 blob 后，不会对快照或版本自动生成相关事务进行计费。 将按写入操作费率对“撤销删除 Blob”操作进行计费。

有关 Blob 存储定价的详细信息，请参阅 [Blob 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)页。

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Blob 软删除和虚拟机磁盘  

Blob 软删除适用于高级和标准非托管磁盘（实际上是页 blob）。 软删除可帮助恢复由“删除 Blob”、“放置 Blob”、“放置块列表”和“复制 Blob”操作删除或覆盖的数据   。

通过调用“放置页”覆盖的数据不可恢复。 Azure 虚拟机通过调用“放置页”来写入非托管磁盘，因此不支持使用软删除来撤消从 Azure VM 写入到非托管磁盘的操作。

## <a name="next-steps"></a>后续步骤

- [为 blob 启用软删除](./soft-delete-blob-enable.md)
- [管理和还原软删除的 Blob](soft-delete-blob-manage.md)
- [Blob 版本控制](versioning-overview.md)
