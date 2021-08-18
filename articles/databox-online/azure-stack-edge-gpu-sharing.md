---
title: Azure Stack Edge Pro GPU 设备上的 GPU 共享
description: 介绍在 Azure Stack Edge Pro GPU 设备上共享 GPU 的方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/01/2021
ms.author: alkohli
ms.openlocfilehash: b1fd1d77a8b2e6f7da3a88bc4d7f57336d18237c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736716"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备上的 GPU 共享

图形处理单元 (GPU) 是旨在加速图形渲染的专用处理器。 GPU 可以同时处理许多数据片段，使这些片段能够用于机器学习、视频编辑和游戏应用程序。 除了用于常规用途计算的 CPU 以外，Azure Stack Edge Pro GPU 设备还可以包含一个或两个 Nvidia Tesla T4 GPU 用于运行计算密集型工作负载，例如硬件加速推理。 有关详细信息，请参阅 [Nvidia 的 Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)。


## <a name="about-gpu-sharing"></a>关于 GPU 共享

许多机器学习工作负载或其他计算工作负载可能不需要专用的 GPU。 GPU 可以共享，在容器化工作负载或 VM 工作负载之间共享 GPU 有助于提高 GPU 利用率，而且不会明显影响 GPU 的性能优势。  

## <a name="using-gpu-with-vms"></a>将 GPU 用于 VM

在 Azure Stack Edge Pro 设备上，部署 VM 工作负载时不能共享 GPU。 一个 GPU 只能映射到一个 VM。 这意味着，在配备一个 GPU 的设备上只能有一个 GPU VM，在配备两个 GPU 的设备上只能有两个 VM。 在为容器化工作负载配置了 Kubernetes 的设备上使用 GPU VM 时，还必须考虑其他因素。 有关详细信息，请参阅 [GPU VM 和 Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)。


## <a name="using-gpu-with-containers"></a>将 GPU 用于容器

如果要部署容器化的工作负载，可以通过多种方式在硬件和软件层共享 GPU。 如果 Azure Stack Edge Pro 设备上配备了 Tesla T4 GPU，则仅限于软件共享。 在设备上，使用以下两种方法进行 GPU 软件共享： 

- 第一种方法涉及到使用环境变量来指定可共享时间的 GPU 数。 使用此方法时请注意以下事项：

    - 可以使用此方法指定一个或两个 GPU，或者不指定任何 GPU。 不能指定一部分使用量。
    - 多个模块可以映射到一个 GPU，但同一个模块不能映射到多个 GPU。
    - 使用 Nvidia SMI 输出可以查看总体 GPU 利用率，包括内存利用率。
    
    有关详细信息，请参阅如何在设备上[部署使用 GPU 的 IoT Edge 模块](azure-stack-edge-gpu-configure-gpu-modules.md)。

- 第二种方法要求在 Nvidia GPU 上启用多进程服务。 MPS 是一个运行时服务，它允许多个使用 CUDA 的进程在单个共享的 GPU 上并发运行。 MPS 允许重叠 GPU 上不同进程的内核和 memcopy 操作，以实现最大利用率。 有关详细信息，请参阅[多进程服务](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)。

    使用此方法时请注意以下事项：
    
    - MPS 允许在 GPU 部署中指定更多标志。
    - 可以通过 MPS 指定一部分使用量，从而限制设备上部署的每个应用程序的使用。 可以通过添加以下参数，在 `deployment.yaml` 的 `env` 部分中指定用于每个应用的 GPU 百分比： 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>GPU 使用率
 
在设备上部署的容器化工作负载上共享 GPU 时，可以使用 Nvidia 系统管理界面 (nvidia-smi)。 Nvidia-smi 是一个命令行实用程序，可帮助你管理和监视 Nvidia GPU 设备。 有关详细信息，请参阅 [Nvidia 系统管理界面](https://developer.nvidia.com/nvidia-system-management-interface)。

若要查看 GPU 使用率，请先连接到设备的 PowerShell 接口。 运行 `Get-HcsNvidiaSmi` 命令并查看 Nvidia SMI 输出。 还可以通过在设备上启用 MPS 并部署多个工作负载来查看 GPU 利用率如何变化。 有关详细信息，请参阅[启用多进程服务](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps)。


## <a name="next-steps"></a>后续步骤

- [Azure Stack Edge Pro 上用于 Kubernetes 部署的 GPU 共享](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md)。
- [Azure Stack Edge Pro 上用于 IoT 部署的 GPU 共享](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md)。
