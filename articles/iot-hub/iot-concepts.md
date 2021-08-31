---
title: 新 Azure IoT 中心用户需了解的基本概念 | Microsoft Docs
description: 本文介绍新 Azure IoT 中心用户需了解的基本概念
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: 28c91ee86a2657e5483d6d0287be8d42bd1fac23
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725305"
---
# <a name="iot-concepts"></a>IoT 概念

IoT 中心是托管在云中的托管服务，充当中央消息中心，用于 IoT 应用程序及其连接设备之间双向通信。 可以安全可靠地连接数百万台设备及其后端解决方案。 几乎任何设备都可以连接到 IoT 中心。 

支持多种消息传递模式，包括“设备到云”遥测、从设备上传文件，以及用于从云中控制设备的请求-答复方法。 IoT 中心还支持监视，以帮助跟踪创建设备、连接设备和设备故障。

借助 IoT 中心的功能，你可以生成可缩放且功能完整的 IoT 解决方案，例如管理制造业中使用的工业设备、跟踪医疗保健中宝贵的资产以及监视办公大楼使用情况。

相比于其他客户端（例如浏览器和移动应用），IoT 设备有不同的特征。 [设备 SDK](iot-hub-devguide-sdks.md) 可帮助解决以安全可靠的方式将设备连接到后端服务的难题。 

具体而言，IoT 设备具有以下特征：

- 通常是无人操作的嵌入式系统（与电话不同）。
- 可以部署到物理访问开销十分昂贵的远程位置。
- 可能只能通过解决方案后端来访问。
- 能力和处理资源可能都有限。
- 网络连接可能不稳定、缓慢或昂贵。
- 可能需要使用专属、自定义或行业特定的应用程序协议。

## <a name="securely-connect-and-communicate"></a>安全连接和通信

通过对每个设备进行身份验证，可以安全地将每个设备连接到 IoT 中心，并且安全地管理这些设备。 可以完全控制设备访问，并可以根据每个设备的级别控制连接。

### <a name="devices-have-a-secure-identity"></a>设备具有安全标识

每个 IoT 中心都有一个标识注册表，存储允许连接到 IoT 中心的设备和模块的相关信息。 IoT 中心的标识注册表中必须先有设备或模块的条目，然后该设备或模块才能连接到 IoT 中心。 设备或模块还必须基于标识注册表中存储的凭据向 IoT 中心进行身份验证。

我们支持通过两种方法在设备与 IoT 中心之间进行身份验证。 一种方法是使用基于 SAS 令牌的身份验证。 支持的另一种方法是使用 X.509 证书身份验证。

基于 SAS 的令牌方法通过将对称密钥关联到每个调用，为设备向 IoT 中心发出的每个调用提供身份验证。 使用基于 X.509 的身份验证可以在建立传输层安全性 (TLS) 标准连接的过程中，对物理层上的 IoT 设备进行身份验证。 基于安全令牌的方式可在没有 X.509 身份验证的情况下使用，但这种模式的安全性较低。 这两种方式的选择主要取决于设备验证需要达到的安全程度和设备上安全储存的可用性（以安全存储私钥）。

Azure IoT SDK 无需任何特殊配置即可自动生成令牌。 如果不使用该 SDK，则必须生成安全令牌。

可以使用 [IoT 中心设备预配服务](../iot-dps/index.yml)来一次设置并预配许多设备。

### <a name="devices-can-securely-communicate-with-an-iot-hub"></a>设备可以安全地与 IoT 中心通信

选择身份验证方法后，将使用传输层安全性 (TLS) 标准来保护 IoT 设备与 IoT 中心之间的 Internet 连接。 Azure IoT 支持 TLS 1.2、TLS 1.1 和 TLS 1.0（按此顺序）。 对 TLS 1.0 的支持仅为向后兼容性提供。 请检查 IoT 中心内的 TLS 支持，以了解如何将中心配置为使用可提供最高安全性的 TLS 1.2。

## <a name="communication-patterns-with-a-device"></a>与设备的通信模式

通常，IoT 设备将来自传感器的遥测数据发送到云中的后端服务。 但是，也可以实现其他类型的通信，例如，在后端服务中将命令发送到设备。 不同类型的通信的一些示例包括： 

*  冷藏货车每隔 5 分钟向 IoT 中心发送温度数据
*  后端服务向设备发送一条命令，以更改设备发送遥测数据的频率来帮助诊断问题
*  监视化学工厂中间歇式反应器的设备在温度超出特定值时发送警报

### <a name="telemetry-is-data-emitted-by-a-device"></a>遥测数据是设备发出的数据 

从设备接收的遥测数据示例包括传感器数据（例如速度或温度）、错误消息（例如丢失事件），或者指示设备处于正常状态的信息性消息。 IoT 设备将事件（通知、确认、遥测）发送到应用程序以获取见解。 应用程序可能需要特定的事件子集在不同的终结点进行处理或存储。

### <a name="properties-are-state-values-or-data-that-applications-can-access"></a>属性是指应用程序可以访问的状态值或数据。 

例如，设备的当前固件版本或可更新的可写属性（例如温度）都是属性。 可以从 IoT 中心读取或设置属性；完成某个操作后，可以使用属性发送通知。 例如，温度就是设备上的一个特定属性。 此属性可能是可在设备上更新的可写属性，也可能是可从连接到设备的温度传感器读取的属性。 

可以使用[设备孪生](iot-hub-devguide-device-twins.md)或[即插即用](../iot-develop/overview-iot-plug-and-play.md)在 IoT 中心启用属性。

    
若要详细了解设备孪生与即插即用之间的差异，请参阅[即插即用](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins)。

### <a name="commands-can-be-used-to-execute-methods-directly-on-connected-devices"></a>命令可用于直接在连接的设备上执行方法。 

例如，重新启动设备就是一条命令。 IoT 中心通过允许你从云中在设备上调用直接方法来实现命令。 [直接方法](iot-hub-devguide-direct-methods.md)表示与设备之间的请求-回复式交互，与 HTTP 调用的类似之处在于，它们也会立即成功或失败（在用户指定的超时后）。 此方法用于即时操作过程不同的情况，即时操作的不同取决于设备能否响应。

## <a name="view-and-act-on-data-collected-from-your-devices"></a>查看和处理从设备收集的数据

IoT 中心可让你通过其他 Azure 服务来解锁设备数据的价值，使你可以改为预测性地解决问题，而不是进行被动式管理。 将 IoT 中心连接到其他 Azure 服务可以运行机器学习、分析和 AI 来处理实时数据，优化处理，并获得更深入的见解。

### <a name="built-in-endpoint-collects-data-from-your-devices-by-default"></a>内置终结点默认从你的设备收集数据

内置终结点默认从你的设备收集数据。 数据是使用请求-响应模式通过专用 IoT 设备终结点收集的，最长可使用七天，并且可用于在设备上执行操作。 下面是设备终结点接受的数据：

*    发送设备到云的消息。 设备使用此终结点发送设备到云的消息。

*    接收云到设备的消息。 设备使用此终结点接收目标云到设备的消息。

*    启动文件上传。 设备使用此终结点接收来自 IoT 中心的 Azure 存储 SAS URI，以便上传文件。

*    检索并更新设备孪生的属性。 设备使用此终结点访问其设备孪生的属性。 

*    接收直接方法请求。 设备使用此终结点侦听直接方法的请求。 

有关 IoT 中心终结点的详细信息，请参阅 [IoT 中心开发指南 - 终结点](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints)

### <a name="use-message-routing-to-send-data-to-other-endpoints-for-processing"></a>使用消息路由将数据发送到其他终结点进行处理

还可以将数据路由到不同的服务以供进一步处理。 随着 IoT 解决方案的横向扩展，设备数量、事件量、事件多样性以及不同的服务也会有变化。 要满足此模式的需求，有必要通过灵活、可缩放、一致且可靠的方法来路由事件。 还可以筛选要发送到不同服务的数据。 创建消息路由后，数据将停止流向内置终结点，除非已配置回退路由。 有关介绍多种消息路由用法的教程，请参阅[路由教程](tutorial-routing.md)。

IoT 中心还与事件网格集成，使你能够将数据扇出到多个订阅者。 事件网格是一个完全托管的事件服务，可用于跨许多不同的 Azure 服务和应用程序轻松管理事件。 其设计考虑到了性能和可伸缩性，简化了构建事件驱动式应用程序和无服务器体系结构的过程。 详细了解事件网格。 [消息路由和事件网格的比较](iot-hub-event-grid-routing-comparison.md)中说明了消息路由与使用事件网格之间的差别