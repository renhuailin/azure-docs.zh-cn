---
title: '用于现场可编程入口阵列的 Azure 虚拟机大小 (FPGA) '
description: 列出了可用于 Azure 中虚拟机的不同 FPGA 优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: b05c3198f6c3d84f852d9535a3cf0b67f66e01f6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417086"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA 优化的虚拟机大小

FPGA 优化的 VM 大小是单个或多个 Fpga 可用的专用虚拟机。 这些大小是为计算密集型工作负荷设计的。 本文提供了有关 Fpga、个 vcpu、数据磁盘和 Nic 的数量和类型的信息。 此分组中的每个大小还包括存储吞吐量及网络带宽。

- [Np-in 系列](np-series.md)大小针对工作负荷进行了优化，包括机器学习推理、视频转码和数据库搜索 & 分析。 NP-IN 系列由 Xilinx U250 加速服务提供支持。


## <a name="deployment-considerations"></a>部署注意事项

- 有关 N 系列 VM 的可用性，请查看[可用产品(按区域)](https://azure.microsoft.com/regions/services/)。

- N 系列 VM 只能按 Resource Manager 部署模型部署。

- 如果需要部署的 N 系列 VM 较多，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- 你可能需要提高 Azure 订阅中每个区域) 的核心配额 (，并增加 NP-IN 内核的单独配额。 若要请求增加配额，可免费 [建立联机客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md) 。 默认限制可能因订阅类别而异。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [GPU 加速计算](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
