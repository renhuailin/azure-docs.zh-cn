---
title: 使用适用于 Azure CLI 的 IoT 扩展进行 Azure IoT 设备管理 | Microsoft Docs
description: 使用适用于 Azure CLI 的 IoT 扩展工具进行 Azure IoT 中心设备管理，特点是使用直接方法并提供孪生所需的属性管理选项。
author: robinsh
manager: ''
keywords: azure iot 设备管理, azure iot 中心设备管理, 设备管理 iot, iot 中心设备管理
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: robinsh
ms.openlocfilehash: c67e9fc7cb6a2530c752efee760b2fb7fcb7f277
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727093"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>针对 Azure IoT 中心设备管理，使用适用于 Azure CLI 的 IoT 扩展

![端到端关系图](media/iot-hub-get-started-e2e-diagram/2.png)

在本文介绍如何在开发计算机上通过各种管理选项使用适用于 Azure CLI 的 IoT 扩展。 [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)是一个开放源代码 IoT 扩展，它增强了 [Azure CLI](/cli/azure/overview) 的功能。 Azure CLI 包含用于与 Azure 资源管理器和管理终结点进行交互的命令。 例如，可使用 Azure CLI 创建 Azure VM 或 IoT 中心。 CLI 扩展使 Azure 服务能够扩展 Azure CLI，从而可访问其他特定于服务的功能。 IoT 扩展为 IoT 开发人员提供了针对所有 IoT 中心、IoT Edge 和 IoT 中心设备预配服务功能的命令行访问权限。

| 管理选项          | 任务  |
|----------------------------|-----------|
| 直接方法             | 让设备执行操作，如开始或停止发送消息或重新启动设备。                                        |
| 孪生所需属性    | 让设备进入特定状态，例如将 LED 设置为绿色，或将遥测发送间隔设置为 30 分钟。         |
| 孪生报告属性   | 获取报告的设备状态。 例如，设备报告 LED 现在正在闪烁。                                    |
| 孪生标记                  | 将设备特定的元数据存储在云中。 例如，存储在自动售货机的部署位置。                         |
| 设备孪生查询        | 查询所有设备孪生以检索符合任意条件的设备孪生，例如确定可供使用的设备。 |

有关这些选项的差异和使用指导的更详细说明，请参阅[设备到云通信指南](iot-hub-devguide-d2c-guidance.md)和[云到设备通信指南](iot-hub-devguide-c2d-guidance.md)。

设备孪生是存储设备状态信息（元数据、配置和条件）的 JSON 文档。 IoT 中心为连接到它的每台设备保留一个设备孪生。 有关设备孪生的详细信息，请参阅[设备孪生入门](iot-hub-node-node-twin-getstarted.md)。

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>先决条件

* 请完成 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程。 例如，可转到[将 Raspberry Pi 与 Node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)教程或[发送遥测](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)快速入门中的一个教程。 这些文章阐述下列要求：

  * 一个有效的 Azure 订阅。
  * 已在订阅中创建一个 Azure IoT 中心。
  * 一个可向 Azure IoT 中心发送消息的客户端应用程序。

* 在学习本教程期间，确保设备与客户端应用程序均处于运行状态。

* [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)

* Azure CLI。 如需进行安装，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.70 或更高版本。 请使用 `az –version` 验证版本。

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* 安装 IoT 扩展。 最简单的方法是运行 `az extension add --name azure-iot`。 [IoT 扩展自述文件](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md)介绍了该扩展的多种安装方法。

## <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

通过运行以下命令登录到 Azure 帐户：

```azurecli
az login
```

## <a name="direct-methods"></a>直接方法

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>设备孪生所需属性

通过运行以下命令将所需属性间隔设置为 3000：

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval=3000
```

可从设备读取此属性。

## <a name="device-twin-reported-properties"></a>设备孪生报告属性

通过运行以下命令获取报告的设备属性：

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

其中一个孪生报告属性为 $metadata.$lastUpdated，它显示设备应用上次更新其报告属性集的时间。

## <a name="device-twin-tags"></a>设备孪生标记

通过运行以下命令显示设备的标记和属性：

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

通过运行以下命令向设备添加字段角色 = 温度和湿度：

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags='{"role":"temperature&humidity"}'
```

## <a name="device-twin-queries"></a>设备孪生查询

通过运行以下命令查询角色标记 =“温度和湿度”的设备：

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

通过运行以下命令查询除角色标记 =“温度和湿度”的设备以外的所有设备：

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>后续步骤

现在，已了解如何监视设备到云的消息，以及在 IoT 设备与 Azure IoT 中心之间发送云到设备的消息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]