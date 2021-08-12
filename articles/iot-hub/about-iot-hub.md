---
title: 什么是 Azure IoT 中心 | Microsoft Docs
description: 本文介绍对 Azure IoT 中心的使用。 它使你可以以可缩放的方式读取数据，并可以安全地管理设备。
author: robinsh
ms.author: robinsh
ms.date: 05/03/2021
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
- contentperf:fy21q3
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8e491e6fc8d0eb93a3d9696f68f045191c2da20b
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400896"
---
# <a name="what-is-azure-iot-hub"></a>什么是 Azure IoT 中心

IoT 中心是托管在云中的托管服务，充当中央消息中心，用于 IoT 应用程序及其连接设备之间双向通信。 可以安全可靠地连接数百万台设备及其后端解决方案。 几乎任何设备都可以连接到 IoT 中心。 

支持多种消息传递模式，包括“设备到云”遥测、从设备上传文件，以及用于从云中控制设备的请求-答复方法。 IoT 中心还支持监视，以帮助跟踪创建设备、连接设备和设备故障。

借助 IoT 中心的功能，你可以生成可缩放且功能完整的 IoT 解决方案，例如管理制造业中使用的工业设备、跟踪医疗保健中宝贵的资产以及监视办公大楼使用情况。

## <a name="scale-your-solution"></a>缩放解决方案

IoT 中心可扩展为数百万个同时连接的设备，以及每秒数百万个事件以支持 IoT 工作负载。 有关缩放 IoT 中心的详细信息，请参阅 [IoT 中心缩放](iot-hub-scaling.md)。 若要详细了解 IoT 中心提供的多层服务以及如何最好地满足可伸缩性需求，请查看[定价页](https://azure.microsoft.com/pricing/details/iot-hub/)。

## <a name="secure-your-communications"></a>保护通信

可以使用 IoT 中心安全地发送数据。

* 通过对每个设备进行身份验证，可以安全地将每个设备连接到 IoT 中心，并且安全地管理这些设备。

* 可以完全控制设备访问，并可以根据每个设备的级别控制连接。

* [IoT 中心设备预配服务](../iot-dps/index.yml)在设备首次启动时，自动将设备提供给正确的 IoT 中心。

* 多种身份验证类型支持各种设备功能：

  * 基于 SAS 令牌的身份验证使你可以快速启动 IoT 解决方案。

  * 单个 X.509 证书身份验证可用于安全、以标准为基础的身份验证。

  * X.509 CA 身份验证可用于简单、以标准为基础的注册。

## <a name="route-device-data"></a>路由设备数据

内置的消息路由功能可以灵活地设置基于规则的自动消息扇出：

* [消息路由](iot-hub-devguide-messages-d2c.md)用于控制你的中心发送设备遥测数据的位置。

* 无需支付额外费用，即可将消息路由到多个终结点。

* 传递规则可以配置为根据消息中的内容自动引导消息，而无需编写任何代码。

## <a name="integrate-with-other-services"></a>与其他服务集成

可以将 IoT 中心与其他 Azure 服务集成，生成完整的端对端解决方案。 例如，使用：

* [Azure 事件网格](../event-grid/index.yml)，可以采用可靠、可缩放且安全的方式快速地让你的业务响应关键事件。

* [Azure 逻辑应用](../logic-apps/index.yml)，可以自动执行业务流程。

* [Azure 机器学习](iot-hub-weather-forecast-machine-learning.md)，可为解决方案添加机器学习和 AI 模型。

* [Azure 流分析](../stream-analytics/index.yml)，可从设备对数据流执行实时分析计算。

## <a name="configure-and-control-your-devices"></a>配置和控制设备

可以通过一系列的内置功能，管理连接到 IoT 中心的设备。

* 存储、同步和查询设备元数据和所有设备的状态信息。

* 根据每个设备或设备的共同功能设置设备状态。

* 通过信息路由集成自动响应报告设备的状态更改。

## <a name="make-your-solution-highly-available"></a>使解决方案具有高可用性

IoT 中心具有 [IoT 中心的服务级别协议](https://azure.microsoft.com/support/legal/sla/iot-hub/)的 99.9% 的可用性。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/) 说明了 Azure 作为整体的保证可用性。

## <a name="connect-your-devices"></a>连接数据

使用 [Azure IoT 设备 SDK](./iot-hub-devguide-sdks.md) 库，生成在设备上运行且与 IoT 中心集成的应用程序。 支持的平台包括多个 Linux 分发版、Windows 和实时操作系统。 支持的语言包括：

* C
* Embedded C
* C#
* Java
* Python
* Node.js。

IoT 中心和设备 SDK 支持用于连接设备的以下协议：

* HTTPS
* AMQP
* 基于 WebSockets 的 AMQP
* MQTT
* 基于 WebSocket 的 MQTT

IoT 中心和设备 SDK 支持用于连接设备的 [Azure IoT 即插即用](../iot-develop/overview-iot-plug-and-play.md)约定。 IoT 即插即用设备使用设备模型向支持 IoT 即插即用的应用程序播发其功能。 通过设备模型，解决方案构建者无需进行任何手动配置即可将智能设备集成到解决方案中。

如果解决方案无法使用设备库，则设备可以使用 MQTT v3.1.1、HTTPS 1.1 或 AMQP 1.0 协议，以本地化的方式连接到中心。

如果解决方案无法使用其中一种支持的协议，则可以将 IoT 中心扩展到支持自定义协议：

* 使用 [Azure IoT Edge](../iot-edge/index.yml)，创建现场网关，执行边缘的协议转换。

* 自定义 [Azure IoT 协议网关](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)，在云中执行协议转换。

## <a name="quotas-and-limits"></a>配额和限制

每个 Azure 订阅都有默认配额限制，以防止服务滥用。 这些限制可能影响 IoT 解决方案的作用域。 每个订阅的当前限制是每个订阅 50 个 IoT 中心。 可以通过联系支持人员，请求增加配额。 有关详细信息，请参阅 [IoT 中心配额和限制](iot-hub-devguide-quotas-throttling.md)。 有关配额限制的详细信息，请参阅以下文章之一：

* [Azure 订阅服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)（IoT 中心限制和你）

## <a name="iot-hub-on-azure-stack-hub-preview"></a>Azure Stack Hub 上的 IoT 中心（预览）

Azure Stack Hub 上的 IoT 中心（预览）允许创建混合 IoT 解决方案。 IoT 中心是一项托管服务，充当消息中心，用于在 IoT 应用程序与所管理的设备之间进行双向通信。 可以使用 Azure Stack Hub 上的 IoT 中心，在 IoT 设备和本地解决方案之间建立可靠又安全的通信，生成 IoT 解决方案。

在公共预览版中，Azure Stack Hub 上的 IoT 中心免费。 有关详细信息，请参阅 [Azure Stack Hub 上的 IoT 中心概述](/azure-stack/operator/iot-hub-rp-overview)。

## <a name="next-steps"></a>后续步骤

若要尝试端对端的 IoT 解决方案，请参阅 IoT 中心快速入门：

* [快速入门：将遥测数据从设备发送到 IoT 中心](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)

若要详细了解如何通过 Azure IoT 构建和部署 IoT 解决方案，请访问：

* [基础知识：Azure IoT 技术和解决方案](../iot-fundamentals/iot-services-and-technologies.md)。