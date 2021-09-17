---
title: Azure VM 大小 - GPU | Microsoft Docs
description: 列出了 Azure 中虚拟机可用的不同 GPU 优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 6cf54dc041d844c14abb68b1fd7463cfa62f5e80
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695776"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 优化虚拟机大小

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

> [!TIP]
> 请尝试使用[虚拟机选择器工具](https://aka.ms/vm-selector)查找最适合你的工作负载的其他尺寸。

GPU 优化 VM 大小是具有单个、多个或部分 GPU 的专用虚拟机。 这些大小是针对计算密集型、图形密集型和可视化工作负荷设计的。 本文介绍有关 GPU、vCPU、数据磁盘和 NIC 的数量和类型的信息。 此分组中的每个大小还包括存储吞吐量及网络带宽。

- [NCv3 系列](ncv3-series.md)和 [NC T4_v3 系列](nct4-v3-series.md)大小针对计算密集型 GPU 加速的应用程序进行了优化。 一些示例包括基于 CUDA 和 OpenCL 的应用程序以及模拟、AI 和深度学习。 NC T4 v3 系列专用于采用 NVIDIA Tesla T4 GPU 和 AMD EPYC2 Rome 处理器的推理工作负荷。 NCv3 系列带有 NVIDIA Tesla V100 GPU，专用于高性能计算和 AI 工作负载。

- [ND A100 v4 系列](nda100-v4-series.md)大小专注于纵向扩展和横向扩展深度学习训练以及加速的 HPC 应用程序。 ND A100 v4 系列使用 8 个 NVIDIA A100 TensorCore GPU，每个 GPU 都配备 200 千兆位 Mellanox InfiniBand HDR 连接和 40 GB GPU 内存。

- [NV 系列](nv-series.md)和 [NVv3 系列](nvv3-series.md)大小已针对使用框架（如 OpenGL 和 DirectX）的远程可视化、流式处理、游戏、编码和 VDI 方案进行了优化和设计。 这些 VM 由 NVIDIA Tesla M60 GPU 提供支持。

- [NVv4 系列](nvv4-series.md) VM 大小已针对 VDI 和远程可视化进行了优化和设计。 NVv4 具有已分区的 GPU，可为需要较少 GPU 资源的工作负载提供适当的大小。 这些 VM 由 AMD Radeon Instinct MI25 GPU 提供支持。 NVv4 VM 目前只支持 Windows 来宾操作系统。

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用 Azure N 系列 VM 的 GPU 功能，必须安装 NVIDIA 或 AMD GPU 驱动程序。

- 对于 NVIDIA GPU 支持的 VM，[NVIDIA GPU 驱动程序扩展](./extensions/hpccompute-gpu-windows.md)会安装相应的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](./extensions/hpccompute-gpu-windows.md)。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](./extensions/overview.md)。

   或者，你也可以手动安装 NVIDIA GPU 驱动程序。 请参阅[在运行 Windows 的 N 系列 VM 上安装 NVIDIA GPU 驱动程序](./windows/n-series-driver-setup.md)或[在运行 Linux 的 N 系列 VM 上安装 NVIDIA GPU 驱动程序](./linux/n-series-driver-setup.md)，以了解受支持的操作系统、驱动程序以及安装和验证步骤。

- 对于 AMD GPU 支持的 VM，[AMD GPU 驱动程序扩展](./extensions/hpccompute-amd-gpu-windows.md) 会安装相应的 AMD 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](./extensions/overview.md)。

   或者，你也可以手动安装 AMD GPU 驱动程序。 请参阅[在运行 Windows 的 N 系列 VM 上安装 AMD GPU 驱动程序](./windows/n-series-amd-driver-setup.md)，以了解受支持的操作系统、驱动程序、安装和验证步骤。

## <a name="deployment-considerations"></a>部署注意事项

- 有关 N 系列 VM 的可用性，请查看[可用产品(按区域)](https://azure.microsoft.com/regions/services/)。

- N 系列 VM 只能按 Resource Manager 部署模型部署。

- N 系列的 VM 在对其磁盘支持的 Azure 存储类型方面有所不同。 NC 和 NV VM 仅支持标准磁盘存储 (HDD) 所支持的 VM 磁盘。 所有其他 GPU VM 都支持由标准磁盘存储和高级磁盘存储 (SSD) 支持的 VM 磁盘。

- 如果需要部署的 N 系列 VM 较多，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- 可能需要提高 Azure 订阅中的核心配额（按区域）以及单独针对 NC、NCv2、NCv3、ND、NDv2、NV 或 NVv2 核心的配额。 若要请求增加配额，可免费 [建立联机客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md) 。 默认限制可能因订阅类别而异。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [高性能计算](sizes-hpc.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
