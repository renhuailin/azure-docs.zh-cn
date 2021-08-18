---
title: Azure 托管磁盘的性能层
description: 了解托管磁盘的性能层。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: c45bc6fa0df5883f060458c3ce92680752e5875a
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358519"
---
# <a name="performance-tiers-for-managed-disks"></a>托管磁盘的性能层

创建磁盘时，将以其性能层的形式设置 Azure 托管磁盘的性能。 性能层决定了托管磁盘的 IOPS 和吞吐量。 当你设置磁盘的预配大小时，系统会自动选择性能层。 可以在部署时或之后更改性能层，而无需更改磁盘大小。

通过更改性能层，你可以在不使用磁盘突发功能的情况下做好准备并满足更高需求。 与依赖于突发功能相比，更改性能层可能更经济高效，具体取决于所需额外性能的时长。 这非常适用于那些暂时需要持续较高性能级别的事件，例如假日购物、性能测试或运行训练环境。 为了应对这些事件，你可根据需要使用级别较高的性能层。 当你不再需要额外性能时，可返回到原始层。

## <a name="how-it-works"></a>工作原理

首次部署或预配磁盘时，该磁盘的基线性能层将根据预配的磁盘大小进行设置。 你可以使用高于原始基线的性能层来满足更高的需求。 当你不再需要该性能级别时，可返回到初始基线性能层。

你的帐单会随性能层的变化而变化。 例如，如果你预配了 P10 磁盘 (128 GiB)，基线性能层将设置为 P10（500 IOPS 和 100 MBps）。 将按 P10 费率计费。 可以升级该层，以匹配 P50（7500 IOPS 和 250 MBps）的性能，而不增加磁盘大小。 在升级期间，将按 P50 费率计费。 如果不再需要更高的性能，可返回到 P10 层。 磁盘将再次按 P10 费率计费。

| 磁盘大小 | 基线性能层 | 可升级到 |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2、P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 8 GiB | P2 | P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 16 GiB | P3 | P4、P6、P10、P15、P20、P30、P40、P50 | 
| 32 GiB | P4 | P6、P10、P15、P20、P30、P40、P50 |
| 64 GiB | P6 | P10、P15、P20、P30、P40、P50 |
| 128 GiB | P10 | P15、P20、P30、P40、P50 |
| 256 GiB | P15 | P20、P30、P40、P50 |
| 512 GiB | P20 | P30、P40、P50 |
| 1 TiB | P30 | P40、P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | 无 |
| 8 TiB | P60 |  P70、P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | 无 |

有关计费信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>后续步骤

若要了解如何更改性能层，请参阅[门户](disks-performance-tiers-portal.md)或 [PowerShell/CLI](disks-performance-tiers.md) 文章。

