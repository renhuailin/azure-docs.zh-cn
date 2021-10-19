---
title: IoT 概念和 Azure IoT 中心 | Microsoft Docs
description: 本文介绍 Azure IoT 中心新用户需要了解的基本概念
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: 0ba840e4382aba96ead1d97f954362539833ba97
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670380"
---
# <a name="iot-concepts-and-azure-iot-hub"></a>IoT 概念和 Azure IoT 中心

本文介绍物联网 (IoT)、Azure IoT 中心和 IoT 设备。

## <a name="iot-concepts"></a>IoT 概念

物联网 (IoT) 通常定义为通过 Internet 或其他通信网络连接到其他设备和服务并与之交换数据的物理设备网络。 目前，全球有超过 100 亿台联网设备，并且每年都在增加。 任何可以嵌入必要传感器和软件的设备都可以通过 Internet 进行连接。 以下技术使 IoT 成为可能：

- 访问低成本、低功耗的传感器。
- 支持 Internet 连接的各种协议。
- Azure 等云计算平台。
- 大数据。
- 机器学习。
- 人工智能。

## <a name="azure-iot-hub"></a>Azure IoT 中心

IoT 中心是托管在云中的托管服务，充当中央消息中心，用于 IoT 应用程序及其连接设备之间的通信。 可以安全可靠地连接数百万台设备及其后端解决方案。 几乎任何设备都可以连接到 IoT 中心。 

支持多种消息传递模式，包括“设备到云”遥测、从设备上传文件，以及用于从云中控制设备的请求-答复方法。 IoT 中心还支持监视，以帮助跟踪设备创建、设备连接和设备故障。

IoT 中心可扩展为数百万个同时连接的设备，以及每秒数百万个事件以支持 IoT 工作负载。 有关缩放 IoT 中心的详细信息，请参阅 [IoT 中心缩放](iot-hub-scaling.md)。 若要详细了解 IoT 中心提供的多层服务以及如何最好地满足可伸缩性需求，请查看[定价页](https://azure.microsoft.com/pricing/details/iot-hub/)。

可以将 IoT 中心与其他 Azure 服务集成，生成完整的端对端解决方案。 例如，使用：

* [Azure 事件网格](../event-grid/index.yml)，可以采用可靠、可缩放且安全的方式快速地让你的业务响应关键事件。

* [Azure 逻辑应用](../logic-apps/index.yml)，可以自动执行业务流程。

* [Azure 机器学习](iot-hub-weather-forecast-machine-learning.md)，可为解决方案添加机器学习和 AI 模型。

* [Azure 流分析](../stream-analytics/index.yml)，可从设备对数据流执行实时分析计算。

IoT 中心具有 [IoT 中心的服务级别协议](https://azure.microsoft.com/support/legal/sla/iot-hub/)的 99.9% 的可用性。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/) 说明了 Azure 作为整体的保证可用性。

每个 Azure 订阅都有默认配额限制，以防止服务滥用。 这些限制可能影响 IoT 解决方案的作用域。 每个订阅的当前限制是每个订阅 50 个 IoT 中心。 可以通过联系支持人员，请求增加配额。 有关详细信息，请参阅 [IoT 中心配额和限制](iot-hub-devguide-quotas-throttling.md)。 有关配额限制的详细信息，请参阅以下文章之一：

* [Azure 订阅服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)（IoT 中心限制和你）

## <a name="iot-devices"></a>IoT 设备

IoT 设备不同于其他客户端（例如浏览器和移动应用）。 具体而言，IoT 设备具有以下特征：

- 通常是无人操作的嵌入式系统。
- 可以部署到物理访问开销十分昂贵的远程位置。
- 可能只能通过解决方案后端来访问。
- 能力和处理资源可能都有限。
- 网络连接可能不稳定、缓慢或成本高昂。
- 可能需要使用专有、自定义或行业特定的应用程序协议。

### <a name="device-identity"></a>设备标识

每个 IoT 中心都有一个标识注册表，存储允许连接到 IoT 中心的设备和模块的相关信息。 IoT 中心的标识注册表中必须先有设备或模块的条目，然后该设备或模块才能连接到 IoT 中心。 设备或模块还必须基于标识注册表中存储的凭据向 IoT 中心进行身份验证。

我们支持通过两种方法在设备与 IoT 中心之间进行身份验证。 你可以使用基于 SAS 令牌的身份验证或 X.509 证书身份验证。

基于 SAS 的令牌方法通过将对称密钥关联到每个调用，为设备向 IoT 中心发出的每个调用提供身份验证。 使用基于 X.509 的身份验证可以在建立传输层安全性 (TLS) 标准连接的过程中，对物理层上的 IoT 设备进行身份验证。 基于安全令牌的方式可在没有 X.509 身份验证的情况下使用，但这种模式的安全性较低。 这两种方式的选择主要取决于设备验证需要达到的安全程度和设备上安全储存的可用性（以安全存储私钥）。

可以使用 [IoT 中心设备预配服务](../iot-dps/index.yml)来一次设置并预配许多设备。

### <a name="device-communication"></a>设备通信

选择身份验证方法后，将使用传输层安全性 (TLS) 标准来保护 IoT 设备与 IoT 中心之间的 Internet 连接。 Azure IoT 支持 TLS 1.2、TLS 1.1 和 TLS 1.0（按此顺序）。 对 TLS 1.0 的支持仅为向后兼容性提供。 请检查 IoT 中心内的 TLS 支持，以了解如何将中心配置为使用可提供最高安全性的 TLS 1.2。

### <a name="device-communication-patterns"></a>设备通信模式

通常，IoT 设备将来自传感器的遥测数据发送到云中的后端服务。 但是，也可以实现其他类型的通信，例如，在后端服务中将命令发送到设备。 不同类型的通信的一些示例包括： 

*  冷藏货车每隔 5 分钟向 IoT 中心发送温度数据
*  后端服务向设备发送一条命令，以更改设备发送遥测数据的频率来帮助诊断问题
*  监视化学工厂中间歇式反应器的设备在温度超出特定值时发送警报

### <a name="device-telemetry"></a>设备遥测

从设备接收的遥测数据示例包括传感器数据（例如速度或温度）、错误消息（例如丢失事件），或者指示设备处于正常状态的信息性消息。 IoT 设备将事件发送到应用程序，以获得见解。 应用程序可能需要特定的事件子集在不同的终结点进行处理或存储。

### <a name="device-properties"></a>设备属性

可以从 IoT 中心读取或设置属性；完成某个操作后，可以使用属性发送通知。 例如，温度就是设备上的一个特定属性。 此属性可能是可在设备上更新的可写属性，也可能是可从连接到设备的温度传感器读取的属性。 

可以使用[设备孪生](iot-hub-devguide-device-twins.md)或[即插即用](../iot-develop/overview-iot-plug-and-play.md)在 IoT 中心启用属性。

若要详细了解设备孪生与即插即用之间的差异，请参阅[即插即用](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins)。

### <a name="device-commands"></a>设备命令

重新启动设备就是一条命令。 IoT 中心通过允许你在设备上调用直接方法来实现命令。 [直接方法](iot-hub-devguide-direct-methods.md)表示与设备之间的请求-回复式交互，与 HTTP 调用的类似之处在于，它们也会立即成功或失败（在用户指定的超时后）。 此方法用于即时操作过程不同的情况，即时操作的不同取决于设备能否响应。

### <a name="act-on-device-data"></a>处理设备数据

IoT 中心可让你通过其他 Azure 服务来解锁设备数据的价值，使你可以改为预测性地解决问题，而不是进行被动式管理。 将 IoT 中心连接到其他 Azure 服务可以运行机器学习、分析和 AI 来处理实时数据，优化处理，并获得更深入的见解。

#### <a name="built-in-endpoint-collects-device-data-by-default"></a>内置终结点默认收集设备数据

内置终结点默认从你的设备收集数据。 数据是使用请求-响应模式通过专用 IoT 设备终结点收集的，最长可用时间为七天，并且可用于在设备上执行操作。 下面是设备终结点接受的数据：

*    发送设备到云的消息。
*    接收云到设备的消息。
*    启动文件上传。
*    检索并更新设备孪生的属性。
*    接收直接方法请求。

有关 IoT 中心终结点的详细信息，请参阅 [IoT 中心开发指南 - 终结点](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints)

#### <a name="message-routing-sends-data-to-other-endpoints"></a>消息路由将数据发送到其他终结点

还可以将数据路由到不同的服务以供进一步处理。 随着 IoT 解决方案的横向扩展，设备数量、事件量、事件多样性以及不同的服务也会有变化。 要满足此模式的需求，有必要通过灵活、可缩放、一致且可靠的方法来路由事件。 创建消息路由后，数据将停止流向内置终结点，除非已配置回退路由。 有关介绍多种消息路由用法的教程，请参阅[路由教程](tutorial-routing.md)。

IoT 中心还与事件网格集成，使你能够将数据扇出到多个订阅者。 事件网格是一个完全托管的事件服务，可用于跨许多不同的 Azure 服务和应用程序轻松管理事件。 其设计考虑到了性能和可伸缩性，简化了构建事件驱动式应用程序和无服务器体系结构的过程。 [消息路由和事件网格的比较](iot-hub-event-grid-routing-comparison.md)中说明了消息路由与使用事件网格之间的差别

## <a name="next-steps"></a>后续步骤

若要尝试端对端的 IoT 解决方案，请参阅 IoT 中心快速入门：

* [快速入门：将遥测数据从设备发送到 IoT 中心](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)

若要详细了解如何通过 Azure IoT 构建和部署 IoT 解决方案，请访问：

* [基础知识：Azure IoT 技术和解决方案](../iot-fundamentals/iot-services-and-technologies.md)。