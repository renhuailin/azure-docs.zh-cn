---
title: 用于 Azure IoT Central 的设备开发 | Microsoft Docs
description: Azure IoT Central 是一种 IoT 应用程序平台，用于简化创建 IoT 解决方案。 本文概述了如何开发设备以连接到 IoT Central 应用程序。 设备使用遥测发送流式处理数据和属性以报告设备状态。 IoT Central 可以在设备上使用可写属性和调用命令来设置设备状态。
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: f2131ec5a0b939172097494dcd457b9d661614ad
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473489"
---
# <a name="iot-central-device-development-guide"></a>IoT Central 设备开发指南

IoT Central 应用程序使你可以在设备整个生命周期内监视和管理数百万台设备。 本指南适用于要执行以下操作的设备开发人员：实现代码以在连接到 IoT Central 的设备上运行。

设备使用以下基元与 IoT Central 应用程序进行交互：

- “遥测”是设备发送给 IoT Central 的数据。 例如，来自内载传感器的温度值流。
- “属性”是设备报告给 IoT Central 的状态值。 例如，设备的当前固件版本。 你还可以拥有 IoT Central 可以在设备上更新的可写属性，例如目标温度。
- “命令”由 IoT Central 进行调用以控制设备的行为。 例如，IoT Central 应用程序可能会调用命令来重启设备。

解决方案生成器负责在 IoT Central Web UI 中配置仪表板和设备视图，以直观呈现遥测、管理属性和调用命令。

## <a name="types-of-device"></a>设备类型

以下部分介绍了可连接到 IoT Central 应用程序的设备的主要类型：

### <a name="iot-device"></a>IoT 设备

IoT 设备是可直接连接到 IoT Central 的独立设备。 IoT 设备通常会将遥测从其内载或联网的传感器发送到 IoT Central 应用程序。 并且还可以报告属性值、接收可写属性值以及响应命令。

### <a name="iot-edge-device"></a>IoT Edge 设备

IoT Edge 设备直接连接到 IoT Central。 IoT Edge设备可以发送自己的遥测，报告其属性，并响应可写的属性更新和命令。 IoT Edge 模块可以本地处理 IoT Edge 设备上的数据。 IoT Edge 设备还可以充当其他设备（称为叶设备）的中介。 使用 IoT edge 设备的场景包括：

- 在将遥测发送到 IoT Central 之前，对其进行聚合或筛选。 此方法有助于降低向 IoT Central 发送数据的成本。
- 使无法直接连接到 IoT Central 的设备能够通过 IoT Central 设备连接。 例如，叶设备可能使用蓝牙连接到 IoT Edge 设备，后者随后通过 Internet 连接到 IoT Central。
- 本地控制叶设备，避免与通过 Internet 连接到 IoT Central 相关的延迟。

IoT Central 仅会看到 IoT Edge 设备，而不会看到连接到 IoT Edge 设备的叶设备。

若要了解详细信息，请参阅[将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序](./tutorial-add-edge-as-leaf-device.md)。

### <a name="gateways"></a>网关

网关设备管理连接到 IoT Central 应用程序的一个或多个下游设备。 可使用 IoT Central 配置下游设备与网关设备之间的关系。 IoT 设备和 IoT Edge 设备均可用作网关。 若要了解详细信息，请参阅[在 Azure IoT Central 应用程序中定义新的 IoT 网关设备类型](./tutorial-define-gateway-device-type.md)。

## <a name="connect-a-device"></a>连接一个设备

Azure IoT Central 使用 [Azure IoT 中心设备预配服务 (DPS)](../../iot-dps/about-iot-dps.md) 来管理所有设备注册和连接。

使用 DPS：

- 可让 IoT Central 支持大规模的设备加入和连接。
- 可让你离线生成设备凭据并配置设备，而无需通过 IoT Central UI 注册设备。
- 可让你使用自己的设备 ID 在 IoT Central 中注册设备。 使用自己的设备 ID 可以简化与现有后端办公系统的集成。
- 可以通过一致的单一方式将设备连接到 IoT Central。

若要了解详细信息，请参阅[连接到 Azure IoT Central](./concepts-get-connected.md) 和[最佳做法](concepts-best-practices.md)。

### <a name="security"></a>安全性

设备与 IoT Central 应用程序之间的连接使用[共享访问签名](./concepts-get-connected.md#sas-group-enrollment)或[行业标准 X.509 证书](./concepts-get-connected.md#x509-group-enrollment)来得到保护。

### <a name="communication-protocols"></a>通信协议

设备可用于连接到 IoT Central 的通信协议包括 MQTT、AMQP 和 HTTPS。 在内部，IoT Central 使用 IoT 中心来启用设备连接。 有关 IoT 中心针对设备连接支持的通信协议的详细信息，请参阅[选择通信协议](../../iot-hub/iot-hub-devguide-protocols.md)。

## <a name="implement-the-device"></a>实现设备

IoT Central 设备模板包括一个模型，用于指定该类型的设备应实现的行为。 行为包括遥测、属性和命令。

若要详细了解编辑模型的最佳做法，请参阅[编辑现有设备模板](howto-edit-device-template.md)。

> [!TIP]
> 可以将模型作为[数字孪生定义语言 (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl) JSON 文件从 IoT Central 导出。

每个模型都具有唯一的设备孪生模型标识符 (DTMI)，如 `dtmi:com:example:Thermostat;1`。 当设备连接到 IoT Central 时，它会发送其实现的模型的 DTMI。 然后 IoT Central 可以将正确的设备模板与设备相关联。

[IoT 即插即用](../../iot-develop/overview-iot-plug-and-play.md)定义设备在实现 DTDL 模型时应遵循的一组[约定](../../iot-develop/concepts-convention.md)。

[Azure IoT 设备 SDK](#languages-and-sdks) 包括对 IoT 即插即用约定的支持。

### <a name="device-model"></a>设备型号

设备模型是使用 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl) 定义的。 此语言可让你定义：

- 设备发送的遥测。 定义包括遥测的名称和数据类型。 例如，设备以双精度形式发送温度遥测。
- 设备报告给 IoT Central 的属性。 属性定义包括其名称和数据类型。 例如，设备将阀门的状态报告为布尔值。
- 设备可从 IoT Central 接收的属性。 还可以选择将属性标记为可写。 例如，IoT Central 将目标温度以双精度值将发送到设备。
- 设备响应的命令。 定义包含命令的名称，以及任何参数的名称和数据类型。 例如，设备响应一个指定在重新启动之前要等待的秒数的重新启动命令。

DTDL 模型可以是非组件或多组件模型 ：

- 非组件模型：简单的模型不使用嵌入或级联的组件。 所有遥测、属性和命令都定义为单个根组件。 有关示例，请参阅[恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)模型。
- 多组件模型。 包含两个或以上组件的更为复杂的模型。 这些组件包括单个根组件以及一个或多个附加嵌套组件。 有关示例，请参阅[温度控制器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)模型。

若要了解详细信息，请参阅 [IoT 即插即用建模指南](../../iot-develop/concepts-modeling-guide.md)

### <a name="conventions"></a>约定

设备在与 IoT Central 交换数据时，应遵循 IoT 即插即用约定。 约定包括：

- 当 DTMI 连接到 IoT Central 时发送 DTMI。
- 将格式正确的 JSON 有效负载和元数据发送到 IoT Central。
- 正确响应 IoT Central 中的可写属性和命令。
- 遵循组件命令的命名约定。

> [!NOTE]
> 目前 IoT Central 不完全支持 DTDL“Array”和“Geospatial”数据类型 。

若要了解有关设备与 IoT Central 交换的 JSON 消息格式的详细信息，请参阅[遥测、属性和命令有效负载](concepts-telemetry-properties-commands.md)。

若要了解有关 IoT 即插即用约定的详细信息，请参阅 [IoT 即插即用约定](../../iot-develop/concepts-convention.md)。

### <a name="device-sdks"></a>设备 SDK

使用 [Azure IoT 设备 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 中的一个来实现设备行为。 代码应做到以下几点：

- 将设备注册到 DPS，并使用 DPS 中的信息连接到 IoT Central 应用程序中的内部 IoT 中心。
- 公布设备实现的模型的 DTMI。
- 以设备模型指定的格式发送遥测。 IoT Central 使用设备模板中的模型来确定如何使用遥测，以便实现可视化效果和分析。
- 同步设备和 IoT Central 之间的属性值。 模型指定属性名称和数据类型，以便 IoT Central 可以显示信息。
- 为模型中指定的命令实现命令处理程序。 模型指定设备应使用的命令名称和参数。

有关设备模板的角色的详细信息，请参阅[什么是设备模板？](./concepts-device-templates.md)。

有关一些示例代码，请参阅[创建和连接客户端应用程序](./tutorial-connect-device.md)。

### <a name="languages-and-sdks"></a>语言和 SDK

有关支持的语言和 SDK 的详细信息，请参阅[了解和使用 Azure IoT 中心设备 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)。

## <a name="next-steps"></a>后续步骤

如果你是设备开发人员并想深入了解某种代码，建议执行的下一步骤是[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)。

如果希望了解有关使用 IoT Central 的详细信息，建议的后续步骤是尝试快速入门，从[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)开始。
