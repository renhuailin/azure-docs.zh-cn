---
title: 了解 Azure IoT 设备开发人员的连接选项
description: 了解适用于 Azure IoT 设备开发人员的常用设备连接选项和工具。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654066"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>概述：适用于 Azure IoT 设备开发人员的连接选项
作为使用设备的开发人员，你可以使用多个选项来连接和管理 Azure IoT 设备。 本文概述了可帮助您连接和管理设备的最常用选项和工具。

评估适用于你的设备的 Azure IoT 连接选项时，比较以下各项会很有帮助：
- Azure IoT 设备应用程序平台
- 用于连接和管理设备的工具

## <a name="application-platforms-iot-central-and-iot-hub"></a>应用程序平台： IoT Central 和 IoT 中心
Azure IoT 包含两项服务，它们是启用设备的云应用程序的平台： Azure IoT Central 和 Azure IoT 中心。 你可以使用其中一个来托管 IoT 应用程序并连接设备。
- [IoT Central](../iot-central/core/overview-iot-central.md) 旨在降低使用 IoT 解决方案的复杂性和成本。 它是一个软件即服务 (SaaS) 应用程序，它提供了用于托管 IoT 应用程序的完整平台。 你可以使用 IoT Central web UI 来简化创建和管理 IoT 应用程序的整个生命周期。 Web UI 简化了创建应用程序的任务，并从多达数百万台设备进行连接和管理。 IoT Central 使用 IoT 中心来创建和管理应用程序，但会使详细信息对用户透明。 通常，IoT Central 通过 web UI 简化了复杂性和开发工作，并简化了设备管理。
- [Iot 中心](../iot-hub/about-iot-hub.md) 充当用于 IoT 应用程序与连接的设备之间的双向通信的中心消息中心。 它是平台即服务 (PaaS) 应用程序，它还提供了用于托管 IoT 应用程序的平台。 与 IoT Central 一样，它可以进行缩放以支持数百万台设备。 通常，IoT 中心提供对应用程序设计的更好的控制和自定义，并且更多开发人员工具选项可用于服务，同时增加开发和管理复杂性。

## <a name="tools-to-connect-and-manage-devices"></a>用于连接和管理设备的工具
选择 IoT 中心或 IoT Central 来托管 IoT 应用程序后，可以使用多个开发人员工具选项。 你可以使用这些工具连接到所选 IoT 应用程序平台，并创建和管理应用程序和设备。 下表汇总了常用工具选项。 

> [!NOTE]
> 除了以下工具外，还可以使用 REST API 的、Azure Sdk 或 Azure 资源管理器模板以编程方式创建和管理 IoT 应用程序。 可在 [IoT 中心](../iot-hub/about-iot-hub.md) 和 [IoT Central](../iot-central/core/overview-iot-central.md) 服务文档中了解详细信息。

|工具  |支持 IoT 平台 &nbsp; &nbsp; &nbsp;&nbsp; |文档  |说明  |
|---------|---------|---------|---------|
|中央 web UI     | Central | [中心快速入门](../iot-central/core/quick-deploy-iot-central.md) | 用于 IoT Central 的基于浏览器的门户。 |
|Azure 门户     | 中心，中部      | [使用 Azure 门户创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)，并 [从 Azure 门户管理 IoT Central](../iot-central/core/howto-manage-iot-central-from-portal.md)| 用于 IoT 中心和设备的基于浏览器的门户。 还适用于其他 Azure 资源，包括 IoT Central。 |
|Azure CLI     | 中心，中部          | [使用 CLI 创建 IoT 中心](../iot-hub/iot-hub-create-using-cli.md)， [从 Azure CLI 管理 IoT Central](../iot-central/core/howto-manage-iot-central-from-cli.md) | 用于创建和管理 IoT 应用程序的命令行接口。 |
|Azure PowerShell     | 中心，中部   | [使用 PowerShell 创建 IoT 中心](../iot-hub/iot-hub-create-using-powershell.md)， [从 Azure PowerShell 管理 IoT Central](../iot-central/core/howto-manage-iot-central-from-powershell.md) | 用于创建和管理 IoT 应用程序的 PowerShell 接口 |
|适用于 VS Code 的 Azure IoT Tools  | 集线器 | [使用用于 VS Code 的工具创建 IoT 中心](../iot-hub/iot-hub-create-use-iot-toolkit.md) | IoT 中心应用程序的 VS Code 扩展。 |
|Azure IoT 资源管理器     | 集线器 | [Azure IoT 资源管理器](https://github.com/Azure/azure-iot-explorer) | 无法创建 IoT 中心。 连接到现有的 IoT 中心以管理设备。 通常与 CLI 或门户一起使用。|

## <a name="next-steps"></a>后续步骤
若要了解有关将设备连接到 Azure IoT 的选项的详细信息，请浏览以下快速入门：
- IoT Central： [创建 Azure IoT Central 应用程序](../iot-central/core/quick-deploy-iot-central.md)
- IoT 中心： [将遥测数据从设备发送到 IoT 中心，并使用 Azure CLI 进行监视](../iot-hub/quickstart-send-telemetry-cli.md)