---
title: NV 系列迁移指南
description: NV 系列迁移指南
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: e91da8b052ba9ecce4a345c610b2299de22de1b3
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254258"
---
#  <a name="nv-series-migration-guide"></a>NV 系列迁移指南 

Microsoft Azure 数据中心现在提供更多大小的功能强大 GPU VM，我们建议你对工作负载进行评估，并迁移 NV 和 NV_Promo 系列的虚拟机 (VM)。 可以将这些旧的 VM 迁移到新 VM 系列（例如 NVsv3 和 NVasv4 系列），以提升性能，并降低成本。 NVsv3 VM 系列由 Nvidia M60 GPU 提供技术支持，NVasv4 系列由 AMD Radeon Instinct MI25 GPU 提供技术支持。  NV、NV_Promo 系列以及较新的 NVsv3 和 NVasv4 之间的主要区别是，新系列使性能得到提升，支持高级存储，并能提供各种细分的 GPU 大小和多种 GPU 配置供你选择。 NVsv3 和 NVasv4 系列都具有更多新式核心和更大的容量。  

以下部分总结了旧的 NV 系列与 NVsv3 和 NVv4 系列之间的区别。
 
 ## <a name="nvsv3-series"></a>NVsv3 系列 

NVv3 系列虚拟机由 NVIDIA Tesla M60 GPU 和 NVIDIA GRID 技术提供支持，具备 Intel E5-2690 v4 (Broadwell) CPU 和 Intel 超线程技术。 此类虚拟机面向 GPU 加速图形应用程序和虚拟桌面，客户希望利用这些应用和桌面直观呈现数据、模拟要查看的结果、处理 CAD 或渲染和流式处理内容。 此外，这些虚拟机还能运行编码和渲染等单精度工作负荷。 NVv3 虚拟机支持高级存储，且与 NV 系列相比，系统内存 (RAM) 增加了一倍。 有关最新的规格，请参阅 [GPU 加速的计算 VM 大小：NVsv3 系列](nvv3-series.md)

| 当前 VM 大小 | 目标 VM 大小 | 规格差异  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV12s_v3  | vCPU 数量：12 (+6) <br> 内存：GiB 112 (+56) <br> 临时存储 (SSD) GiB：320 (-20) <br> 最大数据磁盘数：12 (-12) <br> 加速网络：是 <br> 高级存储：是  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU 数量：24 (+12) <br>内存：GiB 224 (+112) <br>临时存储 (SSD) GiB：640 (-40)<br>最大数据磁盘数：24 (-24)<br>加速网络：是 <br>高级存储：是   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU 数量：48 (+24) <br>内存：GiB 448 (+224) <br>临时存储 (SSD) GiB：1280 (-160) <br>最大数据磁盘数：32 (-32) <br>加速网络：是 <br>高级存储：是   |

## <a name="nvsv4-series"></a>NVsv4 系列 

NVv4 系列虚拟机由 AMD Radeon Instinct MI25 GPU 和 AMD EPYC 7V12(Rome) CPU 提供技术支持。 Azure 通过 NVv4 系列引入使用部分 GPU 的虚拟机。 选取适当大小虚拟机来运行 GPU 加速图形应用程序和虚拟桌面，可选范围为具有 2 GiB 帧缓冲区的 1/8 个 GPU 到具有 16 GiB 帧缓冲区的完整 GPU。 NVv4 虚拟机目前只支持 Windows 来宾操作系统。 有关最新的规格，请参阅 [GPU 加速的计算 VM 大小：NVsv4 系列](nvv4-series.md)

| 当前 VM 大小 | 目标 VM 大小 | 规格差异  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV16as_v4  | vCPU 数量：16 (+10) <br>内存：GiB 56  <br>临时存储 (SSD) GiB：352 (+12) <br>最大数据磁盘数：16 (-8) <br>加速网络：是 <br>高级存储：是   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU 数量：32 (+20) <br>内存：GiB 112 <br>临时存储 (SSD) GiB：704 (+24) <br>最大数据磁盘数：32 (+16)<br>加速网络：是 <br>高级存储：是   |
|Standard_NV24 <br> Standard_NV24_Promo |不适用  | 空值  |

## <a name="migration-steps"></a>迁移步骤 
 

常规更改 

1. 选择迁移的系列和大小。 

2. 获取目标 VM 系列的配额 

3. 将当前 NV 系列 VM 大小调整为目标大小 

  如果目标大小为 NVv4，请务必删除 Nvidia GPU 驱动程序，并安装 AMD GPU 驱动程序 
  
## <a name="breaking-changes"></a>重大更改 

## <a name="select-target-size-for-migration"></a>选择迁移的目标大小 
评估当前使用情况后，确定需要哪种类型的 GPU VM。 根据工作负载要求，有几种不同的选择。 下面是选择方法：  

如果工作负载是图形/可视化效果，对使用 Nvidia GPU 有较强的依赖性，我们建议迁移到 NVsv3 系列。  

如果工作负载是图形/可视化效果，对特定类型的 GPU 的依赖性不强，则建议迁移到 NVsv3 或 NVVasv4 系列。 
> [!Note]
>最佳做法是结合成本和性能来选择 VM 大小。 本指南中的建议是基于对 NV 和 NV_Promo 大小的性能指标与另一个 VM 系列中最接近的匹配进行一对一的比较。
>在确定适当的大小之前，请使用 Azure 定价计算器进行成本比较。

## <a name="get-quota-for-the-target-vm-family"></a>获取目标 VM 系列的配额 

按照指南进行操作，[请求提高 VM 系列的 vCPU 配额](../azure-portal/supportability/per-vm-quota-requests.md)。 选择 NVSv3 系列或 NVv4 系列作为 VM 系列名称，具体取决于为迁移选择的目标 VM 大小。
## <a name="resize-the-current-virtual-machine"></a>调整当前虚拟机的大小
可以[通过 Azure 门户或 PowerShell 调整虚拟机的大小](./windows/resize-vm.md)。 也可以[使用 Azure CLI 调整虚拟机的大小](./linux/change-vm-size.md)。 

## <a name="faq"></a>常见问题解答
问：应为目标 VM 大小使用哪种 GPU 驱动程序？ 

答：对于 NVsv3 系列，可以使用 [Nvidia GRID 驱动程序](./windows/n-series-driver-setup.md)。 对于 NVv4，可以使用 [AMD GPU 驱动程序](./windows/n-series-amd-driver-setup.md)。 

问：我目前使用的是 Nvidia GPU 驱动程序扩展。 它是否适用于目标 VM 大小？ 

答：目前的 [Nvidia 驱动程序扩展](./extensions/hpccompute-gpu-windows.md)适用于 NVsv3。 如果目标 VM 大小为 NVv4，可以使用 [AMD GPU 驱动程序扩展](./extensions/hpccompute-amd-gpu-windows.md)。 
       
问：如果依赖于 CUDA，应使用哪个目标 VM 系列？ 

 答：NVv3 支持 CUDA。 使用 AMD GPU 的 NVv4 VM 系列不支持 CUDA。  
