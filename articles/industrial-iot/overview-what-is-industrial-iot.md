---
title: Azure 工业 IoT 概述
description: 本文提供了工业 IoT 的概述。 它介绍了 IIoT 中的车间连接和安全组件。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787420"
---
# <a name="what-is-industrial-iot-iiot"></a>什么是工业 IoT (IIoT)？

IIoT（工业物联网）通过在制造业中应用 IoT，提高了行业效率。

![工业 IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>提高工业效率
通过 Azure 工业 IoT 提高运营效率和盈利率。 减少访问现场资产的耗时流程。 在云端连接并监视工业设施和设备，包括工厂车间中已在运转的机器。 分析 IoT 数据，从而获取有助于提高整个现场的绩效的见解。

## <a name="industrial-iot-components"></a>工业 IoT 组件

**IoT 边Edge 设备** IoT Edge 设备由边缘运行时和边缘模块组成。 
- *边缘模块* 是 docker 容器，而这些容器又是最小的计算单位，如 OPC 发布服务器和 OPC 孪生。 
- *边缘设备* 用于部署此类模块，这些模块充当 OPC UA 服务器与云中的 IoT 中心之间的媒介。 可在[此处](https://azure.microsoft.com/services/iot-edge/)了解有关 IoT Edge 的更多信息。

**IoT 中心** IoT 中心充当中央消息中心，负责在 IoT 应用程序及其管理的设备之间进行双向通信。 它是一个开放且灵活的云平台即服务，支持开源 SDK 和多种协议。 有关 IoT 中心的更多信息，请参阅[此处](https://azure.microsoft.com/services/iot-hub/)。

**工业边缘模块**
- *OPC 发布服务器*：OPC 发布服务器在 IoT Edge 中运行。 OPC 发布服务器连接到 OPC UA 服务器，并将来自这些服务器的 JSON 编码遥测数据发送到 Azure IoT 中心（以 OPC UA“Pub/Sub”格式）。 可以使用 Azure IoT 中心客户端 SDK 支持的所有传输协议，即 HTTPS、AMQP 和 MQTT。
- *OPC 孪生*：PC 孪生由使用 Azure IoT Edge 和 IoT 中心连接云与工厂网络的微服务构成。 OPC 孪生通过 REST API 提供发现、注册和远程控制工业设备的功能。 OPC 克隆不需要 OPC 统一体系结构 (OPC UA) SDK。 OPC 克隆对编程语言不可知，可包含在无服务器工作流中。
- *发现*：发现模块（由发现器标识表示）在边缘提供发现服务，其中包括 OPC UA 服务器发现。 如果已配置并已启用发现，则发现模块将通过 IoT Edge 和 IoT 中心遥测路径向载入服务发送扫描探测的结果。 服务处理结果，并更新注册表中所有相关的标识。


**通过 Azure 发现、注册和管理你的工业资产** 使用 Azure 工业 IoT，工厂操作员可以发现工厂网络中已启用 OPC UA 的服务器，并将这些服务器注册到 Azure IoT 中心。 操作人员可以从世界各地的任何地点订阅和响应工厂的事件。 微服务的 REST API 将镜像 OPC UA 服务边缘。 使用 Azure Active Directory (AAD) 支持的 OAUTH 身份验证和授权来保护这些服务。 这使你的云应用程序能够浏览服务器地址空间或读/写变量，并使用 HTTPS 和简单的 OPC UA JSON 负载来执行各种方法。

## <a name="next-steps"></a>后续步骤
现在你已了解什么是工业 IoT，可以阅读有关工业 IoT 平台和 OPC 发布服务器的内容：

> [!div class="nextstepaction"]
> [什么是工业 IoT 平台？](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [什么是 OPC 发布服务器？](overview-what-is-opc-publisher.md)