---
title: IoT 即插即用体系结构 | Microsoft Docs
description: 作为解决方案生成器，了解 IoT 即插即用的关键体系结构元素。
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 94376e1bc5192a0aa437066cb8a460e9aecd27a6
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607418"
---
# <a name="iot-plug-and-play-architecture"></a>IoT 即插即用体系结构

通过 IoT 即插即用，解决方案构建者无需进行任何手动配置即可将 IoT 设备集成到解决方案中。 IoT 即插即用的核心是设备 _模型_，该模型向支持 IoT 即插即用的应用程序介绍设备的功能。 该模型被构造为一组接口，这些接口定义以下内容：

- _属性_：表示设备或其他实体的只读或可写状态。 例如，设备序列号可以是只读属性，而调温器上的目标温度可以是可写属性。
- _遥测_：设备发出的数据，包括常规的传感器读数流、偶发性错误或信息消息。
- _命令_：描述可在设备上执行的功能或操作。 例如，命令可以重新启动网关，或使用远程相机拍照。

每个模型和接口都具有唯一的 ID。

下图显示了 IoT 即插即用解决方案的关键元素：

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT 即插即用体系结构":::

## <a name="model-repository"></a>模型存储库

[模型存储库](./concepts-model-repository.md)是模型和接口定义的存储区。 可以使用[数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 定义模型和接口。

利用 web UI，用户可以管理模型和接口。

模型存储库具有内置的基于角色的访问控制，因此用户可以限制对接口定义的访问。

## <a name="devices"></a>设备

设备生成器使用一个 [Azure IoT 设备 SDK](./libraries-sdks.md) 来实现在 IoT 设备上运行的代码。 设备 SDK 可帮助设备生成器执行以下操作：

- 安全地连接到 IoT 中心。
- 向 IoT 中心注册设备，并公告标识设备实现的 DTDL 接口集合的模型 ID。
- 同步在设备与 IoT 中心之间的 DTDL 接口中定义的属性。
- 添加 DTDL 接口中所定义命令的命令处理程序。
- 将遥测数据发送到 IoT 中心。

## <a name="iot-edge-gateway"></a>IoT Edge 网关

IoT Edge 网关可充当中介，用于连接无法直接连接到 IoT 中心的 IoT 即插即用设备。 有关详细信息，请参阅“[如何将 IoT Edge 设备用作网关](../iot-edge/iot-edge-as-gateway.md)”。

## <a name="iot-edge-modules"></a>IoT Edge 模块

通过 _IoT Edge module_，允许用户部署和管理 Edge 上的业务逻辑。 Azure IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Azure 服务（如 Azure 流分析）或你自己特定于解决方案的代码。

_IoT Edge 中心_ 是组成 Azure IoT Edge 运行时的模块之一。 它通过公开与 IoT 中心相同的协议终结点，充当 IoT 中心的本地代理。 这种一致性意味着客户端（无论是设备还是模块）可以连接到 IoT Edge 运行时，就像连接到 IoT 中心一样。

设备 SDK 可帮助模块生成器执行以下操作：

- 使用 IoT Edge 中心安全地连接到 IoT 中心。
- 向 IoT 中心注册模块，并公告标识设备实现的 DTDL 接口集合的模型 ID。
- 同步在设备与 IoT 中心之间的 DTDL 接口中定义的属性。
- 添加 DTDL 接口中所定义命令的命令处理程序。
- 将遥测数据发送到 IoT 中心。

## <a name="iot-hub"></a>IoT 中心

[IoT 中心](../iot-hub/about-iot-hub.md)是一项云托管服务，充当消息中心，用于在 IoT 解决方案与所管理的设备之间进行双向通信。

IoT 中心：

- 使设备实现的模型 ID 可供后端解决方案使用。
- 保持数字孪生体与连接到中心的每个即插即用设备的关联。
- 将遥测数据流转发到其他服务进行处理或存储。
- 将数字孪生体更改事件路由到其他服务以启用设备监视。

## <a name="backend-solution"></a>后端解决方案

后端解决方案可与 IoT 中心中的数字孪生体交互，从而监视和控制已连接的设备。 使用一个服务 SDK 来实现后端解决方案。 为了了解已连接设备的功能，解决方案后端将执行以下操作：

1. 检索在 IoT 中心注册的设备的模型 ID。
1. 使用模型 ID 从任何模型存储库检索接口定义。
1. 使用模型分析程序从接口定义中提取信息。

后端解决方案可以使用接口定义中的信息来执行以下操作：

- 读取设备报告的属性值。
- 更新设备上的可写属性。
- 调用设备实现的命令。
- 了解设备所发送遥测数据的格式。

## <a name="next-steps"></a>后续步骤

现在，用户已大致了解 IoT 即插即用解决方案的体系结构，后续步骤是详细了解以下内容：

- [模型存储库](./concepts-model-repository.md)
- [数字孪生模型集成](./concepts-model-discovery.md)
- [为 IoT 即插即用进行开发](./concepts-developer-guide-device.md)
