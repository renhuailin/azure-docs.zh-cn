---
title: H 系列停用
description: H 系列自 2021 年 9 月 1 日起停用。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 1ad9a9384d00ec0961235c167379d9984ec417fe
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705244"
---
# <a name="migrate-your-h-and-h-series-promo-virtual-machines-by-august-31-2022"></a>在 2022 年 8 月 31 日前迁移 H 和 H 系列促销虚拟机

Microsoft Azure 引入了更新一代的高性能计算 (HPC)、常规用途和内存优化虚拟机 (VM)。 因此，建议将工作负载从原始的 H 系列和 H 系列促销 VM 迁移到我们的新产品/服务。

Azure [HC](hc-series.md)、[HBv2](hbv2-series.md)、[HBv3](hbv3-series.md)、[Dv4](dv4-dsv4-series.md)、[Dav4](dav4-dasv4-series.md)、[Ev4](ev4-esv4-series.md) 和 [Eav4](eav4-easv4-series.md) VM 具有以下优势：提高了内存带宽、改进了网络功能，并可以较低成本和更高性能迁移各种 HPC 工作负载。 2022 年 8 月 31 日，我们将停用以下 H 系列 Azure VM 大小：

- H8
- H8m
- H16
- H16r
- H16m
- H16mr
- H8 促销
- H8m 促销
- H16 促销
- H16r 促销
- H16m 促销
- H16mr 促销

## <a name="how-does-the-h-series-migration-affect-me"></a>H 系列迁移对我有何影响？

2022 年 8 月 31 日之后，上述列表中任何剩余的 H 系列 VM 订阅都将设置为解除分配状态。 它们将停止工作，不再产生账单费用。

当前的 VM 大小停用仅影响 [H 系列](h-series.md)（包括 H 系列促销）中的 VM 大小。

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？

你需要重设 H 系列 VM 的大小或解除分配。 建议将工作负载从原始的 H 系列 VM 和 H 系列促销 VM 迁移到我们的新产品/服务。

**HPC 工作负载：** [HC](hc-series.md)、[HBv2](hbv2-series.md) 和 [HBv3](hbv3-series.md) VM 提供的 HPC 工作负载性能和成本效率大幅提升，原因如下：

- CPU 核心体系结构有了重大改进。
- 内存带宽更高。
- L3 缓存更大。
- 与 H 系列相比，增强了 InfiniBand 网络硬件和软件支持。

因此，HC、HBv2 和 HBv3 系列通常可以显著提高每个成本单位产生的性能（即在支出不变的情况下最大限度提升性能），以及降低每个性能单位消耗的成本（即在性能不变的情况下最大限度降低所耗成本）。

**常规用途的工作负载：** 配置相同或更多核心数的 [Dv4](dv4-dsv4-series.md)、[Dav4](dav4-dasv4-series.md) 和 Dv5 VM 可提供同等或更好的 CPU 性能，其每个物理 CPU 核心的内存数量相差无几，但 Azure 网络功能更出色且所需的总体成本更低。

**内存优化工作负载：** 配置相同或更多核心数的 [Ev4](ev4-esv4-series.md)、[Eav4](eav4-easv4-series.md) 和 Ev5 VM 可提供同等或更好的 CPU 性能，其每个物理 CPU 核心的内存数量相差无几，但 Azure 网络功能更出色且所需的总体成本更低。

[H 系列](h-series.md)和 H 系列促销 VM 要到 2022 年 9 月才会停用。 我们提前发布本指南，是为了让你有充足的时间来评估、规划和执行迁移。

### <a name="migration-steps"></a>迁移步骤

1. 选择迁移的系列和大小。
1. 获取目标 VM 系列的配额。
1. 将当前 H 系列 VM 大小重设为目标大小。

### <a name="breaking-changes"></a>中断性变更

如果你使用公开 InfiniBand 网络接口的 H 系列 VM 大小（VM 大小名称中含有“r”等），并且希望新 VM 大小也支持 InfiniBand 网络，则你将无法再使用支持内置 InfiniBand 驱动程序的传统 OS 映像（CentOS 7.4 及更早版本、Windows Server 2012）。

请改为使用新式 OS 映像，例如 Azure 市场中提供的支持新式操作系统（CentOS 7.5 及更高版本、Windows Server 2016 及更高版本）和标准 OFED 驱动程序的映像。 请参阅[支持的软件堆栈](hbv3-series.md#get-started)，包括相应 VM 大小支持的 OS。

### <a name="get-a-quota-for-the-target-vm-family"></a>获取目标 VM 系列的配额

按照指南进行操作，[请求提高 VM 系列的 vCPU 配额](../azure-portal/supportability/per-vm-quota-requests.md)。

### <a name="resize-the-current-vm"></a>重设当前 VM 大小

可以[重设虚拟机大小](resize-vm.md)。
