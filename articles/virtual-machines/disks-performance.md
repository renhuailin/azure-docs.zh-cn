---
title: 虚拟机和磁盘性能
description: 详细了解虚拟机及其附加的磁盘如何结合使用以提高性能。
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5e9f6ecc733eccf317e3013752ee2f5b0586ea78
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540519"
---
# <a name="virtual-machine-and-disk-performance"></a>虚拟机和磁盘性能
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虚拟机非缓存限制与缓存限制
同时启用了高级存储和高级存储缓存的虚拟机有两种不同的存储带宽限制。 让我们以 Standard_D8s_v3 虚拟机为例。 下面是有关 [Dsv3 系列](dv3-dsv3-series.md)和 Standard_D8s_v3 的文档：

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

让我们对可创建 IO 活动的此虚拟机和磁盘组合运行基准测试。 若要了解如何在 Azure 上对存储 IO 进行基准测试，请参阅[在 Azure 磁盘存储上对应用程序进行基准测试](disks-benchmarks.md)。 通过基准测试工具，可以看到 VM 和磁盘组合可以实现 22,800 个 IOPS：

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-3.md)]
