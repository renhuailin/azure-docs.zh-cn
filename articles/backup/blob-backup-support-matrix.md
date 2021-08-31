---
title: Azure Blob 备份的支持矩阵
description: 汇总了备份 Azure Blob 时的支持设置和限制
ms.topic: conceptual
ms.date: 07/07/2021
ms.custom: references_regions
ms.openlocfilehash: ba2798ff464720379326ee56098f840a06e2c042
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722649"
---
# <a name="support-matrix-for-azure-blobs-backup"></a>Azure Blob 备份的支持矩阵

本文总结了区域可用性、支持的方案以及 blob 操作备份的限制。

## <a name="supported-regions"></a>支持的区域

Blob 的操作备份目前在以下区域提供：澳大利亚中部、澳大利亚中部 2、澳大利亚东部、澳大利亚东南部、巴西南部、巴西东南部、加拿大中部、加拿大东部、印度中部、美国中部、东亚、美国东部、美国东部 2、法国中部、德国北部、德国中西部、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、挪威东部、挪威西部、南非北部、美国中南部、东南亚、印度南部、瑞士北部、瑞士西部、阿拉伯联合酋长国中部、阿拉伯联合酋长国北部、英国南部、英国西部、美国中西部、西欧、印度西部、美国西部、美国西部 2。

## <a name="limitations"></a>限制

Blob 的操作备份使用 blob 时点恢复、blob 版本控制、blob 的软删除、blob 的更改源以及删除锁定来提供本地备份解决方案。 因此，适用于这些功能的限制也适用于操作备份。

**支持的方案：** 操作备份仅支持标准常规用途 v2 存储帐户中的块 blob。 因此 ADLS Gen2 帐户不受支持。 此外，存储帐户中的任何页 blob、追加 blob 和高级 blob 都不会还原，并且只会还原块 blob。

**其他限制：**

- 如果在保持期内删除了某个容器，则该容器将无法使用时点恢复操作进行还原。 如果尝试还原的 blob 范围包含已删除的容器中的 blob，则时点恢复操作将失败。 要详细了解如何防止删除容器，请参阅[容器软删除（预览版）](../storage/blobs/soft-delete-container-overview.md)。
- 如果在当前时间与还原点之间的时间段内，blob 已移动到热存储层和冷存储层之间，则会将 blob 还原到其以前的层。 不支持还原存档层中的块 blob。 例如，如果热存储层中的某一 blob 在两天前已移至存档存储层，而还原操作还原至三天前的某个时间点，则该 blob 不会被还原至热存储层。 要还原已存档的 blob，请先将其从存档存储层移开。 有关详细信息，请参阅[从存档层解冻 Blob 数据](../storage/blobs/storage-blob-rehydration.md)。
- 通过 [Put Block](/rest/api/storageservices/put-block) 或 [Put Block from URL](/rest/api/storageservices/put-block-from-url) 上传、但未通过 [Put Block List](/rest/api/storageservices/put-block-list) 提交的块不是 blob 的一部分，因此不会在还原操作过程中还原。
- 无法还原具有活动租约的 blob。 如果要还原的 blob 范围内包含具有活动租约的 blob，则还原操作将自动失败。 在开始还原操作之前中断任何活动租约。
- 在还原操作过程中，不会创建或删除快照。 只可将基本 blob 还原到其以前的状态。
- 如果正在还原的 blob 中存在[不可变 blob](../storage/blobs/immutable-storage-overview.md#about-immutable-storage-for-blobs)，则此类不可变 blob 将不会根据所选恢复点恢复到其状态。 但是，其他未启用不可变性的 Blob 将还原到所选的恢复点，这是预期行为。

## <a name="next-steps"></a>后续步骤

[Azure Blob 操作备份概述](blob-backup-overview.md)