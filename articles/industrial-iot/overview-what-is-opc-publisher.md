---
title: Microsoft OPC 发布服务器
description: 本文概述了 OPC 发布服务器边缘模块。
author: v-condav
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 6df39c93e9bcfca522ac61a863c87269216cc592
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816163"
---
# <a name="what-is-the-opc-publisher"></a>什么是 OPC 发布服务器？

OPC 发布服务器是一种完全受支持的 Microsoft 产品，可弥补工业资产与 Microsoft Azure 云之间的差距。 它通过将启用了 OPC UA 的资产或工业连接软件连接到 Microsoft Azure 云来实现此目的。 它将收集到的遥测数据以各种格式发布到 Azure IoT 中心，包括 IEC62541 OPC UA PubSub 标准格式（从版本 2.6 开始）。 OPC 发布服务器在 Azure IoT Edge 上作为模块运行，在纯 Docker 上作为容器运行。 由于 OPC 发布服务器利用 .NET 跨平台运行时，因此在 Linux 和 Windows 10 上都会以本地方式运行。

OPC 发布服务器是一种参考实现，演示了如何：

- 连接到现有的 OPC UA 服务器。
- 使用 JSON 有效负载将 OPC UA 服务器中采用 OPC UA Pub/Sub 格式的 JSON 编码遥测数据发布到 Azure IoT 中心。

可以使用 Azure IoT 中心客户端 SDK 支持的任何传输协议，例如 HTTPS、AMQP 和 MQTT。

参考实现包括以下内容。

- 用于连接到网络上现有 OPC UA 服务器的 OPC UA 客户端。
- 端口 62222 上的 OPC UA *服务器*，你可以使用它来管理已发布的内容，并提供 IoT 中心直接方法来执行同样的操作。

可以从 GitHub 下载 [OPC 发布服务器参考实现](https://github.com/Azure/iot-edge-opc-publisher)。

该应用程序使用 .NET Core 技术实现，可以在 .NET Core 支持的任何平台上运行。

## <a name="what-does-the-opc-publisher-do"></a>OPC 发布服务器有什么作用？

OPC 发布服务器实现重试逻辑以建立与不响应一定数量的保持活动状态请求的终结点的连接。 例如，如果 OPC UA 服务器因断电而停止响应。

对于 OPC UA 服务器的每个不同的发布时间间隔，应用程序将创建一个单独的订阅，将通过该订阅更新采用此发布时间间隔的所有节点。

OPC 发布服务器支持批处理发送到 IoT 中心的数据，以减少网络负载。 仅当达到配置的数据包大小时，此批处理才会向 IoT 中心发送数据包。

此应用程序使用 OPC 基金会 OPC UA 参考堆栈作为 NuGet 包。 有关许可条款，请参阅 [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/)。

## <a name="next-steps"></a>后续步骤
现在你已了解了 OPC 发布服务器，接下来可以通过部署 OPC 发布服务器来进行入门学习：

> [!div class="nextstepaction"]
> [在独立模式下部署 OPC 发布服务器](tutorial-publisher-deploy-opc-publisher-standalone.md)
