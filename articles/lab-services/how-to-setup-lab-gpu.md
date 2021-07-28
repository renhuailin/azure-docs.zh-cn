---
title: 在 Azure 实验室服务中使用 GPU 设置实验室 | Microsoft Docs
description: 了解如何使用图形处理单元 (GPU) 虚拟机设置实验室。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 1ddc3d35817211d7396defa7460a2505b86c700c
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109713231"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>使用 GPU 虚拟机设置实验室

本文演示如何完成以下任务：

- 在“可视化”和“计算”图形处理单元 (GPU) 之间进行选择。
- 确保安装了相应的 GPU 驱动程序。

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>在“可视化”和“计算”GPU 大小之间进行选择
在实验室创建向导第一页的“你需要哪种虚拟机大小 ?”下拉列表中，选择课程所需的 VM 大小。  

![“新建实验室”窗格的屏幕截图，其中显示选择 VM 大小](./media/how-to-setup-gpu/lab-gpu-selection.png)

在此过程中，你可以选择“可视化”，也可以选择“计算”GPU。  必须根据学生要使用的软件选择 GPU 类型，这很重要。  

如下表中所述，“计算”GPU 的大小适用于计算密集型应用程序。  例如，[自然语言处理深度学习类类型](./class-type-deep-learning-natural-language-processing.md)使用“小型 GPU (计算)”大小。  “计算”GPU 适用于此类型的类，因为学生使用 [Data Science Virtual Machine 映像](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)提供的深度学习框架和工具，用大量数据来训练深度学习模型。

| 大小 | 核心数 | RAM | 说明 | 
| ---- | ----- | --- | ----------- | 
| 小型 GPU (计算) | -&nbsp;6&nbsp;核<br>-&nbsp;56&nbsp;GB&nbsp;RAM  | [Standard_NC6](../virtual-machines/nc-series.md) |此大小最适用于计算密集型应用程序（如人工智能 (AI) 和深度学习）。 |

“可视化”GPU 大小适用于图形密集型应用程序。  例如，[SOLIDWORKS 工程类类型](./class-type-solidworks.md)显示使用“小型 GPU (可视化)”大小。  “可视化”GPU 适用于此类型的类，因为学生与 SOLIDWORKS 3D 计算机辅助设计 (CAD) 环境交互来建模和可视化实体对象。

| 大小 | 核心数 | RAM | 说明 | 
| ---- | ----- | --- | ----------- | 
| 小型 GPU (可视化) | -&nbsp;6&nbsp;核<br>-&nbsp;56&nbsp;GB&nbsp;RAM  | [Standard_NV6](../virtual-machines/nv-series.md) | 此大小最适合使用框架（例如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。 |
| 中等 GPU (可视化) | -&nbsp;12&nbsp;核<br>-&nbsp;112&nbsp;GB&nbsp;RAM  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 此大小最适合使用框架（例如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。 |

> [!NOTE]
> 创建课堂实验室时，列表中可能不会显示某些 VM 大小。 根据实验室位置的当前容量填充列表。 如果实验室帐户创建者[允许实验室创建者选取实验室位置](allow-lab-creator-pick-lab-location.md)，则可以尝试为实验室选择其他位置，并查看 VM 大小是否可用。 有关 VM 可用性，请查看[可用产品（按区域）](https://azure.microsoft.com/regions/services/?products=virtual-machines)。

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>确保安装了相应的 GPU 驱动程序
若要利用实验室 VM 的 GPU 功能，请确保已安装相应的 GPU 驱动程序。  在实验室创建向导中选择 GPU VM 大小时，可以选择“安装 GPU 驱动程序”选项。  

![“新建实验室”屏幕截图，其中显示“安装 GPU 驱动程序”选项](./media/how-to-setup-gpu/lab-gpu-drivers.png)

如上图所示，此选项默认启用，可确保为所选 GPU 类型和映像安装最新发布的驱动程序：
- 选择“计算”GPU 大小时，实验室 VM 由 [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU 提供支持。  在这种情况下，将安装最新的[计算统一设备体系结构 (CUDA)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) 驱动程序，从而实现高性能计算。
- 选择“可视化”GPU 大小时，实验室 VM 由 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和 [GRID 技术](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)提供支持。  在这种情况下，将安装最新的 GRID 驱动程序，便于使用图形密集型应用程序。

> [!IMPORTANT]
> “安装 GPU 驱动程序”选项仅在实验室映像中没有驱动程序时才安装驱动程序。  例如，GPU 驱动程序已安装到 Azure 市场的[数据科学映像](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)。  如果使用数据科学映像创建实验室并选择“安装 GPU 驱动程序”，驱动程序将不会更新为更新版本。  若要更新驱动程序，需按照下一部分中的说明手动安装。  

### <a name="install-the-drivers-manually"></a>手动安装驱动程序
可能需要安装其他版本的驱动程序，而不是 Azure 实验室服务为你安装的版本。  本部分介绍如何手动安装相应的驱动程序，具体取决于你使用的是“计算”GPU 还是“可视化”GPU。

#### <a name="install-the-compute-gpu-drivers"></a>安装“计算”GPU 驱动程序

若要手动安装用于“计算”GPU 大小的驱动程序，请执行以下操作：

1. 在实验室创建向导中[创建实验室](./how-to-manage-classroom-labs.md)时，禁用“安装 GPU 驱动程序”设置。

1. 创建实验室后，连接到模板 VM 以安装相应的驱动程序。

   ![“NVIDIA 驱动程序下载”页的屏幕截图](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. 在浏览器中，转到[“NVIDIA 驱动程序下载”页](https://www.nvidia.com/Download/index.aspx)。  
   b. 将“产品类型”设置为“Tesla”。  
   c. 将“产品序列”设置为“K 系列”。  
   d. 根据创建实验室时所选的基础映像类型，设置“操作系统”。  
   e. 将“CUDA 工具包”设置为所需的 CUDA 驱动程序版本。  
   f. 选择“搜索”以查找驱动程序。  
   g. 选择“下载”以下载安装程序。  
   h. 运行安装程序，以便在模板 VM 上安装驱动程序。  
1. 按照[验证已安装的驱动程序](how-to-setup-lab-gpu.md#validate-the-installed-drivers)部分中的说明，验证是否正确安装了驱动程序。 
1. 在安装了课程所需的驱动程序和其他软件之后，选择“发布”以创建学生的 VM。

> [!NOTE]
> 如果使用的是 Linux 映像，则在下载安装程序后，请按照[在 Linux 上安装 CUDA 驱动程序](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#install-cuda-drivers-on-n-series-vms)中的说明安装驱动程序。

#### <a name="install-the-visualization-gpu-drivers"></a>安装“可视化”GPU 驱动程序

若要手动安装用于“可视化”GPU 大小的驱动程序，请执行以下操作：

1. 在实验室创建向导中[创建实验室](./how-to-manage-classroom-labs.md)时，禁用“安装 GPU 驱动程序”设置。
1. 创建实验室后，连接到模板 VM 以安装相应的驱动程序。
1. 按照适用于你的操作系统的说明，在模板 VM 上安装 Microsoft 提供的 GRID 驱动程序：
   -  [Windows NVIDIA GRID 驱动程序](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID 驱动程序](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#nvidia-grid-drivers)
  
1. 重启模板 VM。
1. 按照[验证已安装的驱动程序](how-to-setup-lab-gpu.md#validate-the-installed-drivers)部分中的说明，验证是否正确安装了驱动程序。
1. 在安装了课程所需的驱动程序和其他软件之后，选择“发布”以创建学生的 VM。

### <a name="validate-the-installed-drivers"></a>验证已安装的驱动程序
本部分介绍如何验证是否正确安装了 GPU 驱动程序。

#### <a name="windows-images"></a>Windows 映像
1.  按照[在运行 Windows 的 N 系列 VM 上安装 NVIDIA GPU 驱动程序](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation)的“验证驱动程序安装”部分中的说明进行操作。
1.  如果使用的是“可视化”GPU，则还可以执行以下操作：
    - 在“NVIDIA 控制面板”中查看和调整 GPU 设置。 为此，请在“Windows 控制面板”中选择“硬件”，然后选择“NVIDIA 控制面板”。

      ![“Windows 控制面板”屏幕截图，其中显示“NVIDIA 控制面板”链接](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - 使用“任务管理器”查看 GPU 性能。  为此，请选择“性能”选项卡，然后选择“GPU”选项。

       ![“任务管理器”屏幕截图，其中显示 GPU“性能”选项卡](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > 仅可以针对“可视化”GPU 访问“NVIDIA 控制面板”设置。  如果尝试针对“计算”GPU 打开“NVIDIA 控制面板”，则会出现此错误：“NVIDIA 显示器设置不可用。  当前未使用附加到 NVIDIA GPU 的显示器。”  同样，任务管理器中的 GPU 性能信息仅针对“可视化”GPU 提供。

 根据情况，可能还需进行其他验证，以确保正确配置 GPU。  请参阅 [Python 和 Jupyter Notebooks](./class-type-jupyter-notebook.md#template-virtual-machine) 了解类类型，其中说明了需使用特定版本的驱动程序的示例。

#### <a name="linux-images"></a>Linux 映像
按照[在运行 Linux 的 N 系列 VM 上安装 NVIDIA GPU 驱动程序](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation)的“验证驱动程序安装”部分中的说明进行操作。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [创建和管理实验室](how-to-manage-classroom-labs.md)
- [SOLIDWORKS 计算机辅助设计 (CAD) 类类型](class-type-solidworks.md)
- [MATLAB（矩阵实验室）类类型](class-type-matlab.md)