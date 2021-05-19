---
title: 了解面向 Azure IoT 设备开发人员的连接选项
description: 了解面向 Azure IoT 设备开发人员的常用设备连接选项和工具。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654066"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>概述：面向 Azure IoT 设备开发人员的连接选项
身为使用设备的开发人员，你有多个选项来连接和管理 Azure IoT 设备。 本文概述了可帮助你连接和管理设备的最常用选项和工具。

评估可用于设备的 Azure IoT 连接选项时，比较下列各项将很有帮助：
- Azure IoT 设备应用程序平台
- 用于连接和管理设备的工具

## <a name="application-platforms-iot-central-and-iot-hub"></a>应用程序平台： IoT Central 和 IoT 中心
Azure IoT 包含两项服务 - Azure IoT Central 和 Azure IoT 中心，它们是设备启用的云应用程序的平台。 可使用任一一个来托管 IoT 应用程序并连接设备。
- [IoT Central](../iot-central/core/overview-iot-central.md) 旨在降低使用 IoT 解决方案时的复杂度和成本。 它是一种软件即服务 (SaaS) 应用程序，提供完整的平台来托管 IoT 应用程序。 可使用 IoT Central Web UI 来简化创建和管理 IoT 应用程序的整个生命周期。 通过 Web UI，可更轻松地创建应用程序并从几台到数百万台设备中进行连接和管理。 IoT Central 使用 IoT 中心来创建和管理应用程序，但使详细信息对用户来说公开透明。 通常，IoT Central 会减少复杂性和开发工作量，并通过 Web UI 简化设备管理。
- [IoT 中心](../iot-hub/about-iot-hub.md)充当集中消息中心，用于 IoT 应用程序与连接设备之间的双向通信。 它是一种平台即服务 (PaaS) 应用程序，还提供了平台来托管 IoT 应用程序。 与 IoT Central 一样，它可进行缩放来支持数百万台设备。 通常借助 IoT 中心，可对应用程序设计进行更大程度地控制和自定义，还有更多开发人员工具选项用来使用服务，代价是开发和管理复杂度有所增加。

## <a name="tools-to-connect-and-manage-devices"></a>用于连接和管理设备的工具
选择 IoT 中心或 IoT Central 来托管 IoT 应用程序后，有多个开发人员工具选项可供使用。 可使用这些工具连接到你选择的 IoT 应用程序平台，还可用它们来创建和管理应用程序与设备。 下表汇总了常见的工具选项。 

> [!NOTE]
> 除了下列工具，你还可使用 REST API、Azure SDK 或 Azure 资源管理器模板以编程方式创建和管理 IoT 应用程序。 若要了解详细信息，可查看 [IoT 中心](../iot-hub/about-iot-hub.md)和 [IoT Central](../iot-central/core/overview-iot-central.md) 服务文档。

|工具  |支持 IoT 平台 &nbsp; &nbsp; &nbsp; &nbsp; |文档  |说明  |
|---------|---------|---------|---------|
|Central Web UI     | Central | [Central 快速入门](../iot-central/core/quick-deploy-iot-central.md) | 基于浏览器的 IoT Central 门户。 |
|Azure 门户     | 中心、Central      | [使用 Azure 门户创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)，[从 Azure 门户管理 IoT Central](../iot-central/core/howto-manage-iot-central-from-portal.md)| 适合 IoT 中心和设备的基于浏览器的门户。 还适用于其他 Azure 资源，包括 IoT Central。 |
|Azure CLI     | 中心、Central          | [使用 CLI 创建 IoT 中心](../iot-hub/iot-hub-create-using-cli.md)，[从 Azure CLI 管理 IoT Central](../iot-central/core/howto-manage-iot-central-from-cli.md) | 用于创建和管理 IoT 应用程序的命令行接口。 |
|Azure PowerShell     | 中心、Central   | [使用 PowerShell 创建 IoT 中心](../iot-hub/iot-hub-create-using-powershell.md)，[从 Azure PowerShell 管理 IoT Central](../iot-central/core/howto-manage-iot-central-from-powershell.md) | 用于创建和管理 IoT 应用程序的 PowerShell 接口 |
|适用于 VS Code 的 Azure IoT Tools  | 集线器 | [使用 Tools for VS Code 创建 IoT 中心](../iot-hub/iot-hub-create-use-iot-toolkit.md) | 适合 IoT 中心应用程序的 VS Code 扩展。 |
|Azure IoT 资源管理器     | 集线器 | [Azure IoT 资源管理器](https://github.com/Azure/azure-iot-explorer) | 无法创建 IoT 中心。 连接到现有 IoT 中心来管理设备。 通常与 CLI 或门户一起使用。|

## <a name="next-steps"></a>后续步骤
若要详细了解可用于将设备连接到 Azure IoT 的选项，请浏览以下快速入门：
- IoT Central：[创建 Azure IoT Central 应用程序](../iot-central/core/quick-deploy-iot-central.md)
- IoT 中心：[将遥测数据从设备发送到 IoT 中心并使用 Azure CLI 监视该数据](../iot-hub/quickstart-send-telemetry-cli.md)