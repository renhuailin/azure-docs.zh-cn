---
title: 开发 Azure IoT Edge 模块 | Microsoft Docs
description: 为 Azure IoT Edge 开发可与运行时和 IoT 中心通信的自定义模块
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8907af07fff7b315eec263d38b686c17218ed9d2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445466"
---
# <a name="develop-your-own-iot-edge-modules"></a>开发你自己的 IoT Edge 模块

Azure IoT Edge 模块可以与其他 Azure 服务连接，帮助你实现更大的云数据管道。 本文介绍如何开发模块以与 IoT Edge 运行时和 IoT 中心通信，并进而与 Azure 云的其他服务通信。

## <a name="iot-edge-runtime-environment"></a>IoT Edge 运行时环境

IoT Edge 运行时提供基础结构可集成多个 IoT Edge 模块的功能并将其部署到 IoT Edge 设备上。 任意程序都可打包为 IoT Edge 模块。 若要充分利用 IoT Edge 通信和管理功能，在模块中运行的程序可以使用 Azure IoT 设备 SDK 连接到本地 IoT Edge 中心。
::: moniker range=">=iotedge-2020-11"
模块还可以使用任何 MQTT 客户端连接到本地 IoT Edge 中心 MQTT broker。
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>将程序打包为 IoT Edge 模块

若要将程序部署到 IoT Edge 设备上，必须先将其容器化，并使用 Docker 兼容的引擎运行它。 IoT Edge 使用 [小鲸鱼](https://github.com/moby/moby)，它是 docker 的开源项目，作为其与 docker 兼容的引擎。 用于 Docker 的相同参数可以传递到 IoT Edge 模块。 有关详细信息，请参阅 [如何为 IoT Edge 模块配置容器 create 选项](how-to-use-create-options.md)。

## <a name="using-the-iot-edge-hub"></a>使用 IoT Edge 中心

IoT Edge 中心提供两种主要功能：连接到 IoT 中心的代理和本地通信。

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>从模块连接到 IoT Edge 中心

从模块连接到本地 IoT Edge 集线器涉及与任何客户端相同的连接步骤。 有关详细信息，请参阅 [连接到 IoT Edge 中心](iot-edge-runtime.md#connecting-to-the-iot-edge-hub)。

若要通过 AMQP 或 MQTT 使用 IoT Edge 路由，可以通过 Azure IoT SDK 使用 ModuleClient。 创建一个 ModuleClient 实例，将模块连接到设备上运行的 IoT Edge 中心，其方式与 DeviceClient 实例将 IoT 设备连接到 IoT 中心类似。 有关 ModuleClient 类及其通信方法的更多信息，请参阅首选 SDK 语言的 API 参考：[C#](/dotnet/api/microsoft.azure.devices.client.moduleclient)、[C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)、[Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient) 或 [Node.js](/javascript/api/azure-iot-device/moduleclient)。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要使用 IoT Edge MQTT broker，需要使用自己的 MQTT 客户端，并使用从 IoT Edge daemon 工作负荷 API 中检索到的信息自行启动连接。 <!--Need to add details here-->

若要详细了解如何选择路由或发布/订阅 MQTT broker，请参阅 [本地通信](iot-edge-runtime.md#local-communication)。

### <a name="mqtt-broker-primitives"></a>MQTT broker 基元

#### <a name="send-a-message-on-a-user-defined-topic"></a>针对用户定义的主题发送消息

利用 IoT Edge MQTT broker，你可以在任何用户定义的主题上发布消息。 为此，请授权您的模块发布特定主题，然后在连接到 MQTT broker 时从工作负荷 API 获取一个令牌作为密码，最后使用所选的 MQTT 客户端发布有关授权主题的消息。

#### <a name="receive-messages-on-a-user-defined-topic"></a>接收有关用户定义的主题的消息

对于 IoT Edge MQTT broker，接收消息类似。 首先，请确保你的模块有权订阅特定主题，然后在连接到 MQTT 代理时从工作负荷 API 获取令牌作为密码，最后使用所选的 MQTT 客户端订阅授权主题上的消息。

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT 中心基元

IoT 中心在以下意义上将模块实例视为与设备类似：

* 它可以发送[设备到云消息](../iot-hub/iot-hub-devguide-messaging.md)；
* 它可以接收专门针对其标识的[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
* 它具有不同的模块克隆，并独立于 [设备](../iot-hub/iot-hub-devguide-device-twins.md) 克隆和该设备的其他模块孪生;

目前，模块无法接收云到设备的消息，也无法使用文件上传功能。

编写模块时，可以连接到 IoT Edge 中心，并使用 IoT 中心基元，就像将 IoT 中心与设备应用程序一起使用一样。 IoT Edge 模块和 IoT 设备应用程序之间的唯一区别在于，你必须引用模块标识而不是设备标识。

#### <a name="device-to-cloud-messages"></a>设备到云的消息

IoT Edge 模块可通过充当本地 broker 的 IoT Edge 中心将消息发送到云，并将消息传播到云中。 若要启用对设备到云消息的复杂处理，IoT Edge 模块还可以截获并处理其他模块或设备发送到其本地 IoT Edge 中心的消息，并使用已处理的数据发送新消息。 因此，可以创建 IoT Edge 模块链来构建本地处理管道。

若要使用路由发送设备到云的遥测消息，请使用 Azure IoT SDK 的 ModuleClient。 借助 Azure IoT SDK，每个模块都具有模块 *输入* 和 *输出* 终结点的概念，这些终结点映射到特殊的 MQTT 主题。 使用 `ModuleClient.sendMessageAsync` 方法，它将在模块的输出终结点上发送消息。 然后在 edgeHub 中配置路由，以将此输出终结点发送到 IoT 中心。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

使用 MQTT broker 发送设备到云的遥测消息与发布有关用户定义的主题的消息类似，但对于模块，请使用以下 IoT 中心特殊主题： `devices/<device_name>/<module_name>/messages/events` 。 必须适当地设置授权。 还必须将 MQTT 桥配置为将本主题中的消息转发到云中。

::: moniker-end

若要使用路由处理消息，请首先设置路由，以便将来自其他终结点的消息 (模块或设备) 发送到模块的输入终结点，然后在模块的输入终结点上侦听消息。 每次返回一条新消息时，Azure IoT SDK 都将触发回调函数。 处理此回调函数的消息，并根据需要在模块终结点队列中发送新消息。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

使用 MQTT broker 处理消息类似于订阅有关用户定义的主题的消息，但使用的是模块输出队列 IoT Edge 特殊主题： `devices/<device_name>/<module_name>/messages/events` 。 必须适当地设置授权。 或者，您可以根据您选择的主题发送新的消息。

::: moniker-end

#### <a name="twins"></a>孪生

孪生是 IoT 中心提供的基元之一。 存在存储状态信息（包括元数据、配置和条件）的 JSON 文档。 每个模块或设备都有其自身的克隆。

若要使用 Azure IoT SDK 获取模块克隆，请调用 `ModuleClient.getTwin` 方法。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要获取与任何 MQTT 客户端的模块克隆，只需执行一些更多工作，因为获取克隆不是典型的 MQTT 模式。 该模块必须首先订阅 IoT 中心特殊主题 `$iothub/twin/res/#` 。 此主题名称继承自 IoT 中心，所有设备/模块都需要订阅同一主题。 这并不意味着设备彼此之间相互接收。 IoT 中心和 edgeHub 知道应该将哪些克隆传递到何处，即使所有设备都侦听相同的主题名称。 完成订阅后，该模块需要通过使用请求 ID 将消息发布到以下 IoT 中心特殊主题来请求输入 `$iothub/twin/GET/?$rid=1234` 。 此请求 ID 为任意 ID (即 GUID) ，IoT 中心将连同请求的数据一起发送回该 id。 这是客户端可以如何将其请求与响应配对。 结果代码是类似 HTTP 的状态代码，成功的编码为200。

::: moniker-end

若要接收 Azure IoT SDK 的模块克隆修补程序，请实现一个回调函数，并将其注册到 `ModuleClient.moduleTwinCallback` Azure IOT sdk 中的方法，以便每次出现一次克隆修补程序时都会触发回调函数。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要接收任何 MQTT 客户端的模块克隆修补程序，该过程与接收完整孪生非常相似：客户端需要订阅特殊 IoT 中心主题 `$iothub/twin/PATCH/properties/desired/#` 。 完成订阅后，当 IoT 中心发送对克隆的所需部分的更改时，客户端会接收它。

::: moniker-end

#### <a name="receive-direct-methods"></a>接收直接方法

若要通过 Azure IoT SDK 接收直接方法，请实现一个回调函数，并将其注册到 `ModuleClient.methodCallback` Azure IOT sdk 中的方法，以便每次出现直接方法时都触发回调函数。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要接收任何 MQTT 客户端的直接方法，此过程与接收克隆修补程序非常相似。 客户端需要确认是否已收到呼叫，并且可以同时发送回某些信息。 订阅的特殊 IoT 中心主题是 `$iothub/methods/POST/#` 。

::: moniker-end

## <a name="language-and-architecture-support"></a>语言和体系结构支持

IoT Edge 支持多种操作系统、设备体系结构和开发语言，因此你可以构建满足你的需求的方案。 使用此部分来了解用于开发自定义 IoT Edge 模块的选项。 可以在[为 IoT Edge 准备开发和测试环境](development-environment.md)中详细了解每种语言的工具支持和要求。

### <a name="linux"></a>Linux

对于下表中的所有语言，IoT Edge 支持 AMD64 和 ARM32 Linux 设备的开发。

| 开发语言 | 开发工具 |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中提供了对 ARM64 Linux 设备的开发和调试支持。 有关详细信息，请参阅[在 Visual Studio Code（预览版）中开发和调试 ARM64 IoT Edge 模块](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)。

### <a name="windows"></a>Windows

对于下表中的所有语言，IoT Edge 支持 AMD64 Windows 设备的开发。

| 开发语言 | 开发工具 |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code（无调试功能）<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>后续步骤

[为 IoT Edge 准备开发和测试环境](development-environment.md)

[使用 Visual Studio 开发适用于 IoT Edge 的 C# 模块](how-to-visual-studio-develop-module.md)

[使用 Visual Studio Code 开发适用于 IoT Edge 的模块](how-to-vs-code-develop-module.md)

[了解和使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)
