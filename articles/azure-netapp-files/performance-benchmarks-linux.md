---
title: 适用于 Linux 的 Azure NetApp 文件性能基准 | Microsoft Docs
description: 介绍 Azure NetApp 文件为 Linux 提供的性能基准。
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
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: 814474418386041d2b20759c4fb2accc534e8b9d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355865"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>适用于 Linux 的 Azure NetApp 文件性能基准

本文介绍 Azure NetApp 文件为 Linux 提供的性能基准。

## <a name="linux-scale-out"></a>Linux 横向扩展

本部分介绍 Linux 工作负荷吞吐量和工作负荷 IOPS 的性能基准。

### <a name="linux-workload-throughput"></a>Linux 工作负荷吞吐量  

下图表示一个 64 千位字节 (KiB) 顺序工作负载和一个 1 TiB 工作集。 它显示单个 Azure NetApp 文件卷的处理速度可在大约 1,600 MiB/秒（纯顺序写入）到大约 4,500 MiB/秒（纯顺序读取）之间。  

该图说明从纯读取到纯写入一次减少了 10%。 它演示了在使用不同的读/写比率（100%:0%、90%:10%、80%:20%，等等）时可以预期的结果。

![Linux 工作负荷吞吐量](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux 工作负荷 IOPS  

下图表示一个 4 千位字节 (KiB) 随机工作负载和一个 1 TiB 工作集。 该图显示 Azure NetApp 文件卷的处理次数可在大约 130,000 次（纯随机写入）到大约 460,000 次（纯随机读取）之间。  

此图说明从纯读取到纯写入一次减少了 10%。 它演示了在使用不同的读/写比率（100%:0%、90%:10%、80%:20%，等等）时可以预期的结果。

![Linux 工作负荷 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux 纵向扩展  

本部分的图显示了使用 NFSv3 对客户端装载选项进行验证测试的结果。 有关详细信息，请参阅 [Linux 装载选项的 `nconnect` 部分](performance-linux-mount-options.md#nconnect)。

这些图比较了 `nconnect` 相对于非 `connected` 装载卷的优势。 在图中，FIO 使用 64-KiB 顺序工作负载（执行此处所述的测试时 Azure NetApp 文件支持的最大 I/O 大小）从 us-west2 Azure 区域中的单个 D32s_v4 实例生成了工作负载。 Azure NetApp 文件现在支持更大的 I/O 大小。 有关更多详细信息，请参阅[Linux 装载选项的 `rsize` 和 `wsize` 部分](performance-linux-mount-options.md#rsize-and-wsize)。

### <a name="linux-read-throughput"></a>Linux 读取吞吐量  

下图显示了使用 `nconnect` 时，64-KiB 顺序读取速度约为 3,500 MiB/秒，大约是使用非 `nconnect` 时的 2.3 倍。

![Linux 读取吞吐量](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux 写入吞吐量  

下图显示了顺序写入。 这些图指示就顺序写入来说，`nconnect` 没有明显的优势。 1,500 MiB/秒大致是顺序写入量的上限和 D32s_v4 实例的流出量限制。

![Linux 写入吞吐量](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux 读取 IOPS  

下图显示了使用 `nconnect` 时 4-KiB 随机读取约为 200,000 读取 IOPS，大约是使用非 `nconnect` 时的 3 倍。

![Linux 读取 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux 写入 IOPS  

下图显示了使用 `nconnect` 时 4-KiB 随机写入约为 135,000 写入 IOPS，大约是使用非 `nconnect` 时的 3 倍。

![Linux 写入 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件：充分利用云存储](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
