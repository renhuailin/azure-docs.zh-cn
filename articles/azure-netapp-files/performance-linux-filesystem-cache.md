---
title: 适用于 Azure NetApp 文件的 Linux 文件系统缓存最佳做法 | Microsoft Docs
description: 介绍适用于 Azure NetApp 文件的 Linux 文件系统缓存最佳做法。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: d0d097a13fa24fb62f70648a34edd27e3b5a0636
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203852"
---
# <a name="linux-filesystem-cache-best-practices-for-azure-netapp-files"></a>适用于 Azure NetApp 文件的 Linux 文件系统缓存最佳做法

本文将帮助你了解适用于 Azure NetApp 文件的文件系统缓存最佳做法。  

## <a name="filesystem-cache-tunables"></a>文件系统缓存可调参数

需要了解有关文件系统缓存可调参数的以下因素：  

* 刷新脏缓冲区会使数据处于干净状态，可供将来读取，直到内存压力导致逐出。  
* 异步刷新操作有三个触发器：
    * 基于时间：当缓冲区达到此可调参数定义的期限时，必须将其标记为清理（即刷新或写入存储）。
    * 内存压力：有关详细信息，请参阅 [`vm.dirty_ratio | vm.dirty_bytes`](#vmdirty_ratio--vmdirty_bytes)。
    * 关闭：关闭文件句柄时，所有脏缓冲区都将异步刷新到存储。

这些因素由四个可调参数控制。 每个可调参数都可使用 `tuned` 或 `sysctl` 在 `/etc/sysctl.conf` 文件中进行动态和持久的优化。 优化这些参数可以改进应用程序的性能。  

> [!NOTE]
> 本文中讨论的信息在 SAS GRID 和 SAS Viya 验证练习中有介绍。 因此，这些可调参数基于通过验证练习学习的课程。 调整这些参数也能为许多应用程序带来好处。

### `vm.dirty_ratio | vm.dirty_bytes` 

这两个可调参数定义可用于已修改但尚未写入稳定存储的数据的 RAM 量。  无论设置了哪个可调参数，都会自动将另一个可调参数设置为零；RedHat 建议不要将两个可调参数中的任何一个手动设置为零。  Redhat 将选项 `vm.dirty_ratio`（两者的默认值）设置为物理内存的 20% 或 30%，具体取决于操作系统，而考虑到新式系统的内存占用情况，这可能是一个很大的数字。 无论内存大小如何，都应考虑设置 `vm.dirty_bytes` 而不是 `vm.dirty_ratio` 以获得更一致的体验。  例如，正在进行的 SAS GRID 工作确定了 30 MiB 是实现最佳整体混合工作负载性能的适当设置。 

### `vm.dirty_background_ratio | vm.dirty_background_bytes` 

这些可调参数定义了 Linux 写回机制开始将脏块刷新到稳定存储的起点。 Redhat 默认为 10% 的物理内存，在大内存系统中，对于要开始刷新的数据，这是一个很大的数字。 以 SAS GRID 为例，过去的建议是将 `vm.dirty_background` 设置为 `vm.dirty_ratio` 或 `vm.dirty_bytes` 的 1/5 大小。 考虑到为 SAS GRID 设置 `vm.dirty_bytes` 设置的积极程度，此处未设置任何特定值。  

### `vm.dirty_expire_centisecs` 

此可调参数定义了脏缓冲区在必须被标记为异步写出之前可以使用多长时间。以 SAS Viya 的 CAS 工作负载为例。 通过一个以写入为主的临时工作负载发现，将此值设置为 300 厘秒（3 秒）为最佳，而 3000 厘秒（30 秒）是默认值。  

SAS Viya 将 CAS 数据分成多个小区块，每个小区块为几兆字节。  不是在将数据写入每个分片后关闭这些文件句柄，而是将句柄保持为打开状态，并且应用程序对其中的缓冲区进行内存映射。  如果没有关闭，则在内存压力消失或 30 秒过去之前，将不会进行刷新。 事实证明，等待内存压力和等待长时间的定时器到期一样，都不是最理想的。 与寻求最佳整体吞吐量的 SAS GRID 不同，SAS Viya 寻求优化写入带宽。  

### `vm.dirty_writeback_centisecs` 

内核刷新器线程负责在每个刷新线程休眠之间异步刷新脏缓冲区。  此可调参数定义在两次刷新之间休眠所用时间。  考虑到 SAS Viya 使用的 3 秒 `vm.dirty_expire_centisecs` 值，SAS 将此可调参数设置为 100 厘秒（1 秒）而不是默认值 500 厘秒（5 秒）以寻求最佳整体性能。

## <a name="impact-of-an-untuned-filesystem-cache"></a>未经优化文件系统缓存的影响

考虑到新式系统中的默认虚拟内存可调参数和 RAM 量，从驱动这种混合工作负载的特定客户端的角度来看，写回可能会减慢其他存储绑定操作的速度。  未经优化、写入密集、缓存加载 Linux 计算机可能会出现以下现象。  

* 目录列表 `ls` 花费很长时间显示无响应。
* 与写入吞吐量相比，对文件系统的读取吞吐量大大减少。
* `nfsiostat` 报告数秒或更长时间的写入延迟。

你可能只会在 Linux 计算机执行混合写入密集型工作负载时遇到这种行为。  此外，针对单个存储终结点装载的所有 NFS 卷的体验会降级。  如果装载来自两个或多个终结点，则只有共享终结点的卷才会出现此行为。

此部分中介绍的设置文件系统缓存参数已被证实可用于解决这些问题。

## <a name="monitoring-virtual-memory"></a>监视虚拟内存

若要了解虚拟内存和写回的操作，请查看以下代码片段和输出。  Dirty 表示系统中的脏内存量，writeback 表示主动写入存储的内存量 。  

`# while true; do echo "###" ;date ; egrep "^Cached:|^Dirty:|^Writeback:|file" /proc/meminfo; sleep 5; done`

以下输出来自一个试验，其中 `vm.dirty_ratio` 和 `vm.dirty_background` 比率分别设置为物理内存的 2% 和 1%。  在这种情况下，刷新开始于 3.8 GiB，即 384-GiB 内存系统的 1%。  写回非常像 NFS 的写入吞吐量。 

```
Cons
Dirty:                                    1174836 kB
Writeback:                         4 kB
###
Dirty:                                    3319540 kB
Writeback:                         4 kB
###
Dirty:                                    3902916 kB        <-- Writes to stable storage begins here
Writeback:                         72232 kB   
###
Dirty:                                    3131480 kB
Writeback:                         1298772 kB   
``` 

## <a name="next-steps"></a>后续步骤  

* [适用于 Azure NetApp 文件的 Linux 直接 I/O 最佳做法](performance-linux-direct-io.md)
* [适用于 Azure NetApp 文件的 Linux NFS 装载选项最佳做法](performance-linux-mount-options.md)
* [适用于 Azure NetApp 文件的 Linux 并发最佳做法](performance-linux-concurrency-session-slots.md)
* [Linux NFS 文件预读最佳做法](performance-linux-nfs-read-ahead.md)
* [Azure 虚拟机 SKU 最佳做法](performance-virtual-machine-sku.md) 
* [Linux 性能基准](performance-benchmarks-linux.md) 
