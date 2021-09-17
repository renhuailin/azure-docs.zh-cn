---
title: 什么是 Azure Web PubSub 服务？
description: 更好地了解使用 Azure Web PubSub 的典型用例方案，并了解 Azure Web PubSub 的主要优势。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 01/27/2021
ms.openlocfilehash: ed48335b0417cacce9a1e408de06c0c2a9ec9b44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751125"
---
# <a name="what-is-azure-web-pubsub-service"></a>什么是 Azure Web PubSub 服务？ 

Azure Web PubSub 服务可帮助你轻松地使用 Websocket 和发布-订阅模式生成实时消息传递 Web 应用程序。 此实时功能允许在服务器和连接的客户端（例如单页 Web 应用程序或移动应用程序）之间发布内容更新。 客户端不需要轮询最新更新，也不需要为更新提交新的 HTTP 请求。

本文简要介绍了 Azure Web PubSub 服务。

## <a name="what-is-azure-web-pubsub-service-used-for"></a>Azure Web PubSub 服务的用途是什么？

任何需要在服务器和客户端或客户端之间进行实时发布-订阅消息传送的方案都可以使用 Azure Web PubSub 服务。 通常需要从服务器轮询或提交 HTTP 请求的传统实时功能也可以使用 Azure Web PubSub 服务。

Azure Web PubSub 服务可用于需要实时内容更新的任何应用程序类型。 下面是适合使用 Azure Web PubSub 服务的一些示例：

* **高频率数据更新：** 游戏、投票、轮询、竞拍。
* **实时仪表板和监视：** 公司仪表板、金融市场数据、即时销量更新、多玩家游戏排行榜和 IoT 监视。
* **跨平台实时聊天：** 实时聊天室、聊天机器人、在线客户支持、实时购物助手、信使、游戏内聊天等。
* **地图实时定位：** 物流跟踪、交货状态跟踪、运输状态更新、GPS 应用。
* **实时定向广告：** 个性化的实时推送广告和套餐、交互式广告。
* **协作式应用：** 共同著作、白板应用和团队会议软件。
* **推送即时通知：** 社交网络、电子邮件、游戏、行程通知。
* **实时广播：** 实时音频/视频广播、实时字幕、翻译、活动/新闻广播。
* **IoT 和互联设备：** 实时 IoT 指标、远程控制、实时状态和位置跟踪。
* **自动化：** 基于上游事件的实时触发器。

## <a name="what-are-the-benefits-using-azure-web-pubsub-service"></a>使用 Azure Web PubSub 服务的优势是什么？

**对大规模客户端连接和高可用性体系结构的内置支持：**

Azure Web PubSub 服务是针对大规模实时应用程序设计的。 该服务支持多个实例配合工作，规模可达数百万个客户端连接。 同时，它还支持多个全局区域，以实现分片、高可用性或灾难恢复目的。

**对各种客户端 SDK 和编程语言的支持：**

Azure Web PubSub 服务适用于广泛的客户端，例如 Web 和移动浏览器、桌面应用、移动应用、服务器进程、IoT 设备和游戏主机。 由于此服务支持使用发布-订阅模式的标准 WebSocket 连接，因此可以轻松地在此服务中使用不同语言的任何标准 WebSocket 客户端 SDK。 

**为不同的消息传送模式提供丰富的 API：**

Azure Web PubSub 服务是一种双向消息传递服务，支持在服务器和客户端之间使用不同的消息传递模式，例如：

* 服务器将消息发送到特定客户端、所有客户端或属于特定用户或已放置在任意组中的客户端子集。 
* 客户端将消息发送到属于任意组的客户端。
* 客户端将消息发送到服务器。


## <a name="how-to-use-the-azure-web-pubsub-service"></a>如何使用 Azure Web PubSub 服务？

可通过多种不同的方式对 Azure Web PubSub 服务编程，下面列出了一些示例：

- **生成无服务器实时应用程序**：使用 Azure Functions 与 Azure Web PubSub 服务的集成，以 JavaScript、C#、Java 和 Python 等语言生成无服务器实时应用程序。 
- **使用 WebSocket 子协议执行仅限客户端的发布/订阅** - Azure Web PubSub 服务提供 WebSocket 子协议，使获得授权的客户端能够方便地发布到其他客户端。
- **使用提供的 SDK 管理自托管应用服务器中的 WebSocket 连接** - Azure Web PubSub 服务提供 C#、JavaScript、Java 和 Python 形式的 SDK 来轻松管理 WebSocket 连接，包括向连接广播消息、向某些组添加连接或关闭连接等。
- **通过 REST API 将消息从服务器发送到客户端**：Azure Web PubSub 服务提供的 REST API 使应用程序能够以任何支持 REST 的编程语言向连接的客户端发送消息。