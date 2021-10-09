---
title: H 系列停用
description: H 系列自 2021 年 9 月 1 日起停用
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 98465066f79f93777255a3072c472d342b557bdc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600221"
---
# <a name="migrate-your-h-and-h_promo-series-virtual-machines-by-august-31-2022"></a>在 2022 年 8 月 31 日前迁移 H 和 H_Promo 系列虚拟机
由于 Microsoft Azure 已经推出新一代高性能计算 (HPC)、常规用途和内存优化虚拟机，因此我们建议你将原始 H 系列（包括 H 系列促销）虚拟机中的工作负载迁移至我们较新的产品/服务。

Azure [HC](hc-series.md)、[HBv2](hbv2-series.md)、[HBv3](hbv3-series.md)、[Dv4](dv4-dsv4-series.md)、[Dav4](dav4-dasv4-series.md)、[Ev4](ev4-esv4-series.md) 和 [Eav4](eav4-easv4-series.md) 虚拟机具有以下优势：提高了内存带宽、改进了网络功能，并可以较低成本/更高性能迁移各种 HPC 工作负载。 因此，我们将于 2022 年 8 月 31 日停用 H 系列 Azure 虚拟机大小（H8、H8m、H16、H16r、H16m、H16mr、H8 促销、H8m 促销、H16 促销、H16r 促销、H16m 促销和 H16mr 促销）。

## <a name="how-does-the-h-series-migration-affect-me"></a>H 系列迁移对我有何影响？  

2022 年 8 月 31 日之后，上文中剩余的任何 H 系列虚拟机订阅都将设置为已解除分配状态且停止提供服务，并且不再产生计费费用。 

当前的 VM 大小停用只会影响到 [H 系列](h-series.md)（包括 H 系列促销）中的 VM 大小。 

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？  

你需要针对 H 系列虚拟机执行调整大小或解除分配操作。 我们建议你将原始 H 系列（包括 H 系列促销）虚拟机中的工作负载迁移至我们较新的产品/服务。

与 H 系列相比，[HC](hc-series.md)、[HBv2](hbv2-series.md) 和 [HBv3](hbv3-series.md) VM 在 HPC 工作负载方面存在以下优势：大幅改进了 CPU 核心体系结构、提高了内存带宽、扩大了 L3 缓存并增强了 InfiniBand 网络硬件和软件支持。 因此，HC、HBv2 和 HBv3 系列通常可以显著提高每个成本单位产生的性能（即在支出不变的情况下最大限度提升性能），以及降低每个性能单位消耗的成本（即在性能不变的情况下最大限度降低所耗成本）。 

对于常规用途的工作负载，配置相同或更多核心数的 [Dv4](dv4-dsv4-series.md)、[Dav4](dav4-dasv4-series.md) 和 Dv5 VM 可提供同等或更好的 CPU 性能，其每个物理 CPU 核心的内存数量相差无几，但 Azure 网络功能更出色且所需的总体成本更低。 

对于优化内存的工作负载，配置相同或更多核心数的 [Ev4](ev4-esv4-series.md)、[Eav4](eav4-easv4-series.md) 和 Ev5 VM 可提供同等或更好的 CPU 性能，其每个物理 CPU 核心的内存数量相差无几，但 Azure 网络功能更出色且所需的总体成本更低。 

[H 系列](h-series.md) VM（包括 H 系列促销 VM）不会于 2022 年 9 月之前停用，因此我们提前提供本指南，以便为客户提供较长时间来评估、计划和执行其迁移工作。 


### <a name="migration-steps"></a>迁移步骤 
1. 选择迁移的系列和大小。 
2. 获取目标 VM 系列的配额 
3. 将当前 H 系列 VM 大小调整为目标大小 


### <a name="breaking-changes"></a>重大更改 
如果你使用（VM 大小名称中含有“r”等）公开 InfiniBand 网络接口的 H 系列 VM 大小，并且希望新 VM 大小也支持 InfiniBand 网络，则你将无法再使用支持内置 InfiniBand 驱动程序的传统 OS 映像（CentOS 7.4 及更早版本、Windows Server 2012）。 请改为使用新式 OS 映像，例如 Azure 市场上提供的支持新式操作系统（CentOS 7.5 及更高版本、Windows Server 2016 及更高版本）和标准 OFED 驱动程序的映像。 请参阅[支持的软件堆栈](hbv3-series.md#get-started)，包括相应 VM 大小支持的 OS。 


### <a name="get-quota-for-the-target-vm-family"></a>获取目标 VM 系列的配额 

按照指南进行操作，[请求提高 VM 系列的 vCPU 配额](../azure-portal/supportability/per-vm-quota-requests.md)。


### <a name="resize-the-current-virtual-machine"></a>调整当前虚拟机的大小
可以[调整虚拟机大小](resize-vm.md)。
