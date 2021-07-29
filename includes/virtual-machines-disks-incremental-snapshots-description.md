---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/21/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b9b09012237d8f519322c927f8f2bbca1d3edef2
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107925299"
---
增量快照是托管磁盘的时间点备份；拍摄快照后，这些备份仅包含自上次快照以来的更改。 从增量快照还原磁盘时，系统将重新构造完整的磁盘，这是指在拍摄增量快照时磁盘的时间点备份。 托管磁盘快照的这种功能可能使它们更具成本效益，因为你不必在拍摄单个快照时存储整个磁盘，除非你选择这么做。 与完整快照一样，增量快照可用于创建完整的托管磁盘，也可用于制作完整快照。 完整快照和增量快照都可以在拍摄后立即使用。 换句话说，一旦拍摄了快照，你就可以立即读取基础 VHD 并使用它来还原磁盘。

增量快照和完整快照之间存在一些差异。 增量快照始终使用标准 HDD 存储，而不管磁盘的存储类型如何，而完整快照可使用高级 SSD。 若要在高级存储上使用完整快照来纵向扩展 VM 部署，建议在[共享映像库](../articles/virtual-machines/shared-image-galleries.md)中的标准存储上使用自定义映像。 它将帮助你以更低的成本实现更大的规模。 此外，增量快照可能会凭借[区域冗余存储](../articles/storage/common/storage-redundancy.md) (ZRS) 提供更高的可靠性。 如果 ZRS 在所选区域中可用，则增量快照将自动使用 ZRS。 如果 ZRS 在该区域不可用，则快照将默认为[本地冗余存储](../articles/storage/common/storage-redundancy.md) (LRS)。 此行为可以重写，并手动选择一项，但我们不建议这样做。

增量快照还提供差异功能，但仅适用于托管磁盘。 它们用于获取同一托管磁盘的两个增量快照之间的差异，可适用至块级别。 在跨区域复制快照时，可以使用此功能来减少数据占用空间。  例如，可以将第一个增量快照下载为另一个区域中的基本 Blob。 对于后续增量快照，只需要将自上次快照以来的更改复制到基本 Blob 中。 复制更改后，可以在基本 Blob 上拍摄表示另一个区域中磁盘的时间点备份的快照。 可以从基本 Blob 或从其他区域中的基本 Blob 上的快照还原磁盘。

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="描述跨区域复制的增量快照的图表。快照会执行各种 API 调用，直到最终形成每个快照的页 blob。":::

增量快照仅按已用大小计费。 可以通过查看 [Azure 使用情况报告](../articles/cost-management-billing/understand/review-individual-bill.md)，了解快照的已使用大小。 例如，如果快照的已用数据大小为 10 GiB，则每日使用情况报告将显示 10 GiB/(31 天) = 0.3226 作为已使用数量。