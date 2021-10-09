---
title: HB 系列停用
description: HB 系列自 2021 年 9 月 1 日起停用
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 2d14b9b3adb9905cc87408db33018746e1479a1b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680084"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>在 2024 年 8 月 31 日前迁移 HB 系列虚拟机
由于 Microsoft Azure 已针对高性能计算 (HPC) 推出 HBv2 和 HBv3 系列虚拟机，因此我们建议你将原始 HB 系列虚拟机中的工作负载迁移至我们较新的产品。  

Azure [HBv2](hbv2-series.md) 和 [HBv3](hbv3-series.md) 虚拟机具有以下优势：内存带宽更大、改进了远程直接内存访问 (RDMA) 网络功能、本地固态硬盘容量更大且处理速度更快，以及能以更低成本/更高性能处理各种 HPC 工作负载。 因此，我们将于 2024 年 8 月 31 日停用 HB 系列 Azure 虚拟机大小。

## <a name="how-does-the-hb-series-migration-affect-me"></a>HB 系列迁移对我有何影响？  

2024 年 8 月 31 日之后，任何剩余的 HB 大小虚拟机订阅都将设置为已解除分配状态且停止提供服务，并且不再产生计费费用。  
> [!NOTE]
> 此次 VM 大小停用只会影响到 HB 系列中的 VM 大小。 本停用公告不适用于较新的 HBv2、HBv3 和 HC 系列虚拟机。 

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？  

你需要针对 H 系列虚拟机执行调整大小或解除分配操作。 我们建议你将原始 H 系列（包括 H 系列促销）虚拟机中的工作负载迁移至我们较新的产品/服务。

与 HB 系列相比，[HBv2](hbv2-series.md) 和 [HBv3](hbv3-series.md) VM 大幅改进了 CPU 核心体系结构、提高了内存带宽、扩大了 L3 缓存并增强了 InfiniBand 网络功能，因而能以较低成本提供更出色的 HPC 工作负载性能。 因此，HBv2 和 HBv3 系列通常可以显著提高每个成本单位产生的性能（即在支出不变的情况下最大限度提升性能），以及降低每个性能单位消耗的成本（即在性能不变的情况下最大限度降低所耗成本）。

含 HB 系列 VM 的所有区域都包含 HBv2 和 HBv3 系列虚拟机，因此你可以迁移在 HB 系列 VM 上运行的现有工作负载，而无需考虑放置虚拟机的地理位置或访问这些区域的其他服务。 

[HB 系列](hb-series.md) VM 不会于 2024 年 9 月之前停用，因此我们提前提供本指南，以便为客户提供较长时间来评估、计划和执行其迁移工作。 

### <a name="recommendations-for-workload-migration-from-hb-series-virtual-machines"></a>从 HB 系列虚拟机迁移工作负载的建议方案 

| 当前 VM 大小 | 目标 VM 大小 | 规格差异  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |更新的 CPU：AMD Rome 和 MiIan (+20-30% IPC) <br> 内存：RAM 至多扩大 2 倍  <br> 内存带宽：至多提高 30% <br> InfiniBand：200 Gb HDR（带宽提高 2 倍） <br> 最大数据磁盘数：多达 32（增加 8 倍） |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |更新的 CPU：AMD Rome 和 MiIan (+20-30% IPC) <br> 内存：RAM 至多扩大 2 倍  <br>  内存带宽：至多提高 30% <br> InfiniBand：200 Gb HDR（带宽提高 2 倍） <br> 最大数据磁盘数：多达 32（增加 8 倍） |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |更新的 CPU：AMD Rome 和 MiIan (+20-30% IPC) <br> 内存：RAM 至多扩大 2 倍 <br> 内存带宽：至多提高 30% <br> InfiniBand：200 Gb HDR（带宽提高 2 倍） <br> 最大数据磁盘数：多达 32（增加 8 倍） |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |更新的 CPU：AMD Rome 和 MiIan (+20-30% IPC) <br> 内存：RAM 至多扩大 2 倍 <br> 内存带宽：至多提高 30% <br> InfiniBand：200 Gb HDR（带宽提高 2 倍） <br> 最大数据磁盘数：多达 32（增加 8 倍） |


### <a name="migration-steps"></a>迁移步骤 
1. 选择迁移的系列和大小。 
2. 获取目标 VM 系列的配额 
3. 将当前 HB 系列 VM 大小调整为目标大小 


### <a name="get-quota-for-the-target-vm-family"></a>获取目标 VM 系列的配额 

按照指南进行操作，[请求提高 VM 系列的 vCPU 配额](../azure-portal/supportability/per-vm-quota-requests.md)。


### <a name="resize-the-current-virtual-machine"></a>调整当前虚拟机的大小
可以[重设虚拟机大小](resize-vm.md)。
