---
title: 什么是 Azure IoT Edge for Linux on Windows | Microsoft Docs
description: 概述了你可以在 Windows 10 设备上运行 Linux IoT Edge 模块
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 330eaf5c12372347917e9f3a4aeafb6a2088c592
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492568"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>什么是 Azure IoT Edge for Linux on Windows（预览版）

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge for Linux on Windows 允许你在 Windows IoT 部署中将容器化 Linux 工作负荷与 Windows 应用程序一起运行。 依赖 Windows IoT 为其边缘设备提供支持的企业现在可以利用在 Linux 中构建的云原生分析解决方案。

IoT Edge for Linux on Windows 的工作方式是在 Windows 设备上运行 Linux 虚拟机。 Linux 虚拟机预先安装了 IoT Edge 运行时。 部署到该设备的任何 IoT Edge 模块都在虚拟机中运行。 同时，Windows 主机设备上运行的 Windows 应用程序可以与 Linux 虚拟机中运行的模块进行通信。

立即[开始使用](how-to-install-iot-edge-on-windows.md)预览版。

>[!NOTE]
>请考虑参加我们的[产品调查](https://aka.ms/AzEFLOW-Registration)，帮助我们根据你的 IoT Edge 背景和目标来改进 Azure IoT Edge for Linux on Windows。 你还可以使用此调查注册获取将来的 Azure IoT Edge for Linux on Windows 公告。

## <a name="components"></a>组件

IoT Edge for Linux on Windows 通过以下组件使 Linux 和 Windows 工作负荷能够并行运行并无缝通信：

* 运行 Azure IoT Edge 的 Linux 虚拟机：一台通过 IoT Edge 运行时构建的 Linux 虚拟机，该虚拟机基于 Microsoft 的第一方 [Mariner](https://github.com/microsoft/CBL-Mariner) 操作系统，并且经过验证，可用作 IoT Edge 工作负荷的第 1 层受支持环境。

* Windows Admin Center：Windows Admin Center 的一个 IoT Edge 扩展为在 Linux 虚拟机上安装、配置和诊断 IoT Edge 提供了便利。 Windows Admin Center 可以在本地设备上部署 IoT Edge for Linux on Windows，也可以远程连接到目标设备并管理它们。

* Microsoft 更新：与 Microsoft 更新的集成使 Windows 运行时组件、Mariner Linux VM 和 IoT Edge 可以保持最新。

![Windows 和 Linux VM 并行运行，而 Windows Admin Center 则可以控制这两个组件](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows 进程与 Linux 虚拟机之间的双向通信意味着，Windows 进程可以为在 Linux 容器中运行的工作负荷提供用户界面或硬件代理。

## <a name="samples"></a>示例

IoT Edge for Linux on Windows 着重于 Linux 和 Windows 组件之间的互操作性。

如果需要通过示例来演示 Windows 应用程序与 IoT Edge 模块之间的通信，请参阅 [Windows 10 IoT 示例](https://github.com/microsoft/Windows-IoT-Samples)。

## <a name="public-preview"></a>公共预览版

IoT Edge for Linux on Windows 目前为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 安装和管理过程可能不同于正式版功能的相应过程。

## <a name="support"></a>支持

使用 IoT Edge 支持和反馈通道，获取有关 IoT Edge for Linux on Windows 的帮助。

**报告 bug** - 可以在 IoT Edge 开源项目的[问题页](https://github.com/azure/iotedge/issues)上报告 bug。 与 Azure IoT Edge for Linux on Windows 相关的 bug 可以在 [iotedge-eflow 问题页](https://github.com/azure/iotedge-eflow/issues)上报告。

**Microsoft 客户支持团队** – 拥有 [支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)创建支持票证来与 Microsoft 客户支持团队进行沟通。

**功能请求** – Azure IoT Edge 产品通过产品的 [User Voice 页面](https://feedback.azure.com/forums/907045-azure-iot-edge)跟踪功能请求。

## <a name="next-steps"></a>后续步骤

观看 [IoT Edge for Linux on Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) 来了解详细信息和示例的实际运用。

按照[在 Windows 设备上安装并预配 Azure IoT Edge for Linux](how-to-install-iot-edge-on-windows.md) 中的步骤为设备安装 IoT Edge for Linux on Windows。
