---
title: 在不使用 Azure IoT SDK 的情况下进行开发| Microsoft Docs
description: 开发人员指南 - 关于主题的信息和到主题的链接，你可以使用它们构建设备应用和后端应用，而无需使用 Azure IoT SDK。
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 34476ee99dcf455886a72d3fcef2356ab4e6229f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721924"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>不使用 Azure IoT 中心 SDK 进行开发

本主题为希望不使用 Azure IoT SDK 开发设备或后端应用的开发人员提供有用的信息和链接。

Microsoft 强烈建议使用 Azure IoT SDK。 Azure IoT 设备和服务 SDK 在许多流行平台上发布。 SDK 提供了一个方便层，可以处理底层通信协议的许多复杂问题，包括设备连接和重新连接，以及重试策略。 SDK 会定期更新，以提供 IoT 中心公开的最新功能以及安全更新。 使用 SDK 可以帮助你减少开发时间和用于代码维护的时间。 若要了解有关 Azure IoT SDK 的更多信息，请参阅 [Azure IoT设备和服务 SDK](iot-hub-devguide-sdks.md)。 有关使用 Azure IoT SDK 的优势的详细信息，请参阅[使用 Azure IoT SDK 的优势以及如果不使用需要避免的陷阱](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)博客文章。

尽管 IoT 中心支持 AMQP、AMQP over WebSockets、HTTPS、MQTT 和 MQTT over WebSockets 来与设备进行通信，但如果你的设备支持 MQTT，我们建议你使用它。

## <a name="development-prerequisites"></a>开发必备组件

在开始开发之前，你应该对 IoT 中心和想要设备或后端应用实现的功能有透彻的了解。 以下是你应该熟悉的主题的简短列表：

* 确保你了解 IoT 中心公开的终结点以及每个终结点上支持的协议。 若要了解详细信息，请参阅 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。

* 在设备应用涉及协议选择的地方，我们强烈建议你使用 MQTT。 但是，在选择协议之前，请确保你了解每个协议所强加的限制。 若要了解详细信息，请参阅[选择通信协议](iot-hub-devguide-protocols.md)。

* 若要了解使用 IoT 中心的身份验证，请参阅[控制对 IoT 中心的访问权限](iot-hub-devguide-security.md)。

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>有关不同协议的帮助

有关在不使用 Azure IoT SDK 的情况下使用以下协议的帮助：

* **AMQP** 上的设备或后端应用，请参阅 [AMQP 支持](iot-hub-amqp-support.md)。

* **MQTT** 上的设备应用，请参阅 [MQTT 支持](iot-hub-mqtt-support.md)。 本主题中大部分都直接使用 MQTT 协议。 还包含有关使用 [IoT MQTT 示例存储库](https://github.com/Azure-Samples/IoTMQTTSample)的信息。 该存储库包含使用 Eclipse Mosquitto 库将消息发送到 IoT 中心的 C 示例。

* **HTTPS** 上的设备或后端应用，请查阅 [Azure IoT 中心 REST API](/rest/api/iothub/)。 请注意，如[开发必备组件](#development-prerequisites)中所述，你不能将 X.509 证书颁发机构 (CA) 身份验证和 HTTPS 一起使用。

对于设备，我们强烈建议使用 MQTT （如果设备支持）。

## <a name="next-steps"></a>后续步骤

* [MQTT 支持](iot-hub-mqtt-support.md)