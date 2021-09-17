---
title: Azure VM 大小 - 存储 | Microsoft Docs
description: 列出适用于 Azure 中虚拟机的各种存储优化大小。 列出了有关此系列中各大小的 vCPU 数、数据磁盘数和 NIC 数以及存储吞吐量和网络带宽的信息。
ms.subservice: vm-sizes-storage
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a6210583dc450fa31cd0e76c158cdab40e1c578b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695738"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>存储优化虚拟机大小

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

> [!TIP]
> 请尝试使用[虚拟机选择器工具](https://aka.ms/vm-selector)查找最适合你的工作负载的其他尺寸。

存储优化 VM 大小可提供较高的磁盘吞吐量和 IO，非常适合于大数据、SQL、NoSQL 数据库、数据仓库和大型事务数据库。  示例包括 Cassandra、MongoDB、Cloudera 和 Redis。 本文介绍 vCPU、数据磁盘和 NIC 的数量以及每个优化大小的本地存储吞吐量和网络带宽。

[Lsv2 系列](lsv2-series.md)的特点为：高吞吐量、低延迟、在 [AMD EPYC<sup>TM</sup> 7551 处理器](https://www.amd.com/en/products/epyc-7000-series)上运行的直接映射的本地 NVMe 存储、所有核心提升为 2.55GHz、最大提升为 3.0GHz。 Lsv2 系列 VM 提供同时进行的多线程配置中的 8 到 80 个 vCPU。  每个 vCPU 有 8 GiB 内存，每 8 个 vCPU 有一个 1.92TB 的 NVMe SSD M.2 设备，并且在 L80s v2 上可以使用最多 19.2TB (10x1.92TB)。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

了解如何针对 [Windows](windows/storage-performance.md) 或 [Linux](linux/storage-performance.md) 优化 Lsv2 系列虚拟机上的性能。

有关 Azure 如何命名其 VM 的详细信息，请参阅 [Azure 虚拟机大小命名约定](./vm-naming-conventions.md)。