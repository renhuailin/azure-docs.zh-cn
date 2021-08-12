---
title: include 文件
description: include 文件
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5a48dd8158ed08aee3da459b4373fb0e4b36fb52
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364769"
---
## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) 驱动程序

仅下表中列出的操作系统支持适用于 NC、NCv2、NCv3、NCasT4_v3、ND 和 NDv2 系列 VM 的 NVIDIA Tesla (CUDA) 驱动程序（对 NV 系列为可选项）。 在本文发布时，驱动程序下载链接是最新的。 有关最新驱动程序，请访问 [NVIDIA](https://www.nvidia.com/) 网站。

> [!TIP]
> 作为一种在 Windows Server VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Windows Server 2016 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。


| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows Server 2019 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |
| Windows Server 2016 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驱动程序

Microsoft 为用作虚拟工作站或用于虚拟应用程序的 NV 和 NVv3 系列 VM，重新分发 NVIDIA GRID 驱动程序安装程序。 请仅在下表所列操作系统上的 Azure NV 系列 VM 上安装这些 GRID 驱动程序。 这些驱动程序包括 Azure 中 GRID Virtual GPU Software 的许可。 不需要设置 NVIDIA vGPU 软件许可证服务器。

Azure 重新发布的 GRID 驱动程序不适用于非 NV 系列 VM，如 NCv2、NCv3、ND 和 NDv2 系列 VM。 NCas_T4_V3 VM 系列例外，其中 GRID 驱动程序将启用类似于 NV 系列的图形功能。

具有 Nvidia K80 GPU 的 NC 系列不支持 GRID/图形应用程序。  

请注意，Nvidia 扩展将始终安装最新的驱动程序。 我们在此处为依赖于旧版本的客户提供了上一版本的链接。

对于 Windows Server 2019、Windows Server 2016 1607、1709 和 Windows 10 (直至版本 20H2）：
- [GRID 12.2 (462.31)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRID 12.1 (461.33)](https://download.microsoft.com/download/9/7/e/97e1be73-d24b-410b-9c08-cc98c7becfa3/461.33_grid_win10_server2016_server2019_64bit_azure_swl.exe) (.exe) 

对于 Windows Server 2012 R2： 
- [GRID 12.2 (462.31)](https://download.microsoft.com/download/1/2/0/120551f5-cc05-4911-bd29-88fb2747213c/462.31_grid_server2012R2_64bit_azure_swl.exe) (.exe)
- [GRID 12.1 (461.33)](https://download.microsoft.com/download/9/9/c/99caf5c6-af9f-48b2-bcb0-af5ec64b8592/461.33_grid_server2012R2_64bit_azure_swl.exe) (.exe) 


有关所有以前的 Nvidia GRID 驱动程序链接的完整列表，请访问 [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
