---
title: Azure IoT 中心从云到设备选项 | Microsoft Docs
description: 开发人员指南 - 指导用户何时使用直接方法、设备孪生的所需属性或云到设备的消息，以进行从云到设备的通信。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 105374dc214ad77978973a5e7288ac1dd487941b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728797"
---
# <a name="cloud-to-device-communications-guidance"></a>从云到设备通信指南

IoT 中心提供三个选项，允许设备应用向后端应用公开功能：

* [直接方法](iot-hub-devguide-direct-methods.md)，适用于需要立即确认结果的通信。 直接方法通常用于以交互方式控制设备，例如打开风扇。

* [孪生的所需属性](iot-hub-devguide-device-twins.md)，适用于旨在将设备置于某个所需状态的长时间运行命令。 例如，将遥测发送间隔设置为 30 分钟。

* [云到设备消息](iot-hub-devguide-messages-c2d.md)，适用于向设备应用提供单向通知。

要了解 [Azure IoT 即插即用](../iot-develop/overview-iot-plug-and-play.md)如何使用这些选项控制 IoT 即插即用设备，请参阅 [IoT 即插即用服务开发人员指南](../iot-develop/concepts-developer-guide-service.md)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

下面详细比较了各种从云到设备的通信选项。

| 类别 | 直接方法 | 克隆的所需属性 | 云到设备的消息 |
| ---------- | -------------- | ------------------------- | ------------------------ |
| 方案 | 需要立即确认的命令，例如打开风扇。 | 旨在将设备置于某个所需状态的长时间运行命令。 例如，将遥测发送间隔设置为 30 分钟。 | 提供给设备应用的单向通知。 |
| 数据流 | 双向。 设备应用可以立即响应方法。 解决方案后端根据上下文接收请求结果。 | 单向。 设备应用接收更改了属性的通知。 | 单向。 设备应用接收消息
| 持续性 | 不联系已断开连接的设备。 通知解决方案后端：设备未连接。 | 设备孪生会保留属性值。 设备会在下次重新连接时读取属性值。 属性值可通过 [IoT 中心查询语言](iot-hub-devguide-query-language.md)检索。 | IoT 中心可保留消息长达 48 小时。 |
| 目标 | 使用 **deviceId** 与单个设备通信，或使用 [作业](iot-hub-devguide-jobs.md)与多个设备通信。 | 使用 **deviceId** 与单个设备通信，或使用 [作业](iot-hub-devguide-jobs.md)与多个设备通信。 | 通过 **deviceId** 与单个设备通信。 |
| 大小 | 直接方法有效负载的最大大小为 128 KB。 | 所需属性大小最大为 32 KB。 | 最多 64 KB 消息。 |
| 频率 | 高。 有关详细信息，请参阅 [IoT 中心限制](iot-hub-devguide-quotas-throttling.md)。 | 中。 有关详细信息，请参阅 [IoT 中心限制](iot-hub-devguide-quotas-throttling.md)。 | 低。 有关详细信息，请参阅 [IoT 中心限制](iot-hub-devguide-quotas-throttling.md)。 |
| 协议 | 使用 MQTT 或 AMQP 时可用。 | 使用 MQTT 或 AMQP 时可用。 | 在所有协议上可用。 使用 HTTPS 时，设备必须轮询。 |

在以下教程中学习如何使用直接方法、所需属性以及从云到设备的消息：

* [使用直接方法](quickstart-control-device.md)
* [使用所需属性配置设备](tutorial-device-twins.md) 
* [发送云到设备的消息](iot-hub-node-node-c2d.md)
