---
title: Azure 托管磁盘的冗余选项
description: 了解 Azure 托管磁盘的区域冗余存储和本地冗余存储。
author: roygara
ms.author: rogarana
ms.date: 07/12/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 1875f43203735707a1bf49ac4e2d008abf898828
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664461"
---
# <a name="redundancy-options-for-managed-disks"></a>托管磁盘的冗余选项

Azure 托管磁盘提供两个存储冗余选项：本地冗余存储和预览版的区域冗余存储 (ZRS)。 ZRS 相比本地冗余存储 (LRS) 可为托管磁盘提供更高的可用性。 但 LRS 磁盘的写入延迟短于 ZRS 磁盘，因为 LRS 磁盘将数据同步写入单个数据中心内的三个副本中。

## <a name="locally-redundant-storage-for-managed-disks"></a>托管磁盘的本地冗余存储

本地冗余存储 (LRS) 在所选区域中的单个数据中心内复制数据三次。 LRS 可以保护数据，使其不受服务器机架和驱动器故障影响。 要保护 LRS 磁盘免受区域故障（如自然灾害或其他问题）的影响，请采取以下步骤：

- 使用可将数据同步写入两个区域的应用程序，并在发生灾难时自动故障转移到另一个区域。
    - 例如 SQL Server AlwaysOn。
- 使用 ZRS 快照频繁备份 LRS 磁盘。
- 通过 [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md) 为 LRS 磁盘启用跨区域灾难恢复。 但是，跨区域灾难恢复并不提供零恢复点目标 (RPO)。

如果工作流不支持跨区域的应用程序级同步写入，或者应用程序必须满足零 RPO，则 ZRS 磁盘将是理想之选。

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>托管磁盘的区域冗余存储（预览版）

区域冗余存储 (ZRS) 在所选区域中的三个 Azure 可用性区域同步复制 Azure 托管磁盘。 每个可用性区域都是一个独立的物理位置，具有独立的电源、冷却系统和网络。 

ZRS 磁盘（预览版）可让你从可用性区域故障中恢复。 如果区域出现故障，可将 ZRS 磁盘附加到另一个区域的虚拟机 (VM)。 还可以在 VM 之间共享 ZRS 磁盘，以提高群集或分布式应用程序（如 SQL FCI、SAP ASCS/SCS 或 GFS2）的可用性。 共享的 ZRS 磁盘可以附加到不同区域中的主 VM 和辅助 VM，以充分利用 ZRS 和[可用性区域](../availability-zones/az-overview.md)。 如果主区域发生故障，就可使用 [SCSI 永久预留](disks-shared-enable.md#supported-scsi-pr-commands)功能快速故障转移到辅助 VM。

### <a name="billing-implications"></a>计费影响

有关详细信息，请参阅 [Azure 定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="comparison-with-other-disk-types"></a>与其他磁盘类型的比较

除了写入延迟更长之外，使用 ZRS 的磁盘与使用 LRS 的磁盘完全相同，它们具有相同的缩放目标。 [对磁盘进行基准测试](disks-benchmarks.md)以模拟应用程序的工作负载，并比较 LRS 和 ZRS 磁盘的延迟。 

### <a name="limitations"></a>限制

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

## <a name="next-steps"></a>后续步骤

- 要了解如何创建 ZRS 磁盘，请参阅[部署 ZRS 托管磁盘](disks-deploy-zrs.md)。
