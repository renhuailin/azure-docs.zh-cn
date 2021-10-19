---
title: HB 系列停用
description: 2021 年 9 月 1 日开始停用 HB 系列。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: f582aad0b5af8053ff7accfac2917d4d5191ce5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712156"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>在 2024 年 8 月 31 日前迁移 HB 系列虚拟机

Microsoft Azure 为高性能计算 (HPC) 推出了 HBv2 和 HBv3 系列虚拟机 (VM)。 因此，我们建议将工作负载从原始 HB 系列 VM 迁移到我们的新产品/服务。

Azure [HBv2](hbv2-series.md) 和 [HBv3](hbv3-series.md) VM 具有以下优势：内存带宽更大、改进了远程直接内存访问 (RDMA) 网络功能、本地固态硬盘容量更大且处理速度更快，以及能以更低成本和更高性能处理各种 HPC 工作负载。 因此，我们将在 2024 年 8 月 31 日停用 HB 系列 Azure VM 大小。

## <a name="how-does-the-hb-series-migration-affect-me"></a>HB 系列迁移对我有何影响？

2024 年 8 月 31 日之后，所有剩余的 HB 大小 VM 订阅将设置为已解除分配状态。 这些订阅将停止工作，且不再产生费用。

> [!NOTE]
> 此次 VM 大小停用只会影响到 HB 系列中的 VM 大小。 本停用公告不适用于较新的 HBv2、HBv3 和 HC 系列 VM。

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？

需要调整 H 系列 VM 的大小或将其解除分配。 我们建议将工作负载从原始 H 系列 VM 和 H 系列促销 VM 迁移到我们的较新产品/服务。

[HBv2](hbv2-series.md) 和 [HBv3](hbv3-series.md) VM 提供的 HPC 工作负载性能和成本效率要高得多，因为：

- CPU 核心体系结构有了重大改进。
- 内存带宽更高。
- L3 缓存更大。
- 与 HB 系列相比，InfiniBand 网络已得到增强。

因此，HBv2 和 HBv3 系列通常可以显著提高每个成本单位产生的性能（即在支出不变的情况下最大限度提升性能），以及降低每个性能单位消耗的成本（即在性能不变的情况下最大限度降低所耗成本）。

包含 HB 系列 VM 的所有区域都包含 HBv2 和 HBv3 系列 VM。 可以迁移 HB 系列 VM 上运行的现有工作负载，而无需考虑地理位置，或者如何访问这些区域中的其他服务。

[HB 系列](hb-series.md) VM 在 2024 年 9 月之前不会停用。 我们提前发布本指南的目的是使你有充足的时间可以评估、规划和执行迁移。

### <a name="recommendations-for-workload-migration-from-hb-series-vms"></a>有关从 HB 系列 VM 迁移工作负载的建议

| 当前 VM 大小 | 目标 VM 大小 | 规格差异  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |更新的 CPU：AMD Rome 和 MiIan (+20-30% IPC) <br> 内存：RAM 至多扩大 2 倍  <br> 内存带宽：提高多达 30% <br> InfiniBand：200 Gb HDR（带宽提高 2 倍） <br> 最大数据磁盘数：多达 32（增加 8 倍） |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |更新的 CPU：AMD Rome 和 MiIan (+20-30% IPC) <br> 内存：RAM 至多扩大 2 倍  <br>  内存带宽：提高多达 30% <br> InfiniBand：200 Gb HDR（带宽提高 2 倍） <br> 最大数据磁盘数：多达 32（增加 8 倍） |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |更新的 CPU：AMD Rome 和 MiIan (+20-30% IPC) <br> 内存：RAM 至多扩大 2 倍 <br> 内存带宽：提高多达 30% <br> InfiniBand：200 Gb HDR（带宽提高 2 倍） <br> 最大数据磁盘数：多达 32（增加 8 倍） |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |更新的 CPU：AMD Rome 和 MiIan (+20-30% IPC) <br> 内存：RAM 至多扩大 2 倍 <br> 内存带宽：提高多达 30% <br> InfiniBand：200 Gb HDR（带宽提高 2 倍） <br> 最大数据磁盘数：多达 32（增加 8 倍） |

### <a name="migration-steps"></a>迁移步骤

1. 选择迁移的系列和大小。
1. 获取目标 VM 系列的配额。
1. 将当前 HB 系列 VM 大小调整为目标大小。

### <a name="get-a-quota-for-the-target-vm-family"></a>获取目标 VM 系列的配额

按照指南进行操作，[请求提高 VM 系列的 vCPU 配额](../azure-portal/supportability/per-vm-quota-requests.md)。

### <a name="resize-the-current-vm"></a>重设当前 VM 大小

可以[重设虚拟机大小](resize-vm.md)。
