---
title: 了解运行时如何管理设备 - Azure IoT Edge |Microsoft Docs
description: 了解 IoT Edge 运行时如何管理设备上的模块、安全性、通信和报告
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: c0c3a452c93b88483ac7027405665c26ceab8183
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368490"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 运行时及其体系结构

IoT Edge 运行时是将某个设备转换为 IoT Edge 设备的程序集合。 在 IoT Edge 运行时组件的共同作用下，IoT Edge 设备可以接收要在边缘上运行的代码并传递结果。

IoT Edge 运行时负责 IoT Edge 设备上的以下功能：

* 在设备上安装和更新工作负荷。

* 维护设备上的 Azure IoT Edge 安全标准。

* 确保 [IoT Edge 模块](iot-edge-modules.md)始终处于运行状态。

* 将模块运行状况报告给云以进行远程监视。

* 管理下游设备与 IoT Edge 设备之间的通信。

* 管理 IoT Edge 设备上的模块之间的通信。

* 管理 IoT Edge 设备和云之间的通信。
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* 管理 IoT Edge 设备之间的通信。
::: moniker-end

![运行时向 IoT 中心传达见解和模块运行状况](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 运行时的职责分为两类：通信和模块管理。 这两个角色由作为 IoT Edge 运行时一部分的两个组件执行。 *IoT Edge 代理* 可部署和监视模块，而 *IoT Edge 中心* 负责通信。

IoT Edge 代理和 IoT Edge 中心都是模块，就像在 IoT Edge 设备上运行的任何其他模块一样。 有时将它们称为“运行时模块”。

## <a name="iot-edge-agent"></a>IoT Edge 代理

IoT Edge 代理是组成 Azure IoT Edge 运行时的两个模块之一。 它负责实例化模块、确保它们继续运行以及报告返回到 IoT 中心的模块的状态。 此配置数据作为 IoT Edge 代理模块孪生的属性写入。

[IoT Edge 安全守护程序](iot-edge-security-manager.md)在设备启动时启动 IoT Edge 代理。 该代理从 IoT 中心检索其模块孪生并检查部署清单。 部署清单是一个 JSON 文件，它声明了需要启动的模块。

部署清单中的每项都包含有关模块的特定信息，并由 IoT Edge 代理用于控制模块的生命周期。 有关 IoT Edge 代理用来控制模块的所有属性的详细信息，请参阅 [IoT Edge 代理的属性和 IoT Edge 中心模块孪生](module-edgeagent-edgehub.md)。

IoT Edge 代理会将运行时响应发送到 IoT 中心。 下面是可能的响应的列表：
  
* 200 - 正常
* 400 - 部署配置格式不正确或无效。
* 417 - 没有为设备设置部署配置。
* 412 - 部署配置中的架构版本无效。
* 406 - IoT Edge 设备脱机或不发送状态报告。
* 500 - IoT Edge 运行时中出现了一个错误。

有关创建部署清单的详细信息，请参阅 [了解如何部署模块和在 IoT Edge 中建立路由](module-composition.md)。

### <a name="security"></a>安全性

IoT Edge 代理在 IoT Edge 设备的安全性中起着关键作用。 例如，它会执行某些操作，如启动之前验证模块的映像。

有关 Azure IoT Edge 安全框架的详细信息，请阅读有关 [IoT Edge 安全管理器](iot-edge-security-manager.md)的内容。

## <a name="iot-edge-hub"></a>IoT Edge 中心

IoT Edge 中心是组成 Azure IoT Edge 运行时的另一个模块。 它通过公开与 IoT 中心相同的协议终结点，充当 IoT 中心的本地代理。 这种一致性意味着客户端可以像连接到 IoT 中心一样连接到 IoT Edge 运行时。

IoT Edge 中心不是在本地运行的完整版本的 IoT 中心。 IoT Edge 中心将一些任务以无提示方式委托给 IoT 中心。 例如，IoT Edge 集线器会在其第一次连接时自动从 IoT 中心下载授权信息，使设备能够连接。 在建立第一个连接后，IoT Edge 集线器在本地缓存授权信息。 此设备的未来连接经过授权，无需再次从云中下载授权信息。

### <a name="cloud-communication"></a>云通信

为减少 IoT Edge 解决方案使用的带宽，IoT Edge 中心优化了对云的实际连接数量。 IoT Edge 中心采用来自模块或下游设备的逻辑连接，并将它们组合为连接到云的单个物理连接。 此过程的详细信息对解决方案的其他部分透明。 即使客户端都通过相同连接进行发送，它们也会认为具有自己的云连接。 IoT Edge 集线器可以使用 AMQP 或 MQTT 协议，而与下游设备使用的协议无关。 不过，IoT Edge 集线器目前仅支持使用 AMQP 作为上游协议及其多路复用功能，将逻辑连接合并为单个物理连接。 AMQP 是默认的上游协议。

![IoT Edge 中心是物理设备和 IoT 中心之间的网关](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge 中心可以确定其是否连接到了 IoT 中心。 如果连接丢失，IoT Edge 中心将在本地保存消息或孪生更新。 一旦重新建立连接，将同步所有数据。 用于此临时缓存的位置由 IoT Edge 中心的模块孪生的属性决定。 只要设备具有存储容量，缓存的大小就没有限制并且会增加。  有关详细信息，请参阅[脱机功能](offline-capabilities.md)。

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>模块通信

IoT Edge 中心促进模块间通信。 使用 IoT Edge 中心作为消息中转站可以保持模块之间相互独立。 模块只需指定它们接受消息的输入和写入消息的输出。 解决方案开发人员可以将这些输入和输出拼接在一起，以便于模块按特定于该解决方案的顺序处理数据。

![IoT Edge 中心促进模块间通信。](./media/iot-edge-runtime/module-endpoints.png)

为了将数据发送到 IoT Edge 中心，模块会调用 SendEventAsync 方法。 第一个参数指定要发送消息的输出。 下面的伪代码在 **output1** 上发送消息：

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

若要接收消息，请注册一个回叫，用于处理在特定输入上传入的消息。 下面的伪代码注册要用于处理在 **input1** 上接收到的所有消息的函数 messageProcessor：

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

有关 ModuleClient 类及其通信方法的更多信息，请参阅首选 SDK 语言的 API 参考：[C#](/dotnet/api/microsoft.azure.devices.client.moduleclient)、[C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)、[Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient) 或 [Node.js](/javascript/api/azure-iot-device/moduleclient)。

解决方案开发者负责指定用于确定 IoT Edge 中心如何在模块间传递消息的规则。 路由规则在云中定义，并向下推送到其模块孪生中的 IoT Edge 中心。 使用 IoT 中心路由的同一语法定义在 Azure IoT Edge 中的模块之间的路由。 有关详细信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。

![模块之间的路由通过 IoT Edge 中心](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>本地通信

IoT Edge 中心有助于实现本地通信。 它通过协调消息启用设备到模块的模块到模块的通信，使设备和模块相互独立。

>[!NOTE]
> MQTT broker 功能 IoT Edge 版本1.2 的公共预览版。 必须显式启用它。

IoT Edge 中心支持两种协调机制：

1. [IoT 中心支持的消息路由功能](../iot-hub/iot-hub-devguide-messages-d2c.md)和
2. 满足[MQTT standard v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)的通用 MQTT broker

#### <a name="using-routing"></a>使用路由

第一种协调机制利用与 IoT 中心相同的路由功能来指定如何在设备或模块之间传递消息。 第一个设备或模块指定了其接受消息的输入和它们写入消息的输出。 然后，解决方案开发人员可以使用潜在的筛选器在源（例如输出）和目标（如输入）之间路由消息。

![模块之间的路由通过 IoT Edge 中心](./media/iot-edge-runtime/module-endpoints-routing.png)

可以通过 AMQP 或 MQTT 协议使用 Azure IoT 设备 Sdk 生成的设备或模块使用路由。 支持所有消息传递 IoT 中心基元（如遥测、直接方法、C2D、孪生），但不支持通过用户定义的主题进行通信。

有关路由的详细信息，请参阅 [了解如何部署模块和在 IoT Edge 中建立路由](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>使用 MQTT broker

第二个协调机制基于标准 MQTT broker。 MQTT 是一种轻型消息传输协议，可保证对资源受约束设备的最佳性能，并且是一种常用的发布和订阅标准。 设备或模块会订阅主题，以接收其他设备或模块发布的消息。 IoT Edge 中心实现了其自己的 MQTT broker，遵循 [MQTT 版本3.1.1 的规范](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)。

与路由相比，MQTT broker 启用其他两种通信模式：本地广播和点到点通信。 当一个设备或模块需要本地警报多个其他设备或模块时，本地广播非常有用。 点到点通信使两个 IoT Edge 设备或两个 IoT 设备在本地进行通信，而不会往返到云中。

![IoT Edge 中心进行本地发布和订阅](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

使用 Azure IoT 设备 Sdk （通过 MQTT 协议或任何常规用途的 MQTT 客户端进行通信）生成的设备或模块可以使用 MQTT broker。 除了 C2D 所有消息 IoT 中心基元（如遥测、直接方法、孪生）。 受 IoT 中心基元使用的 IoT 中心特殊主题受支持，因此是用户定义的主题。
本主题可以是 IoT 中心的特殊主题，也可以是用户定义的主题。

与路由机制不同，消息的排序只是最尽力，而且不能保证和筛选消息不受代理的支持。 但缺乏这些功能，可以使 MQTT broker 比路由快。

有关 MQTT broker 的详细信息，请参阅 [发布和订阅 IoT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>协调机制之间的比较

以下是每个协调机制的可用功能：

|功能  | 路由  | MQTT broker  |
|---------|---------|---------|
|D2C 遥测    |     &#10004;    |         |
|本地遥测     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|双子     |    &#10004;     |    &#10004;     |
|设备的 C2D     |   &#10004;      |         |
|中间件排序     |    &#10004;     |         |
|Filtering     |     &#10004;    |         |
|用户定义的主题     |         |    &#10004;     |
|设备到设备     |         |    &#10004;     |
|本地广播     |         |    &#10004;     |
|性能     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>连接到 IoT Edge 集线器

IoT Edge 集线器接受来自设备或模块客户端（通过 MQTT 协议或 AMQP 协议）的连接。

>[!NOTE]
> IoT Edge 中心支持使用 MQTT 或 AMQP 进行连接的客户端， 它不支持使用 HTTP 的客户端。

当客户端连接到 IoT Edge 集线器时，将发生以下情况：

1. 如果 (推荐的) 使用传输层安全性 (TLS) ，则会生成一个 TLS 通道，用于在客户端与 IoT Edge 中心之间建立加密通信。
2. 身份验证信息将从客户端发送到 IoT Edge 集线器以标识自身。
3. IoT Edge 中心根据其授权策略来授权或拒绝连接。

#### <a name="secure-connections-tls"></a>安全连接 (TLS) 

默认情况下，IoT Edge 中心仅接受使用传输层安全性保护的连接 (TLS) ，例如，第三方无法解密的加密连接。

如果客户端连接到端口 8883 (MQTTS) 或 5671 (AMQPS) 到 IoT Edge 中心，则必须生成 TLS 通道。 在 TLS 握手期间，IoT Edge 集线器发送其证书链，客户端需要对其进行验证。 为了验证证书链，必须在客户端上将 IoT Edge 集线器的根证书安装为受信任的证书。 如果根证书不受信任，则 IoT Edge 中心将拒绝客户端库，并出现证书验证错误。

在 [透明网关](how-to-create-transparent-gateway.md) 和 [准备下游设备](how-to-connect-downstream-device.md#prepare-a-downstream-device) 文档中介绍了在设备客户端上安装代理的根证书所要遵循的步骤。 通过利用 IoT Edge 守护程序 API，模块可以使用与 IoT Edge 中心相同的根证书。

#### <a name="authentication"></a>身份验证

IoT Edge 中心仅接受来自具有 IoT 中心标识的设备或模块的连接，例如，已在 IoT 中心内注册，并且具有 IoT 中心支持的三台客户端身份验证方法之一，以提供证明其身份： [对称密钥身份验证](how-to-authenticate-downstream-device.md#symmetric-key-authentication)、 [x.509 自签名身份](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)验证、 [x.509 CA 签名身份验证](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)。  此类 IoT 中心标识可由 IoT Edge 中心本地验证，因此，仍可在脱机状态下进行连接。

说明：

* IoT Edge 模块目前仅支持对称密钥身份验证。
* IoT Edge 中心 MQTT 代理不接受仅包含本地用户名和密码的 MQTT 客户端，必须使用 IoT 中心标识。

#### <a name="authorization"></a>授权

经过身份验证后，IoT Edge 中心可以通过两种方式授权客户端连接：

* 验证客户端是否属于 IoT 中心定义的一组受信任的客户端。 受信任的客户端集是通过在 IoT 中心设置父/子或设备/模块关系来指定的。 在 IoT Edge 中创建模块时，会自动在此模块与其 IoT Edge 设备之间建立信任关系。 这是路由协调机制支持的唯一授权模型。

* 设置授权策略。 此授权策略是一个文档，其中列出了可访问 IoT Edge 中心上的资源的所有授权客户端标识。 这是 IoT Edge 集线器 MQTT broker 使用的主要授权模型，不过，MQTT broker for IoT 中心主题也可以理解父/子和设备/模块关系。

### <a name="remote-configuration"></a>远程配置

IoT Edge 中心由云完全控制。 它通过其 [模块](iot-edge-modules.md#module-twins)克隆从 IoT 中心获得其配置。 其中包括：

* 路由配置
* 授权策略
* MQTT 桥配置

此外，还可以通过 [在 IoT Edge 集线器上设置环境变量](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)来完成多种配置。
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>运行时质量遥测

IoT Edge 从主机运行时和系统模块收集匿名遥测以提高产品质量。 此信息称为运行时质量遥测。 收集的遥测数据作为设备到云的消息从 IoT Edge 代理定期发送到 IoT 中心。 这些消息不会显示在客户的常规遥测中，也不会消耗任何消息配额。

IoT Edge 代理和中心生成指标，你可以收集这些指标来了解设备性能。 IoT Edge 代理收集这些指标的子集，作为运行时质量遥测的一部分。 为运行时质量遥测收集的指标标有 `ms_telemetry` 标志。 有关所有可用指标的信息，请参阅 [Access 内置指标](how-to-access-built-in-metrics.md)。

任何个人身份信息或组织识别信息（如设备和模块名称）都将在上传之前删除，以确保运行时质量遥测的匿名特性。

IoT Edge 代理每小时收集一次遥测数据，每 24 小时向 IoT 中心发送一条消息。

如果要选择不从设备发送运行时遥测数据，可通过以下两种方式实现此目的：

* 将 `SendRuntimeQualityTelemetry` 环境变量的 edgeAgent 设置为 `false`，或者
* 在部署过程中取消选中 Azure 门户中的选项。

## <a name="next-steps"></a>后续步骤

* [了解 Azure IoT Edge 模块](iot-edge-modules.md)
* [了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)
* [了解如何发布和订阅 IoT Edge](how-to-publish-subscribe.md)
* [了解 IoT Edge 运行时指标](how-to-access-built-in-metrics.md)
