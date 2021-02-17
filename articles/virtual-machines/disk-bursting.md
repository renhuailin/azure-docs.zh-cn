---
title: 托管磁盘突发
description: 了解 Azure 磁盘和 Azure 虚拟机的磁盘突发。
author: albecker1
ms.author: albecker
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1cedac5814d1c547a28e9b1c894f416af5a924b5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585095"
---
# <a name="managed-disk-bursting"></a>托管磁盘突发
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虚拟机级突发
在以下 VM 系列上，支持在其支持的所有区域中启用 VM 级突发：
- [Lsv2 系列](lsv2-series.md)
- [Dsv3 系列](dv3-dsv3-series.md)
- [Esv3 系列](ev3-esv3-series.md)

默认情况下会为支持突发的虚拟机启用突发。

## <a name="disk-level-bursting"></a>磁盘级别突发
在 Azure 公有云、政府云和中国云的所有区域中，还为磁盘大小为 P20 和更小的[高级 SSD](disks-types.md#premium-ssd) 提供了突发功能。 在支持磁盘突发的磁盘大小的所有新的和现有的部署上，默认已启用磁盘突发。 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>后续步骤

若要了解如何深入了解突发资源，请参阅 [磁盘突发指标](disks-metrics.md)。
