---
title: 现场可编程门阵列 (FPGA) 的 Azure 虚拟机大小
description: 列出了 Azure 中虚拟机可用的不同 FPGA 优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: c421c30e9a8f117806e9e93b2df5154bd881e7c2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697928"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA 优化虚拟机大小

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

FPGA 优化 VM 大小是具有单个或多个 FPGA 的专用虚拟机。 这些大小专为计算密集型工作负载而设计。 本文介绍有关 FPGA、vCPU、数据磁盘和 NIC 的数量和类型的信息。 此分组中的每个大小还包括存储吞吐量及网络带宽。

- [NP 系列](np-series.md)大小针对工作负载进行了优化，包括机器学习推理、视频转码及数据库搜索和分析。 NP 系列由 Xilinx U250 加速器提供支持。


## <a name="deployment-considerations"></a>部署注意事项

- 有关 N 系列 VM 的可用性，请查看[可用产品(按区域)](https://azure.microsoft.com/regions/services/)。

- N 系列 VM 只能按 Resource Manager 部署模型部署。

- 如果需要部署的 N 系列 VM 较多，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- 你可能需要增加 Azure 订阅中的核心配额（按区域）以及增加单独针对 NP 核心的配额。 若要请求增加配额，可免费 [建立联机客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md) 。 默认限制可能因订阅类别而异。

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
