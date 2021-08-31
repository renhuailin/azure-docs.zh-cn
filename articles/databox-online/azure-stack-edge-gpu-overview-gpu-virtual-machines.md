---
title: Azure Stack Edge Pro GPU 设备上的 GPU VM 概述
description: 介绍如何在 Azure Stack Edge Pro GPU 上使用针对 GPU 加速工作负载进行优化的虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: alkohli
ms.openlocfilehash: d8660ace369fe0da0da384dceae6bff35594df74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778535"
---
# <a name="gpu-virtual-machines-for-azure-stack-edge-pro-gpu-devices"></a>Azure Stack Edge Pro GPU 设备的 GPU 虚拟机

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Azure Stack Edge Pro GPU 设备上的 GPU 加速工作负载需要 GPU 虚拟机。 本文概述了 GPU VM，包括受支持的 OS、GPU 驱动程序和 VM 大小。 还讨论了用于 Kubernetes 群集的 GPU VM 的部署选项。

## <a name="about-gpu-vms"></a>关于 GPU VM

Azure Stack Edge 设备配备了 1 个或 2 个 Nvidia 的 Tesla T4 GPU。 要在这些设备上部署 GPU 加速的 VM 工作负载，请使用 GPU 优化的 VM 大小。 例如，应使用 NC T4 v3 系列来部署采用 T4 GPU 的推理工作负载。 有关详细信息，请参阅 [NC T4 v3 系列 VM](../virtual-machines/nct4-v3-series.md)。

要利用 Azure N 系列 VM 的 GPU 功能，必须安装 Nvidia GPU 驱动程序。 Nvidia GPU 驱动程序扩展可以安装适当的 Nvidia CUDA 或 GRID 驱动程序。 可以[使用模板或通过 Azure 门户安装 GPU 扩展](#gpu-vm-deployment)。

可以在部署 VM 后[使用 Azure 资源管理器模板来安装和管理扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)。 在 Azure 门户中，你可以在部署 VM 时或之后安装 GPU 扩展；有关说明，请参阅[在 Azure Stack Edge 设备上部署 GPU VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)。

如果你的设备配置了 Kubernetes 群集，请确保在部署 GPU VM 之前查看 [Kubernetes 群集的部署注意事项](#gpu-vms-and-kubernetes)。

## <a name="supported-os-and-gpu-drivers"></a>支持的 OS 和 GPU 驱动程序 

适用于 Windows 和 Linux 的 Nvidia GPU 驱动程序扩展支持以下 OS 版本。

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

## <a name="gpu-vm-deployment"></a>GPU VM 部署

可以通过 Azure 门户或使用 Azure 资源管理器模板部署 GPU VM。 在创建 VM 后安装 GPU 扩展。<!--Wording still needs work!-->

- 门户：在 Azure 门户中，可以在[创建 VM 时快速安装 GPU 扩展](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)，或[在部署 VM 后]()执行此操作。<!--Can they remove the GPU extension. Tomorrow, create a new GPU VM to test.-->

- 模板：使用 Azure 资源管理器模板，[创建一个虚拟机](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment)，然后[安装 GPU 扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)。


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

## <a name="next-steps"></a>后续步骤
- 了解如何[部署 GPU VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)。
- 了解如何在设备上运行的 GPU VM 上[安装 GPU 扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)。
