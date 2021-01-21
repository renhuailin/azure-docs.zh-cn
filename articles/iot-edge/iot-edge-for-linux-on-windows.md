---
title: Windows 上适用于 Linux 的 Azure IoT Edge |Microsoft Docs
description: 概述你可以在 Windows 10 设备上运行 Linux IoT Edge 模块
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: ebe0ac1151f3a1f43072f2832e2f433182ccc82d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633841"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Windows (预览版中适用于 Linux 的 Azure IoT Edge) 

Windows 上的适用于 Linux 的 Azure IoT Edge 允许在 Windows IoT 部署中运行容器化的 Linux 工作负荷和 Windows 应用程序。 如果企业依赖 Windows IoT 为其边缘设备供电，现在可以利用在 Linux 中构建的云本机分析解决方案。

Windows 上的 Linux IoT Edge 适用于在 Windows 设备上运行 Linux 虚拟机。 Linux 虚拟机随 IoT Edge 运行时预装。 部署到设备的任何 IoT Edge 模块都在虚拟机中运行。 同时，Windows 主机设备上运行的 Windows 应用程序可以与 Linux 虚拟机中运行的模块进行通信。

>[!NOTE]
>请考虑参加我们的 [产品调查](https://aka.ms/AzEFLOW-Registration) ，帮助我们根据你的 IoT Edge 背景和目标，帮助我们改进针对 Windows 的 Linux Azure IoT Edge。 你还可以使用此调查在 Windows 公告上注册适用于 Linux 的未来 Azure IoT Edge。

## <a name="components"></a>组件

Windows 上适用于 Linux 的 IoT Edge 使用以下组件来使 Linux 和 Windows 工作负荷相互运行并无缝通信：

* **运行 Azure IoT Edge 的 linux 虚拟机**： linux 虚拟机（基于 Microsoft 的第一方 [Mariner](https://github.com/microsoft/CBL-Mariner) 操作系统）使用 IoT Edge 运行时进行生成，并验证为 IoT Edge 工作负载的第1层支持环境。

* **Windows 管理中心**： windows 管理中心的 IoT Edge 扩展有助于在 Linux 虚拟机上安装、配置和诊断 IoT Edge。 Windows 管理中心可以在本地设备上的 Windows 上部署适用于 Linux 的 IoT Edge，或者可以连接到目标设备并远程管理它们。

* **Microsoft 更新**：与 Microsoft 更新的集成使 Windows 运行时组件、MARINER Linux VM 和 IoT Edge 保持最新。

![Windows 和 Linux VM 并行运行，而 Windows 管理中心则控制这两个组件](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows 进程和 Linux 虚拟机之间的双向通信意味着，Windows 进程可以为在 Linux 容器中运行的工作负荷提供用户界面或硬件代理。

## <a name="samples"></a>示例

Windows 上的 IoT Edge 适用于 linux 和 Windows 组件之间的互操作性。

有关演示 Windows 应用程序和 IoT Edge 模块之间的通信的示例，请参阅 [windows 10 IoT 示例](https://github.com/microsoft/Windows-IoT-Samples)。

## <a name="public-preview"></a>公共预览版

Windows 上的 IoT Edge 适用于 Windows 的 [公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 安装和管理过程可能不同于一般的可用功能。

目前，Windows 上适用于 Linux 的 IoT Edge 使用 windows 预览版本的 Windows 管理中心。 有关 Windows 预览体验计划并注册的详细信息，请参阅 [Windows 预览体验计划是什么？](https://insider.windows.com/about-windows-insider-program)。

## <a name="support"></a>支持

使用 IoT Edge 支持和反馈通道，获取适用于 Windows 上的 Linux IoT Edge 的帮助。

**报告 bug** –可在 IoT Edge 开源项目的 " [问题" 页](https://github.com/azure/iotedge/issues) 上报告 bug。 

**Microsoft 客户支持团队** – 拥有 [支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)创建支持票证来与 Microsoft 客户支持团队进行沟通。

**功能请求** – Azure IoT Edge 产品通过产品的 [User Voice 页面](https://feedback.azure.com/forums/907045-azure-iot-edge)跟踪功能请求。

## <a name="next-steps"></a>后续步骤

有关详细信息和操作示例，请观看 [适用于 Windows 10 IoT 企业版 Linux 的 IoT Edge](https://aka.ms/EFLOWPPC9) 。

按照在 [windows 设备上安装和设置适用于 linux 的 Azure IoT Edge](how-to-install-iot-edge-on-windows.md) 中的步骤，使用适用于 Windows 的 linux IoT Edge 设置设备。
