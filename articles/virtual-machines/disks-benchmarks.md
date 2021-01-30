---
title: 在 Azure 磁盘存储上对应用程序进行基准测试
description: 了解在 Azure 上对应用程序进行基准测试的过程。
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094573"
---
# <a name="benchmark-a-disk"></a>对磁盘进行基准检验

基准测试是指模拟应用程序的不同工作负荷，针对每个工作负荷来测量应用程序性能这样一个过程。 通过[为实现高性能而设计](premium-storage-performance.md)一文中描述的步骤，你已经收集了应用程序性能要求。 通过在托管应用程序的 Vm 上运行基准测试工具，可以确定应用程序可使用高级 Ssd 实现的性能级别。 本文提供了有关使用 Azure premium Ssd 预配的 Standard_D8ds_v4 VM 的基准测试示例。

我们使用了常见的基准测试工具 DiskSpd 和 FIO，分别适用于 Windows 和 Linux。 这些工具会生成多个线程，这些线程模拟类似生产的工作负荷，并测量系统性能。 使用这些工具还可以配置各种参数（例如块大小和队列深度），应用程序的这些参数通常无法更改。 这使你能够更灵活地在使用高级 Ssd 为不同类型的应用程序工作负荷预配的大规模 VM 上实现最大性能。 若要详细了解每个基准测试工具，请访问 [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) 和 [FIO](http://freecode.com/projects/fio)。

若要按以下示例进行操作，请创建 Standard_D8ds_v4，并将四个高级 Ssd 附加到 VM。 对于这四个磁盘，请将三个主机缓存配置为 "无"，并将其带到名为为 nocachewrites 的卷。 将剩余磁盘上的主机缓存配置为“ReadOnly”，在该磁盘上创建名为 CacheReads 的卷。 使用此设置，可以查看 Standard_D8ds_v4 VM 的最大读取和写入性能。 有关使用高级 Ssd 创建 Standard_D8ds_v4 的详细步骤，请参阅 [设计高性能](premium-storage-performance.md)。

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>后续步骤

继续阅读有关[针对高性能进行设计](premium-storage-performance.md)的文章。

在该文中，你将为原型创建一个类似于现有应用程序的清单。 使用各种能够用来模拟工作负荷并衡量原型应用程序性能的基准测试工具。 这样做可以确定哪些磁盘产品可以满足或超过你的应用程序性能要求。 然后，就可以将相同的准则实施到生产型应用程序中。
