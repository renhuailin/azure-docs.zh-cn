---
title: 使用网关转换 modbus 协议 - Azure IoT Edge | Microsoft Docs
description: 通过创建 IoT Edge 网关设备，允许设备使用 Modbus TCP 与 Azure IoT 中心通信
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 5b1c1884dd9b6a37219fece848ebab9e9369906b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731293"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>通过 IoT Edge 设备网关连接 Modbus TCP 设备

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

若要将使用 Modbus TCP 或 RTU 协议的 IoT 设备连接到 Azure IoT 中心，可以使用 IoT Edge 设备作为网关。 此网关设备从 Modbus 设备读取数据，然后使用支持的协议将该数据传送到云。

![Modbus 设备通过 IoT Edge 网关连接到 IoT 中心](./media/deploy-modbus-gateway/diagram.png)

本文介绍如何针对 Modbus 模块创建自己的容器映像（也可使用预构建的示例），然后将其部署到会充当网关的 IoT Edge 设备。

本文假定你使用的是 Modbus TCP 协议。 若要详细了解如何配置支持 Modbus RTU 的模块，请参阅 GitHub 上的 [Azure IoT Edge Modbus 模块](https://github.com/Azure/iot-edge-modbus)项目。

## <a name="prerequisites"></a>先决条件

* Azure IoT Edge 设备。 若要详细了解如何设置一个，请参阅[在 Windows 中部署 Azure IoT Edge](quickstart.md) 或[在 Linux 中部署 Azure IoT Edge](quickstart-linux.md)。
* IoT Edge 设备的主键连接字符串。
* 支持 Modbus TCP 的物理或模拟 Modbus 设备。 需要知道其 IPv4 地址。

## <a name="prepare-a-modbus-container"></a>准备 Modbus 容器

若要测试 Modbus 网关功能，可以使用 Microsoft 提供的示例模块。 可以通过 Azure 市场 [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview) 或映像 URI `mcr.microsoft.com/azureiotedge/modbus:1.0` 访问该模块。

如果需要创建自己的模块并根据环境对其自定义，可以使用 GitHub 上的开源 [Azure IoT Edge Modbus 模块](https://github.com/Azure/iot-edge-modbus)项目。 按照该项目中的指南创建自己的容器映像。 若要创建容器映像，请参阅[在 Visual Studio 中开发 C# 模块](./how-to-visual-studio-develop-module.md)或[在 Visual Studio Code 中开发模块](how-to-vs-code-develop-module.md)。 这些文章说明了如何创建新模块并将容器映像发布到注册表。

## <a name="try-the-solution"></a>试用此解决方案

此部分详述如何将 Microsoft 的示例 Modbus 模块部署到 IoT Edge 设备。

1. 在 [Azure 门户](https://portal.azure.com/)中转到 IoT 中心。

2. 转到“IoT Edge”，然后单击 IoT Edge 设备。

3. 选择“设置模块”。

4. 在“IoT Edge 模块”部分中，添加 Modbus 模块：

   1. 单击“添加”下拉列表，然后选择“市场模块”。
   2. 搜索 `Modbus`，并选择 Microsoft 的“Modbus TCP 模块”。
   3. IoT 中心的模块会自动进行配置，并显示在 IoT Edge 模块列表中。 路由也会自动进行配置。 选择“查看 + 创建”。
   4. 查看部署清单并选择“创建”。

5. 在列表中选择 Modbus 模块 `ModbusTCPModule`，然后选择“模块孪生设置”选项卡。模块孪生所需属性的必需 JSON 会自动填充。

6. 查找 JSON 中的 SlaveConnection 属性，并将其值设置为 Modbus 设备的 IPv4 地址。

7. 选择“更新”。

8. 选择“查看 + 创建”，检查部署，然后选择“创建”。

9. 返回到“设备详细信息”页，并选择“刷新”。 此时会看到新的 `ModbusTCPModule` 模块与 IoT Edge 运行时一起运行。

## <a name="view-data"></a>查看数据

通过 Modbus 模块查看传入的数据：

```cmd/sh
iotedge logs modbus
```

也可使用[适用于 Visual Studio Code 的 Azure IoT 中心扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)（以前称为 Azure IoT 工具包扩展）查看设备正发送的遥测数据。

## <a name="next-steps"></a>后续步骤

* 若要详细了解 IoT Edge 设备如何充当网关，请参阅[创建充当透明网关的 IoT Edge 设备](./how-to-create-transparent-gateway.md)。
* 有关 IoT Edge 模块工作原理的详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)。