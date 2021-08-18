---
title: 虚拟机和磁盘性能
description: 详细了解如何组合使用虚拟机及其附加的磁盘以提高性能。
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 64b535c717d446957e06464a7b3393565a4e250e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113362298"
---
# <a name="virtual-machine-and-disk-performance"></a>虚拟机和磁盘性能
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虚拟机非缓存限制与缓存限制
同时启用了高级存储和高级存储缓存的虚拟机有两种不同的存储带宽限制。 让我们以 Standard_D8s_v3 虚拟机为例。 下面是有关 [Dsv3 系列](dv3-dsv3-series.md)和 Standard_D8s_v3 的文档：

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

