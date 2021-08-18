---
title: 开发 Azure IoT Edge 模块 | Microsoft Docs
description: 为 Azure IoT Edge 开发可与运行时和 IoT 中心通信的自定义模块
author: kgremban
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fafb9475d308863113fa943d4e52cf3c1b5652cd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728823"
---
# <a name="develop-your-own-iot-edge-modules"></a>开发你自己的 IoT Edge 模块

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge 模块可以与其他 Azure 服务连接，帮助你实现更大的云数据管道。 本文介绍如何开发模块以与 IoT Edge 运行时和 IoT 中心通信，并进而与 Azure 云的其他服务通信。

## <a name="iot-edge-runtime-environment"></a>IoT Edge 运行时环境

IoT Edge 运行时提供基础结构可集成多个 IoT Edge 模块的功能并将其部署到 IoT Edge 设备上。 任意程序都可打包为 IoT Edge 模块。 若要充分利用 IoT Edge 通信和管理功能，在模块中运行的程序可以使用 Azure IoT 设备 SDK 连接到本地 IoT Edge 中心。
::: moniker range=">=iotedge-2020-11"
模块还可以使用任何 MQTT 客户端连接到本地 IoT Edge 中心 MQTT 代理。
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>将程序打包为 IoT Edge 模块

若要将程序部署在 IoT Edge 设备上，必须先将其容器化，并使用 Docker 兼容的引擎来运行它。 IoT Edge 使用 [Moby](https://github.com/moby/moby)（Docker 之后的开源项目）作为其 Docker 兼容的引擎。 你在 Docker 中惯于使用的参数都可以传递到 IoT Edge 模块。 若要了解详细信息，请参阅[如何配置 IoT Edge 模块的容器创建选项](how-to-use-create-options.md)。

## <a name="using-the-iot-edge-hub"></a>使用 IoT Edge 中心

IoT Edge 中心提供两种主要功能：连接到 IoT 中心的代理和本地通信。

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>从模块连接到 IoT Edge 中心

从模块连接到本地 IoT Edge 中心所涉及的连接步骤与任何客户端的连接步骤相同。 有关详细信息，请参阅[连接到 IoT Edge 中心](iot-edge-runtime.md#connecting-to-the-iot-edge-hub)。

若要使用基于 AMQP 或 MQTT 的 IoT Edge 路由，可以使用 Azure IoT SDK 中的 ModuleClient。 创建一个 ModuleClient 实例，将模块连接到设备上运行的 IoT Edge 中心，其方式与 DeviceClient 实例将 IoT 设备连接到 IoT 中心类似。 有关 ModuleClient 类及其通信方法的更多信息，请参阅首选 SDK 语言的 API 参考：[C#](/dotnet/api/microsoft.azure.devices.client.moduleclient)、[C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)、[Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient) 或 [Node.js](/javascript/api/azure-iot-device/moduleclient)。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要使用 IoT Edge MQTT 代理，需要自带 MQTT 客户端，并使用从 IoT Edge 守护程序工作负载 API 检索到的信息自行发起连接。 <!--Need to add details here-->

有关在路由或使用 MQTT 代理实现发布/订阅之间进行选择的详细信息，请参阅[本地通信](iot-edge-runtime.md#local-communication)。

### <a name="mqtt-broker-primitives"></a>MQTT 代理基元

#### <a name="send-a-message-on-a-user-defined-topic"></a>发送有关用户定义的主题的消息

使用 IoT Edge MQTT 代理可以发布有关用户定义的任何主题的消息。 为此，请授权模块发布有关特定主题的消息，然后在连接到 MQTT 代理时通过工作负载 API 获取一个用作密码的令牌，最后使用你选择的 MQTT 客户端发布有关已授权的主题的消息。

#### <a name="receive-messages-on-a-user-defined-topic"></a>接收有关用户定义的主题的消息

使用 IoT Edge MQTT 代理接收消息的过程与上面类似。 首先请确保已授权模块订阅特定的主题，然后在连接到 MQTT 代理时通过工作负载 API 获取一个用作密码的令牌，最后使用你选择的 MQTT 客户端订阅有关已授权的主题的消息。

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT 中心基元

IoT 中心在以下意义上将模块实例视为与设备类似：

* 它可以发送[设备到云消息](../iot-hub/iot-hub-devguide-messaging.md)；
* 它可以接收专门针对其标识的[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
* 它拥有模块孪生，这与[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)和该设备的其他模块孪生不同且相隔离；

目前，模块无法接收云到设备的消息，也不能使用文件上传功能。

编写模块时，可以连接到 IoT Edge 中心并使用 IoT 中心基元，就像对设备应用程序使用 IoT 中心时一样。 IoT Edge 模块与 IoT 设备应用程序之间的唯一差别在于，你必须引用模块标识而不是设备标识。

#### <a name="device-to-cloud-messages"></a>设备到云的消息

IoT Edge 模块可以通过充当本地代理的 IoT Edge 中心将消息发送到云，并将消息传播到云中。 为了启用设备到云消息的复杂处理，IoT Edge 模块还可以截获并处理其他模块或设备发送到 IoT Edge 模块本地 IoT Edge 中心的消息，并使用处理的数据发送新消息。 因此，可以创建 IoT Edge 模块链来构建本地处理管道。

若要使用路由发送设备到云的遥测消息，请使用 Azure IoT SDK 的 ModuleClient。 使用 Azure IoT SDK 时，每个模块都具有模块输入和输出终结点的概念，这些终结点映射到特殊的 MQTT 主题。  使用 `ModuleClient.sendMessageAsync` 方法，它会在模块的输出终结点上发送消息。 然后在 edgeHub 中配置路由，以将此输出终结点发送到 IoT 中心。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

使用 MQTT 代理发送设备到云的遥测消息类似于发布有关用户定义的主题的消息，但要对模块使用以下 IoT 中心特殊主题：`devices/<device_name>/<module_name>/messages/events`。 必须正确设置授权。 此外，必须将 MQTT 网桥配置为向云转发有关此主题的消息。

::: moniker-end

若要使用路由处理消息，请先设置一个路由用于将来自另一终结点（模块或设备）的消息发送到模块的输入终结点，然后侦听模块的输入终结点上的消息。 每次返回新的消息时，Azure IoT SDK 都会触发一个回调函数。 使用此回调函数处理消息，然后选择性地在模块终结点队列中发送新消息。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

使用 MQTT 代理处理消息类似于订阅有关用户定义的主题的消息，但要使用模块输出队列的 IoT Edge 特殊主题：`devices/<device_name>/<module_name>/messages/events`。 必须正确设置授权。 可以选择性地发送有关你选择的主题的新消息。

::: moniker-end

#### <a name="twins"></a>孪生

孪生体是 IoT 中心提供的基元之一。 有些 JSON 文档会存储状态信息（包括元数据、配置和条件）。 每个模块或设备都有自身的孪生体。

若要使用 Azure IoT SDK 获取模块孪生体，请调用 `ModuleClient.getTwin` 方法。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要使用任何 MQTT 客户端获取模块孪生体，需要执行稍多一些的操作，因为获取孪生体不是典型的 MQTT 模式。 模块必须先订阅 IoT 中心特殊主题 `$iothub/twin/res/#`。 此主题名称继承自 IoT 中心，并且所有设备/模块需要订阅同一主题。 这并不意味着设备会接收彼此的孪生体。 IoT 中心和 edgeHub 中心知道哪个孪生体应传送到何处，即使所有设备侦听同一个主题名称。 配置订阅后，模块需要使用请求 ID `$iothub/twin/GET/?$rid=1234` 将消息发布到后面的 IoT 中心特殊主题，以请求孪生体。 此请求 ID 是一个任意 ID（即 GUID），IoT 中心会将该 ID 与请求的数据一起发回。 这就是客户端如何将其请求与响应配对。 结果代码是类似于 HTTP 的状态代码，如果状态为成功，则会编码为 200。

::: moniker-end

若要使用 Azure IoT SDK 接收模块孪生体补丁，请实现一个回调函数并将其注册到 Azure IoT SDK 中的 `ModuleClient.moduleTwinCallback` 方法，以便每次传入孪生体补丁时都会触发该回调函数。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要使用任何 MQTT 客户端接收模块孪生体补丁，该过程非常相似于接收完整孪生体：客户端需要订阅特殊 IoT 中心主题 `$iothub/twin/PATCH/properties/desired/#`。 配置订阅后，当 IoT 中心发送孪生体所需部分的某项更改时，客户端会接收该更改。

::: moniker-end

#### <a name="receive-direct-methods"></a>接收直接方法

若要使用 Azure IoT SDK 接收直接方法，请实现一个回调函数并将其注册到 Azure IOT SDK 中的 `ModuleClient.methodCallback` 方法，以便每次传入直接方法时都会触发该回调函数。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

若要使用任何 MQTT 客户端接收直接方法，该过程非常相似于接收孪生体补丁。 客户端需要反向确认它已收到调用，同时可以发回某些信息。 要订阅的特殊 IoT 中心主题是 `$iothub/methods/POST/#`。

::: moniker-end

## <a name="language-and-architecture-support"></a>语言和体系结构支持

IoT Edge 支持多种操作系统、设备体系结构和开发语言，因此你可以构建满足你的需求的方案。 使用此部分来了解用于开发自定义 IoT Edge 模块的选项。 可以在[为 IoT Edge 准备开发和测试环境](development-environment.md)中详细了解每种语言的工具支持和要求。

### <a name="linux"></a>Linux

对于下表中的所有语言，IoT Edge 支持 AMD64 和 ARM32 Linux 容器的开发。

| 开发语言 | 开发工具 |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中提供了对 ARM64 Linux 容器的开发和调试支持。 有关详细信息，请参阅[在 Visual Studio Code（预览版）中开发和调试 ARM64 IoT Edge 模块](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)。

### <a name="windows"></a>Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
对于下表中的所有语言，IoT Edge 支持 AMD64 Windows 容器的开发。

| 开发语言 | 开发工具 |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code（无调试功能）<br>Visual Studio 2017/2019 |
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 1.1 LTS 是最后一个支持 Windows 容器的发布通道。 从版本 1.2 开始，将不再支持 Windows 容器。

有关通过 Windows 容器进行开发的信息，请参阅本文的 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 版本。

:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>后续步骤

[为 IoT Edge 准备开发和测试环境](development-environment.md)

[使用 Visual Studio 开发适用于 IoT Edge 的 C# 模块](how-to-visual-studio-develop-module.md)

[使用 Visual Studio Code 开发适用于 IoT Edge 的模块](how-to-vs-code-develop-module.md)

[了解和使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)
