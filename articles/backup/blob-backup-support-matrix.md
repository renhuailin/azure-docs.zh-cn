---
title: Azure Blob 备份的支持矩阵
description: 汇总了备份 Azure Blob 时的支持设置和限制
ms.topic: conceptual
ms.date: 10/01/2021
ms.custom: references_regions
ms.openlocfilehash: 921f08a80511766f55e179fccde38a061800061e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533184"
---
# <a name="support-matrix-for-azure-blobs-backup"></a>Azure Blob 备份的支持矩阵

本文总结了区域可用性、支持的方案以及 blob 操作备份的限制。

## <a name="supported-regions"></a>支持的区域

Blob 操作备份适用于除法国南部和南非西部以外的所有公有云区域。 它还在主权云区域中可用 - 所有 Azure 政府区域和中国区域（中国东部除外）。

## <a name="limitations"></a>限制

Blob 的操作备份使用 blob 时点恢复、blob 版本控制、blob 的软删除、blob 的更改源以及删除锁定来提供本地备份解决方案。 因此，适用于这些功能的限制也适用于操作备份。

**支持的方案：** 操作备份仅支持标准常规用途 v2 存储帐户中的块 blob。 因此 ADLS Gen2 帐户不受支持。 此外，存储帐户中的任何页 blob、追加 blob 和高级 blob 都不会还原，并且只会还原块 blob。

**其他限制：**

- 如果在保持期内删除了某个容器，则该容器将无法使用时点恢复操作进行还原。 如果尝试还原的 blob 范围包含已删除的容器中的 blob，则时点恢复操作将失败。 要详细了解如何防止删除容器，请参阅[容器软删除（预览版）](../storage/blobs/soft-delete-container-overview.md)。
- 如果在当前时间与还原点之间的时间段内，blob 已移动到热存储层和冷存储层之间，则会将 blob 还原到其以前的层。 不支持还原存档层中的块 blob。 例如，如果热存储层中的某一 blob 在两天前已移至存档存储层，而还原操作还原至三天前的某个时间点，则该 blob 不会被还原至热存储层。 要还原已存档的 blob，请先将其从存档存储层移开。 有关详细信息，请参阅[从存档层解冻 Blob 数据](../storage/blobs/archive-rehydrate-overview.md)。
- 通过 [Put Block](/rest/api/storageservices/put-block) 或 [Put Block from URL](/rest/api/storageservices/put-block-from-url) 上传、但未通过 [Put Block List](/rest/api/storageservices/put-block-list) 提交的块不是 blob 的一部分，因此不会在还原操作过程中还原。
- 无法还原具有活动租约的 blob。 如果要还原的 blob 范围内包含具有活动租约的 blob，则还原操作将自动失败。 在开始还原操作之前中断任何活动租约。
- 在还原操作过程中，不会创建或删除快照。 只可将基本 blob 还原到其以前的状态。
- 如果正在还原的 blob 中存在[不可变 blob](../storage/blobs/immutable-storage-overview.md#about-immutable-storage-for-blobs)，则此类不可变 blob 将不会根据所选恢复点恢复到其状态。 但是，其他未启用不可变性的 Blob 将还原到所选的恢复点，这是预期行为。

## <a name="next-steps"></a>后续步骤

[Azure Blob 操作备份概述](blob-backup-overview.md)