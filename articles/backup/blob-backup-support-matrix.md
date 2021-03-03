---
title: Azure Blob 备份的支持矩阵
description: '汇总了在预览版 (中备份 Azure Blob 时的支持设置和限制) '
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: ade43350bbe3fa1bcf58f47e93b948db3a5b21bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744430"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>预览版中的 Azure Blob 备份 (支持矩阵) 

本文总结了区域可用性、支持的方案以及 blob 操作备份的限制。

## <a name="supported-regions"></a>支持的区域

适用于 blob 的操作备份目前在以下区域中提供：澳大利亚中部、澳大利亚东部、巴西南部、加拿大中部、印度中部、美国中部、东亚、美国东部、美国东部2、德国中西部、日本东部、日本西部、韩国中部、韩国南部、北欧、美国中南部、南东亚、瑞士北部、阿拉伯联合酋长国北部、英国南部、英国西部、美国中北部、西欧、美国西部、美国西部2

## <a name="limitations"></a>限制

Blob 的操作备份使用 blob 时点还原、blob 版本控制、blob 的软删除、更改 blob 的源以及删除锁定以提供本地备份解决方案。 因此，适用于这些功能的限制也适用于操作备份。

**支持的方案：** 操作备份仅支持标准常规用途 v2 存储帐户中的块 blob。 因此 ADLS Gen2 帐户不受支持。 此外，存储帐户中的任何页 blob、追加 blob 和高级 blob 都不会还原，并且仅会还原块 blob。

**其他限制：**

- 如果在保留期内删除了某个容器，则该容器不会随时间点还原操作一起还原。 如果尝试还原的 blob 范围包含已删除容器中的 blob，则时间点还原操作将失败。 有关保护删除容器的详细信息，请参阅 [ (预览版) 的容器软删除 ](https://docs.microsoft.com/azure/storage/blobs/soft-delete-container-overview)。
- 如果在当前时间与还原点之间的时间段内，blob 已移动到热层和冷层之间，则会将 blob 还原到其以前的层。 不支持在存档层中还原块 blob。 例如，如果热层中的 blob 在两天前移到了存档层，并且还原操作在三天前还原，则 blob 不会还原到热层。 若要还原已存档的 blob，请先将其从存档层移开。 有关详细信息，请参阅[从存档层解冻 Blob 数据](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration)。
- 通过 [put](https://docs.microsoft.com/rest/api/storageservices/put-block) 块或 [PUT 块从 URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)上传的块，但不是通过 [put 块列表](https://docs.microsoft.com/rest/api/storageservices/put-block-list)提交的块，不是 blob 的一部分，因此不会在还原操作过程中还原。
- 无法还原具有活动租约的 blob。 如果要还原的 blob 范围内包含具有活动租约的 blob，则还原操作将自动失败。 在开始还原操作之前中断任何活动租约。
- 在还原操作过程中，不会创建或删除快照。 只有基本 blob 还原到其以前的状态。

## <a name="next-steps"></a>后续步骤

- [预览版中的 Azure Blob (操作备份概述) ](blob-backup-overview.md)
