---
title: IoT 即插即用 bridge |Microsoft Docs
description: 了解 IoT 即插即用 bridge 以及如何使用它将连接到 Windows 或 Linux 网关的现有设备连接为 IoT 即插即用设备。
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 05b9df2c5083d24ae6a78212bdd29cd1c740e1d1
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746486"
---
# <a name="iot-plug-and-play-bridge"></a>IoT 即插即用桥接

IoT 即插即用桥是一个开源应用程序，用于将连接到 Windows 或 Linux 网关的现有设备连接为 IoT 即插即用设备。 在 Windows 或 Linux 计算机上安装和配置应用程序后，可以使用它将连接的设备连接到 IoT 中心。 你可以使用桥将 IoT 即插即用接口映射到附加设备发送的遥测、使用设备属性以及调用命令。

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="左侧有几个现有的传感器 (有线和无线) 连接到包含 IoT 即插即用 bridge 的 Windows 或 Linux 计算机。然后，IoT 即插即用桥连接到右侧的 IoT 中心":::

IoT 即插即用 bridge 可以作为独立的可执行文件部署在运行 Windows 10 或 Linux 的任何 IoT 设备、工业 PC、服务器或网关上。 它还可以编译到应用程序代码中。 简单的配置 JSON 文件告诉 IoT 即插即用桥接哪些连接的设备/外设应在 Azure 中公开。

## <a name="supported-protocols-and-sensors"></a>支持的协议和传感器

默认情况下，IoT 即插即用桥支持以下类型的外围设备，并提供适配器文档的链接：

|外设|Windows|Linux|
|---------|---------|---------|
|[蓝牙传感器适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) 连接检测到 (BLE) 启用传感器的蓝牙低能耗。       |是|否|
|[照相机适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) 将相机连接到 Windows 10 设备上。               |是|否|
|[Modbus 适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) 在 Modbus 设备上连接传感器。              |是|是|
|[MQTT 适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) 连接使用 MQTT broker 的设备。                  |是|是|
|[SerialPnP 适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) 连接通过串行连接进行通信的设备。               |是|是|
|[WINDOWS USB 外设](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) 使用适配器支持的设备接口类的列表来连接具有特定硬件 ID 的设备。  |是|不适用|

若要了解如何扩展 IoT 即插即用 bridge 以支持其他设备协议，请参阅 [扩展 iot 即插即用 bridge](howto-author-pnp-bridge-adapter.md)。 若要了解如何生成和部署 IoT 即插即用 bridge，请参阅 [生成和部署 iot 即插即用 bridge](howto-build-deploy-extend-pnp-bridge.md)。

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT 即插即用 bridge 体系结构

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="左侧有几个框，它们表示连接到包含 IoT 即插即用 bridge 的 Windows 或 Linux PC 的各种外设。顶部有一个标记为 &quot;配置点&quot; 的框。然后，桥将连接到关系图右侧的 IoT 中心。":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT 即插即用桥适配器

IoT 即插即用桥支持一组 IoT 即插即用桥适配器，适用于各种类型的设备。 *适配器清单* 将适配器静态定义为桥。

桥接适配器管理器使用清单来标识和调用适配器函数。 适配器管理器仅对配置文件中列出的接口组件所需的桥接适配器调用 create 函数。 为每个 IoT 即插即用组件创建适配器实例。

桥接适配器创建并获取数字克隆接口句柄。 适配器使用此句柄将设备功能绑定到数字克隆。

通过使用配置文件中的信息，桥接器使用以下技术实现完全设备到通过桥的数字克隆通信：

- 直接建立信道。
- 创建设备观察程序以等待通信通道变为可用。

### <a name="configuration-file"></a>配置文件

IoT 即插即用 bridge 使用基于 JSON 的配置文件，该文件指定：

- 如何连接到 IoT 中心或 IoT Central 应用程序：选项包括连接字符串、身份验证参数或 (DPS) 的设备预配服务。
- 桥使用的 IoT 即插即用功能模型的位置。 模型定义 IoT 即插即用设备的功能，并且是静态的和不可变的。
- IoT 即插即用接口组件列表，以及每个组件的以下信息：
- 接口 ID 和组件名称。
- 与组件进行交互所需的桥适配器。
- 桥适配器建立与设备的通信所需的设备信息。 例如，硬件 ID 或适配器、接口或协议的特定信息。
- 可选的桥适配器子类型或接口配置（如果适配器支持多个具有相似设备的通信类型）。 该示例演示如何配置蓝牙传感器组件：

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- 全局桥适配器参数的可选列表。 例如，蓝牙传感器桥接适配器具有受支持配置的字典。 需要蓝牙传感器适配器的接口组件可以选择其中一种配置 `blesensor_identity` ：

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>下载 IoT 即插即用 bridge

你可以在 [IoT 即插即用 bridge 版本](https://github.com/Azure/iot-plug-and-play-bridge/releases) 中下载具有支持的适配器的网桥预构建版本，并展开最新版本的资产列表。 下载适用于你的操作系统的最新版本的应用程序。

你还可以 [在 GitHub 上下载并查看 IoT 即插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge)的源代码。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 IoT 即插即用 bridge 的体系结构，接下来的步骤是详细了解以下内容：

- [如何将在 Linux 或 Windows 上运行的 IoT 即插即用 bridge 示例连接到 IoT 中心](./howto-use-iot-pnp-bridge.md)
- [构建和部署 IoT 即插即用 bridge](howto-build-deploy-extend-pnp-bridge.md)
- [扩展 IoT 即插即用 bridge](howto-build-deploy-extend-pnp-bridge.md)
- [GitHub 上的 IoT 即插即用桥](https://github.com/Azure/iot-plug-and-play-bridge)
