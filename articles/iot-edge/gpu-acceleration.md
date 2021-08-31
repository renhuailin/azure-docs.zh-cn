---
title: 用于 Azure IoT Edge for Linux on Windows 的 GPU 加速 | Microsoft Docs
description: 了解如何配置 Azure IoT Edge for Linux on Windows 虚拟机，以使用主机设备 GPU。
author: v-tcassi
manager: kgremban
ms.author: v-tcassi
ms.date: 06/22/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2aa4dcc0aa4ecdd3b620aef874a0b5b9ceef4c96
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112963539"
---
# <a name="gpu-acceleration-for-azure-iot-edge-for-linux-on-windows-preview"></a>用于 Azure IoT Edge for Linux on Windows 的 GPU 加速（预览）

GPU 是适用于人工智能计算的常用选项，因为它们提供并行处理功能，并且通常可以执行基于视觉的推断，速度比 CPU 更快。 为了更好地支持人工智能和机器学习应用程序，Azure IoT Edge for Linux on Windows 可以向虚拟机的 Linux 模块公开一个 GPU。

> [!NOTE]
> 下面详细介绍的 GPU 加速功能处于预览阶段，可能会有所更改。

Azure IoT Edge for Linux on Windows 支持几种 GPU 传递技术，其中包括：

* 直接设备分配 (DDA) - 将 GPU 核心分配给 Linux 虚拟机或主机。

* GPU 半虚拟化 (GPU-PV) - 在 Linux 虚拟机和主机之间共享 GPU。

Azure IoT Edge for Linux on Windows 部署将自动选择适当的传递方法，以匹配设备 GPU 硬件的支持功能。

> [!IMPORTANT]
> 这些功能可能包括由 NVIDIA 公司或其授权方开发和拥有的组件。 组件的使用受 [NVIDIA 网站](https://www.nvidia.com/content/DriverDownload-March2009/licence.php?lang=us)上的 NVIDIA 最终用户许可协议的约束。
>
> 使用 GPU 加速功能，即表示你接受并同意 NVIDIA 最终用户许可协议的条款。

## <a name="prerequisites"></a>先决条件

Azure IoT Edge for Linux on Windows 的 GPU 加速功能目前支持一组选定的 GPU 硬件。 此外，根据配置，使用此功能可能需要最新的 Windows 预览体验成员开发频道版本。

下面列出了支持的 GPU 和所需的 Windows 版本：

* NVIDIA T4（支持 DDA）

  * Windows Server 版本 17763 或更高版本
  * Windows 企业版或专业版，版本 21318 或更高版本（Windows 预览体验成员版本）

* NVIDIA GeForce/Quadro（支持 GPU-PV）

  * Windows 企业版或专业版，版本 20145 或更高版本（Windows 预览体验成员版本）

### <a name="windows-insider-builds"></a>Windows 预览体验成员版本

对于 Windows 企业版或专业版用户，将需要[注册 Windows 预览体验成员计划](https://insider.windows.com/getting-started#register)。

注册后，请按照“2. 发布外部测试版”选项卡上的说明进行操作，获取对相应 Windows 预览体验成员版本的访问权限。 在选择要使用的频道时，请选择[开发频道](/windows-insider/flight-hub/#active-development-builds-of-windows-10)。 安装完成后，可以通过命令提示符运行 `winver` 来验证内部版本号。

### <a name="t4-gpus"></a>T4 GPU

对于 T4 GPU，Microsoft 建议从 GPU 的供应商那里安装设备缓解驱动程序。 尽管安装缓解驱动程序是可选的操作，但安装后可以提高部署的安全性。 有关详细信息，请参阅[使用直接设备分配来部署图形设备](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda#optional---install-the-partitioning-driver)。

> [!WARNING]
> 启用硬件设备传递可能会增加安全风险。 建议从 GPU 的供应商那里安装设备缓解驱动程序。

### <a name="geforcequadro-gpus"></a>GeForce/Quadro GPU

对于 GeForce/Quadro GPU，请下载并安装[适用于 Linux 的 Windows 子系统 (WSL) 的支持 NVIDIA CUDA 的驱动程序](https://developer.nvidia.com/cuda/wsl)，以便与现有 CUDA ML 工作流一起使用。 WSL CUDA 驱动程序最初是为 WSL 开发的，它也可以用于 Azure IoT Edge for Linux on Windows。

## <a name="using-gpu-acceleration-for-your-linux-on-windows-deployment"></a>为 Linux on Windows 部署使用 GPU 加速

现在，你已准备好通过 Azure IoT Edge for Linux on Windows 在 Windows 环境中部署和运行 GPU 加速的 Linux 模块。 有关部署过程的详细信息，请参阅[安装 Azure IoT Edge for Linux on Windows](how-to-install-iot-edge-on-windows.md)。

## <a name="next-steps"></a>后续步骤

* [创建 Azure IoT Edge for Linux on Windows 部署](how-to-install-iot-edge-on-windows.md)

* 尝试使用[采用 Edge 上的视觉的支持 GPU 的示例](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/blob/master/factory-ai-vision/Tutorial/Eflow.md)，这是一个解决方案模板，说明了如何生成你自己的基于视觉的机器学习应用程序。

* 有关 GPU 传递技术的详细信息，请参阅 [DDA 文档](/windows-server/virtualization/hyper-v/plan/plan-for-gpu-acceleration-in-windows-server#discrete-device-assignment-dda)和 [GPU-PV 博客文章](https://devblogs.microsoft.com/directx/directx-heart-linux/#gpu-virtualization)。
