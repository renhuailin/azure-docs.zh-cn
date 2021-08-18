---
title: IoT 即插即用网桥 | Microsoft Docs
description: 了解 IoT 即插即用网桥，以及如何使用它将附加到 Windows 或 Linux 网关的现有设备作为 IoT 即插即用设备进行连接。
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.custom: mvc
ms.openlocfilehash: 975b663be048d4438b14fce29e1a055bbd979643
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406325"
---
# <a name="iot-plug-and-play-bridge"></a>IoT 即插即用桥接

IoT 即插即用网桥是一种开放源代码应用程序，用于将附加到 Windows 或 Linux 网关的现有设备作为 IoT 即插即用设备进行连接。 在 Windows 或 Linux 虚拟机上安装和配置此应用程序后，可以使用它将附加的设备连接到 IoT 中心。 使用此网桥，可以将 IoT 即插即用接口映射到附加的设备正在发送的遥测，并能处理设备属性和调用命令。

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="左侧有几个现有的传感器（通过有线和无线方式）连接到包含 IoT 即插即用网桥的 Windows 或 Linux PC。然后，IoT 即插即用网桥连接到右侧的 IoT 中心":::

IoT 即插即用网桥可以作为独立的可执行文件部署在任何运行 Windows 10 或 Linux 的 IoT 设备、工业电脑、服务器或网关上。 也可以将其编译成应用程序代码。 简单的配置 JSON 文件指示 IoT 即插即用网桥应向 Azure 最多公开哪些附加的设备/外设。

## <a name="supported-protocols-and-sensors"></a>支持的协议和传感器

IoT 即插即用网桥默认支持以下外设类型（包含指向适配器文档的链接）：

|外设|Windows|Linux|
|---------|---------|---------|
|[蓝牙传感器适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md)：连接检测到的已启用蓝牙低功耗 (BLE) 的传感器。       |是|否|
|[摄像头适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md)：连接 Windows 10 设备上的摄像头。               |是|否|
|[Modbus 适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md)：连接 Modbus 设备上的传感器。              |是|是|
|[MQTT 适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md)：连接使用 MQTT 代理的设备。                  |是|是|
|[SerialPnP 适配器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md)：连接通过串行连接进行通信的设备。               |是|是|
|[Windows USB 外设](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md)：使用适配器支持的设备接口类列表来连接具有特定硬件 ID 的设备。  |是|不适用|

若要了解如何扩展 IoT 即插即用网桥来支持额外设备协议，请参阅[扩展 IoT 即插即用网桥](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/author_adapter.md)。 若要了解如何生成和部署 IoT 即插即用网桥，请参阅[生成和部署 IoT 即插即用网桥](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/build_deploy.md)。

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT 即插即用网桥体系结构

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="左侧有几个方框，它们指示连接到包含 IoT 即插即用网桥的 Windows 或 Linux PC 的各种外设。顶部有一个标有配置点的方框指向网桥。然后该网桥连接到关系图右侧的 IoT 中心。":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT 即插即用网桥适配器

IoT 即插即用网桥支持一组 IoT 即插即用网桥适配器，以用于各种类型的设备。 适配器清单静态定义了网桥的适配器。

网桥适配器管理器使用此清单来标识和调用适配器函数。 适配器管理器只对配置文件中列出的接口组件所需的网桥适配器调用创建函数。 为每个 IoT 即插即用组件都会创建一个适配器实例。

网桥适配器创建并获取数字孪生体接口句柄。 适配器使用此句柄将设备功能绑定到数字孪生体。

通过使用配置文件中的信息，网桥适配器使用以下技术来实现通过网桥从完全设备到数字孪生体的通信：

- 直接建立信道。
- 创建设备观察程序来等待信道可用。

### <a name="configuration-file"></a>配置文件

IoT 即插即用网桥使用基于 JSON 的配置文件来指定：

- 如何连接到 IoT 中心或 IoT Central 应用程序：方式包括连接字符串、身份验证参数或设备预配服务 (DPS)。
- 网桥使用的 IoT 即插即用功能模型的位置。 此模型定义了 IoT 即插即用设备的功能，它是静态且不可变的。
- IoT 即插即用接口组件的列表，以及每个组件的以下信息：
- 接口 ID 和组件名称。
- 与组件交互所需的网桥适配器。
- 网桥适配器与设备建立通信所需的设备信息。 例如，硬件 ID，或者适配器、接口或协议的特定信息。
- 可选的网桥适配器子类型或接口配置（如果适配器支持与类似设备的多种通信类型的话）。 下面的示例展示了如何配置蓝牙传感器组件：

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

- 可选的全局网桥适配器参数列表。 例如，蓝牙传感器网桥适配器有受支持配置的字典。 需要蓝牙传感器适配器的接口组件可以选择其中一个配置作为 `blesensor_identity`：

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

## <a name="download-iot-plug-and-play-bridge"></a>下载 IoT 即插即用网桥

可以在 [IoT 即插即用网桥发布](https://github.com/Azure/iot-plug-and-play-bridge/releases)中下载包含受支持的适配器的预生成版本网桥，并展开资产列表来获取最新版本。 下载适合你的操作系统的最新版应用程序。

也可以下载和查看 [GitHub 上的 IoT 即插即用网桥](https://github.com/Azure/iot-plug-and-play-bridge)的源代码。

## <a name="next-steps"></a>后续步骤

至此，你已大致了解 IoT 即插即用网桥的体系结构，后续步骤是详细了解：

- [如何将 Linux 或 Windows 上运行的 IoT 即插即用网桥示例连接到 IoT 中心](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/quick_start.md)
- [构建和部署 IoT 即插即用桥接](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/build_deploy.md)
- [扩展 IoT 即插即用桥接](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/author_adapter.md)
- [GitHub 上的 IoT 即插即用网桥](https://github.com/Azure/iot-plug-and-play-bridge)
