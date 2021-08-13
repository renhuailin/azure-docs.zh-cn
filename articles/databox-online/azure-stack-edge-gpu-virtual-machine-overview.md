---
title: Azure Stack Edge 设备上 VM 的概述
description: 介绍 Azure Stack Edge 设备上的虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/28/2021
ms.author: alkohli
ms.openlocfilehash: 775cb6f7cfc19b0009fc92cc5afbd3ac383b3ea3
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663852"
---
# <a name="virtual-machines-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备上的虚拟机

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文简要概述了在 Azure Stack Edge 设备上运行的虚拟机 (VM)、支持的 VM 大小，并总结了多种创建 VM 映像、部署和管理 VM 的方法。 

## <a name="about-vms"></a>关于 VM

Azure Stack Edge 解决方案提供了由 Microsoft 提供的专用硬件即服务设备，在生成数据的边缘部署边缘计算工作负载并获得快速可操作的见解。 

根据你的环境和正在运行的应用程序类型，你可以在这些设备上部署以下边缘计算工作负载之一： 

- 容器化 - 使用 IoT Edge 或 Kubernetes 来运行容器化应用程序。
- 非容器化 - 在设备上部署 Windows 和 Linux 虚拟机，以运行非容器化应用程序。 

当你需要更好地控制计算环境时，可在设备上部署 VM。 可以通过多种方式使用设备上的 VM，包括在边缘上开发、测试到运行应用程序。

## <a name="before-you-create-a-vm"></a>创建 VM 之前

在开始之前，请查看有关 VM 的以下注意事项：

- 将使用的 VM 的大小。
- 在设备上可以创建的 VM 数量上限。
- VM 运行的操作系统。
- VM 在启动后的配置。


### <a name="vm-size"></a>VM 大小

如果计划部署 VM，则需注意 VM 的大小。 你可以使用多种大小的 VM 在设备上运行应用程序和工作负载。 然后，选择的大小决定了处理能力、内存和存储容量等因素。 有关详细信息，请参阅[支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)。

若要确定在设备上可以部署的 VM 的大小和数量，请考虑设备可用的计算能力，以及正在运行的其他工作负载。 如果运行的是 Kubernetes，还要考虑 Kubernetes 主 VM 和辅助 VM 的计算要求。

|Kubernetes VM 类型|CPU 和内存要求|
|---------|---------|
|主 VM|4 核，4 GB RAM|
|辅助 VM|12 核，32 GB RAM|


有关设备的可用计算和内存，请参阅所用设备型号的[计算和内存规格](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications)。 


### <a name="vm-limits"></a>VM 限制

最多可在设备上运行 24 个 VM。 这是部署工作负载时要考虑的另一个因素。

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像

在你的设备上，只能使用具有固定虚拟硬盘 (VHD) 格式的第 1 代 VM。 VHD 可用于存储计算机操作系统 (OS) 和数据。 VHD 还可用于存储安装 OS 时使用的映像。 

用于创建 VM 映像的映像可进行通用化或专用化处理。 为 VM 创建映像时，必须准备映像。 请参阅在设备上准备和使用 VM 映像的各种方法：

- [从 VHD 准备 Windows 通用化映像](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [从 ISO 准备通用化映像](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [从 Azure VM 创建自定义 VM 映像](azure-stack-edge-gpu-create-virtual-machine-image.md)
- [使用专用映像](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md)

### <a name="extensions"></a>扩展

自定义脚本扩展适用于设备上的 VM，可在预配 VM 时通过运行脚本来帮助配置工作负载。

有关详细信息，请参阅[在设备上运行的 VM 上部署自定义脚本扩展](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md)

如果要在预配 GPU VM 时安装 GPU 驱动程序，还可以为 VM 使用 GPU 扩展。 有关详细信息，请参阅[创建 GPU VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms) 和[安装 GPU 扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)。

## <a name="create-a-vm"></a>创建 VM

若要部署 VM，首先需要创建创建 VM 所需的所有资源。 无论使用哪种方法创建 VM，都需要执行以下步骤： 

1. 连接到设备的本地 Azure 资源管理器。 
1. 识别设备上的内置订阅。
1. 提供 VM 映像。
    1. 在内置订阅中创建资源组。 资源组将包含 VM 和所有相关资源。
    2. 在设备上创建本地存储帐户，以存储将用于创建 VM 映像的 VHD。
    3. 将 Windows/Linux 源映像上传到存储帐户，用于创建托管磁盘。
    4. 使用托管磁盘创建 VM 映像。
1. 启用设备端口的计算功能，以创建虚拟交换机。
    1. 这将使用连接到已启用计算功能的端口的虚拟交换机来创建虚拟网络。  
1. 使用之前创建的 VM 映像、虚拟网络和虚拟网络接口创建 VM，以在虚拟网络中进行通信，并分配用于远程访问 VM 的公共 IP 地址。 可以选择包含数据磁盘，以便为 VM 提供更多存储空间。
 
下图显示了此部署工作流：

![VM 部署工作流关系图。](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

可以采用多种方法在设备上部署 VM。 你的选择取决于环境。 下表总结了在设备上部署 VM 的各种方法：

|方法|文章|
|---------|---------|
|Azure 门户|[通过 Azure 门户在设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。|
|模板|[通过模板在设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)|
|PowerShell|[通过 Azure PowerShell cmdlet 在设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)<br>[通过 Azure PowerShell 脚本在设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell-script.md)|
|CLI/Python|[通过 Azure CLI/Python 在设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-cli-python.md)|
|GPU|[使用 GPU 在设备上部署 VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)|


## <a name="manage-your-vm"></a>管理 VM

可以通过 Azure 门户、设备的 PowerShell 界面或直接通过 API 来管理设备上的 VM。 一些典型的管理任务包括：

- 获取有关 VM 的信息。
- 连接到 VM，启动、停止、删除 VM。
- 管理磁盘、VM 大小、网络接口、虚拟交换机
- 备份 VM 磁盘。

### <a name="get-information-about-your-vm"></a>获取有关 VM 的信息

若要通过 Azure 门户获取有关 VM 的详细信息，请执行以下步骤：

1. 转到设备的 Azure Stack Edge 资源，然后转到“虚拟机”>“概述”。 
1. 在“概述”页中，请转到“虚拟机”，选择你感兴趣的虚拟机。 然后即可查看 VM 的详细信息。 

### <a name="connect-to-your-vm"></a>连接到 VM

根据 VM 运行的 OS，可以按照以下方式连接到 VM： 

- [连接到设备上的 Windows VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-windows-vm)。
- [连接到设备上的 Linux VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-linux-vm)。

### <a name="start-stop-delete-vms"></a>开始、停止、删除 VM

你可以[打开 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#turn-on-the-vm)、[挂起或关闭 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm)。 最后，使用完 VM 后，可以[删除 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#delete-the-vm)。

### <a name="manage-network-interfaces-virtual-switches"></a>管理网络接口、虚拟交换机

你可以[添加、修改、拆离 VM 的网络接口](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)。 你还可以在设备上[创建新的虚拟交换机](azure-stack-edge-gpu-create-virtual-switch-powershell.md)，用于部署 VM。 

### <a name="manage-data-disks-vm-size"></a>管理数据磁盘、VM 大小

你可以通过 Azure 门户[向现有 VM 添加数据磁盘](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk)、[附加现有磁盘](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk)、[拆离数据磁盘](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#detach-a-data-disk)，最后[重设 VM 大小](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md#resize-a-vm)。

### <a name="back-up-vms"></a>备份 VM

你可以备份 VM 磁盘，并在出现设备故障时，从备份中还原数据。 有关详细信息，请参阅[备份 VM 磁盘](azure-stack-edge-gpu-back-up-virtual-machine-disks.md)。

## <a name="next-steps"></a>后续步骤

- 了解[适用于 Azure Stack Edge Pro GPU 的 VM 大小和类型](azure-stack-edge-gpu-virtual-machine-sizes.md)。


