---
title: 在 Azure 磁盘存储上对应用程序进行基准测试
description: 了解在 Azure 上对应用程序进行基准测试的过程。
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: a6e156350be0de13b6c6bfea3f8d220830c3480e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360768"
---
# <a name="benchmark-a-disk"></a>对磁盘进行基准检验

基准测试是指模拟应用程序的不同工作负荷，针对每个工作负荷来测量应用程序性能这样一个过程。 通过[为实现高性能而设计](premium-storage-performance.md)一文中描述的步骤，你已经收集了应用程序性能要求。 通过在托管应用程序的 VM 上运行基准测试工具，可以确定应用程序在高级 SSD 中能够达到的性能级别。 在本文中，我们提供了如何对预配了 Azure 高级 SSD 的 Standard_D8ds_v4 VM 进行基准测试的示例。

我们使用了常见的基准测试工具 DiskSpd 和 FIO（分别适用于 Windows 和 Linux）。 这些工具会生成多个线程，这些线程模拟类似生产的工作负荷，并测量系统性能。 使用这些工具还可以配置各种参数（例如块大小和队列深度），应用程序的这些参数通常无法更改。 这样，就可以在预配了高级 SSD 的大规模 VM 上，为不同类型的应用程序工作负荷灵活实现最高性能。 若要详细了解每种基准测试工具，请参阅 [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) 和 [FIO](http://freecode.com/projects/fio)。

若要按以下示例进行操作，请创建一个 Standard_D8ds_v4 VM，并将四个高级 SSD 附加到该 VM。 在这四个磁盘中，将三个磁盘的主机缓存配置为“无”，并将其条带化到名为 NoCacheWrites 的卷中。 将剩余磁盘上的主机缓存配置为“ReadOnly”，在该磁盘上创建名为 CacheReads 的卷。 使用这种设置，即可发现 Standard_D8ds_v4 VM 展现出最大的读写性能。 有关创建具有高级 SSD 的 Standard_D8ds_v4 VM 的详细步骤，请参阅[高性能设计](premium-storage-performance.md)。

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>后续步骤

继续阅读有关[针对高性能进行设计](premium-storage-performance.md)的文章。

在该文中，你将为原型创建一个类似于现有应用程序的清单。 使用各种能够用来模拟工作负荷并衡量原型应用程序性能的基准测试工具。 这样做可以确定哪些磁盘产品可以满足或超过你的应用程序性能要求。 然后，就可以将相同的准则实施到生产型应用程序中。
