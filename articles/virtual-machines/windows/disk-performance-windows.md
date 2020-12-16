---
title: 虚拟机和磁盘性能-Windows
description: 详细了解如何将虚拟机及其附加的磁盘组合在一起以提高 Windows 性能。
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584113"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Windows)  (虚拟机和磁盘性能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虚拟机非缓存限制与缓存限制
 同时启用了高级存储和高级存储缓存的虚拟机有两种不同的存储带宽限制。 接下来，让我们继续查看 Standard_D8s_v3 的虚拟机。 下面是有关 [Dsv3 系列](../dv3-dsv3-series.md)及其中的 Standard_D8s_v3 的文档：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

我们来对此 VM 和磁盘组合运行基准测试，这将创建 IO 活动，你可以在[此处](disks-benchmarks.md)了解如何在 Azure 上对存储 IO 进行基准测试。 通过基准测试工具，可以看到 VM 和磁盘组合能够实现 22,800 IOPS：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]