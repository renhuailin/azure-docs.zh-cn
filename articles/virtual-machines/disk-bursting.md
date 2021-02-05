---
title: 托管磁盘突发
description: 了解 Azure 磁盘和 Azure 虚拟机的磁盘突发。
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576187"
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
