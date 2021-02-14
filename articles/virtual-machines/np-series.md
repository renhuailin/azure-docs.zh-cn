---
title: NP-IN 系列-Azure 虚拟机
description: NP-IN 系列 Vm 的规格。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 2606881cec5527084c3c2788d40f8bdfd8abf5a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417082"
---
# <a name="np-series-preview"></a>NP-IN 系列 (预览) 

NP-IN 系列虚拟机由 [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) fpga 提供支持，以加速工作负载，包括机器学习推理、视频转码和数据库搜索 & 分析。 NP-IN 系列 Vm 还由具有 3.2 GHz 的所有核心 turbo 时钟速度的 Intel 8171M (Skylake) Cpu 提供支持。


[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
VM 代系支持：第 1 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | FPGA | FPGA 内存： GiB | 最大数据磁盘数 | 最大 Nic 数/预期网络带宽 (MBps)  | 
|---|---|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序
请访问 [Xilinx Runtime (XRT) 发行说明](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) ，获取受支持的操作系统的完整列表。

在预览计划期间 Microsoft Azure 工程团队将为驱动程序安装共享特定说明。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
