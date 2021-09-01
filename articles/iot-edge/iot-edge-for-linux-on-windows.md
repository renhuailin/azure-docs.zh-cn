---
title: 什么是 Azure IoT Edge for Linux on Windows | Microsoft Docs
description: 概述了你可以在 Windows 10 设备上运行 Linux IoT Edge 模块
author: kgremban
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/18/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 6723b203215d650b1fe6718630ae3914c8313256
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751971"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows"></a>什么是 Azure IoT Edge for Linux on Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge for Linux on Windows 允许你在 Windows IoT 部署中将容器化 Linux 工作负荷与 Windows 应用程序一起运行。 依赖 Windows IoT 为其边缘设备提供支持的企业现在可以利用在 Linux 中构建的云原生分析解决方案。

IoT Edge for Linux on Windows 的工作方式是在 Windows 设备上运行 Linux 虚拟机。 Linux 虚拟机预先安装了 IoT Edge 运行时。 部署到该设备的任何 IoT Edge 模块都在虚拟机中运行。 同时，Windows 主机设备上运行的 Windows 应用程序可以与 Linux 虚拟机中运行的模块进行通信。

立即开始使用[](how-to-install-iot-edge-on-windows.md)。

## <a name="components"></a>组件

IoT Edge for Linux on Windows 通过以下组件使 Linux 和 Windows 工作负荷能够并行运行并无缝通信：

* 运行 Azure IoT Edge 的 Linux 虚拟机：基于 Microsoft 第一方 [CBL Mariner](https://github.com/microsoft/CBL-Mariner) 操作系统的 Linux 虚拟机使用 IoT Edge 运行时构建，验证为 IoT Edge 工作负载的第 1 层支持环境。

* Windows Admin Center：Windows Admin Center 的一个 IoT Edge 扩展为在 Linux 虚拟机上安装、配置和诊断 IoT Edge 提供了便利。 Windows Admin Center 可以在本地设备上部署 IoT Edge for Linux on Windows，也可以远程连接到目标设备并管理它们。

* **Microsoft 更新**：与 Microsoft 更新集成，使 Windows 运行时组件、CBL-Mariner Linux VM 和 IoT Edge 保持最新状态。

![Windows 和 Linux VM 并行运行，而 Windows Admin Center 则可以控制这两个组件](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows 进程与 Linux 虚拟机之间的双向通信意味着，Windows 进程可以为在 Linux 容器中运行的工作负荷提供用户界面或硬件代理。

## <a name="samples"></a>示例

IoT Edge for Linux on Windows 着重于 Linux 和 Windows 组件之间的互操作性。

如果需要通过示例来演示 Windows 应用程序与 IoT Edge 模块之间的通信，请参阅 [EFLOW & Windows 10 示例](https://aka.ms/AzEFLOW-Samples)。

## <a name="support"></a>支持

使用 IoT Edge 支持和反馈通道，获取有关 IoT Edge for Linux on Windows 的帮助。

**报告 Bug**：与 Azure IoT Edge for Linux on Windows 相关的 bug 可以在 [iotedge-eflow 问题页](https://aka.ms/AzEFLOW-Issues)上报告。 与 IoT Edge 相关的 bug 可以在 IoT Edge 开源项目的[问题页](https://github.com/azure/iotedge/issues)上报告。

**Microsoft 客户支持团队** – 拥有 [支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)创建支持票证来与 Microsoft 客户支持团队进行沟通。

**功能请求** – Azure IoT Edge 产品通过产品的 [User Voice 页面](https://feedback.azure.com/forums/907045-azure-iot-edge)跟踪功能请求。

## <a name="next-steps"></a>后续步骤

观看 [IoT Edge for Linux on Windows 10 IoT Enterprise](https://aka.ms/azeflow-show) 来了解详细信息和示例的实际运用。

按照[在 Windows 设备上安装并预配 Azure IoT Edge for Linux](how-to-install-iot-edge-on-windows.md) 中的步骤为设备安装 IoT Edge for Linux on Windows。
