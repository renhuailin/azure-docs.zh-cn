---
title: Azure Stack Edge Pro GPU 设备上的 GPU VM 概述和部署
description: 介绍如何使用模板在 Azure Stack Edge Pro GPU 设备上创建和管理 GPU 虚拟机 (VM)。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/28/2021
ms.author: alkohli
ms.openlocfilehash: 754cb296d6edebe4a8026df612fc52113e171a1c
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663869"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上部署 GPU VM

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文概述了 Azure Stack Edge Pro GPU 设备上的 GPU 虚拟机 (VM)。 本文还介绍了如何使用 Azure 资源管理器模板创建 GPU VM。 


## <a name="about-gpu-vms"></a>关于 GPU VM

Azure Stack Edge 设备配备了 1 个或 2 个 Nvidia 的 Tesla T4 GPU。 要在这些设备上部署 GPU 加速的 VM 工作负载，请使用 GPU 优化的 VM 大小。 例如，应使用 NC T4 v3 系列来部署采用 T4 GPU 的推理工作负载。 

有关详细信息，请参阅 [NC T4 v3 系列 VM](../virtual-machines/nct4-v3-series.md)。

## <a name="supported-os-and-gpu-drivers"></a>支持的 OS 和 GPU 驱动程序 

要利用 Azure N 系列 VM 的 GPU 功能，必须安装 Nvidia GPU 驱动程序。 

Nvidia GPU 驱动程序扩展可以安装适当的 Nvidia CUDA 或 GRID 驱动程序。 你可以使用 Azure 资源管理器模板来安装或管理扩展。

### <a name="supported-os-for-gpu-extension-for-windows"></a>适用于 Windows 的 GPU 扩展支持的 OS

此扩展支持以下操作系统 (OS)。 其他版本可能也有效，但尚未在 Azure Stack Edge 设备上运行的 GPU VM 上对这些版本进行内部测试。

| 分发 | 版本 |
|---|---|
| Windows Server 2019 | 核心 |
| Windows Server 2016 | 核心 |

### <a name="supported-os-for-gpu-extension-for-linux"></a>适用于 Linux 的 GPU 扩展支持的 OS

此扩展支持以下 OS 发行版，具体取决于特定 OS 版本对驱动程序的支持。 其他版本可能也有效，但尚未在 Azure Stack Edge 设备上运行的 GPU VM 上对这些版本进行内部测试。


| 分发 | 版本 |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU VM 和 Kubernetes

在设备上部署 GPU VM 之前，如果设备上已配置 Kubernetes，请了解以下注意事项。

#### <a name="for-1-gpu-device"></a>对于使用 1 个 GPU 的设备： 

- 在设备上创建 GPU VM，然后配置 Kubernetes：在这种情况下，创建 GPU VM 和配置 Kubernetes 均能成功。 此时 Kubernetes 无法访问 GPU。

- 在设备上配置 Kubernetes，然后创建 GPU VM：在这种情况下，Kubernetes 将回收设备上的 GPU，且创建 VM 将失败，因为没有可用的 GPU 资源。

#### <a name="for-2-gpu-device"></a>对于使用 2 个 GPU 的设备

- 在设备上创建 GPU VM，然后配置 Kubernetes：在这种情况下，你创建的 GPU VM 将回收设备上的一个 GPU，且配置 Kubernetes 将成功，并回收剩余的一个 GPU。 

- 在设备上创建两个GPU VM，然后配置 Kubernetes：在这种情况下，两个 GPU VM 将回收设备上的两个 GPU，并且 Kubernetes 配置成功，没有 GPU。 

- 在设备上配置 Kubernetes，然后创建 GPU VM：在这种情况下，Kubernetes 将回收设备上的两个 GPU，且创建 VM 将失败，因为没有可用的 GPU 资源。

<!--Li indicated that this is fixed. If you have GPU VMs running on your device and Kubernetes is also configured, then anytime the VM is deallocated (when you stop or remove a VM using Stop-AzureRmVM or Remove-AzureRmVM), there is a risk that the Kubernetes cluster will claim all the GPUs available on the device. In such an instance, you will not be able to restart the GPU VMs deployed on your device or create GPU VMs. -->


## <a name="create-gpu-vms"></a>创建 GPU VM

在设备上部署 GPU VM 时，请执行以下步骤：

1. 确定设备是否将同时运行 Kubernetes。 如果设备将运行 Kubernetes，需要先创建 GPU VM，然后再配置 Kubernetes。 如果先配置了 Kubernetes，它将回收所有可用的 GPU 资源，创建 GPU VM 便会失败。

1. [将 VM 模板和参数文件下载](https://aka.ms/ase-vm-templates)到客户端计算机。 将它解压缩到将用作工作目录的那个目录中。

1. 在 Azure Stack Edge 设备上部署 VM 之前，必须先将客户端配置为使用 Azure PowerShell 通过 Azure 资源管理器连接到设备。 有关详细说明，请参阅[连接到 Azure Stack Edge 设备上的 Azure 资源管理器](azure-stack-edge-gpu-connect-resource-manager.md)。

1. 若要创建 GPU VM，请执行[使用模板在 Azure Stack Edge 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 或[使用 Azure 门户在 Azure Stack Edge 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) 中的所有步骤，以下差异除外： 

            
    1. 如果使用模板创建 VM，则在指定 GPU VM 大小时，请确保在 `CreateVM.parameters.json` 中使用 NCasT4-v3 系列，因为它们受 GPU VM 支持。 有关详细信息，请参阅 [GPU VM 支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)。

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```
        如果使用 Azure 门户创建 VM，则仍可从 NCasT4-v3 系列中选择 VM 大小。

    1. 成功创建 GPU VM 后，可以在 Azure 门户的 Azure Stack Edge 资源中的虚拟机列表中查看此 VM。

        ![Azure 门户中虚拟机列表中的 GPU VM](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. 选择 VM 并进一步了解详细信息。 复制分配给 VM 的 IP。

    ![Azure 门户中分配给 GPU VM 的 IP](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. 如有需要，可将计算网络切换回你需要的任何位置。 


创建 VM 后，可使用扩展模板部署 GPU 扩展。


> [!NOTE]
> 将设备软件版本从 2012 更新到更高版本时，需要手动停止 GPU VM。



## <a name="next-steps"></a>后续步骤

- 了解如何在设备上运行的 GPU VM 上[安装 GPU 扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)。
