---
title: Azure Web PubSub 服务常见问题解答
description: 获取有关 Azure Web PubSub 服务的常见问题的解答。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: bcecb9b50c493ba85172fd56ccf3f2d286aa1839
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431307"
---
# <a name="azure-web-pubsub-service-faq"></a>Azure Web PubSub 服务常见问题解答

这是 Azure Web PubSub 服务的常见问题解答。 

## <a name="is-azure-web-pubsub-service-ready-for-production-use"></a>Azure Web PubSub 服务是已可用于生产？

Azure Web PubSub 服务目前为公共预览版，不提供 SLA 方面的承诺。 

## <a name="when-will-azure-web-pubsub-service-be-ready-for-production-use"></a>Azure Web PubSub 服务何时可用于生产？

产品团队正在积极开发 Azure Web PubSub 服务正式版。 正式发布后，该服务将支持：
- Azure Active Directory 支持
- API 管理集成
- 二进制 Pub/Sub WebSocket 子协议
- Azure 资源日志集成

如需正式版功能的更多详细信息，请联系产品团队。

## <a name="how-do-i-choose-between-azure-signalr-service-and-azure-web-pubsub-service"></a>如何在 Azure SignalR 服务与 Azure Web PubSub 服务之间进行选择？

[Azure SignalR 服务](https://azure.microsoft.com/services/signalr-service)和 [Azure Web PubSub 服务](https://azure.microsoft.com/services/web-pubsub)都可帮助客户轻松生成大规模且高度可用的实时 Web 应用程序，使客户能够将工作重心放在业务逻辑上，而无需管理消息传递基础结构。 一般而言，如果你已使用 SignalR 库生成了实时应用程序，则可以选择 Azure SignalR 服务。 如果你正在寻求一种通用解决方案来基于 WebSocket 和发布-订阅模式生成实时应用程序，则可以选择 Azure Web PubSub 服务。 Azure Web PubSub 服务不能替代 Azure SignalR 服务。 它们面向不同的方案。

对于以下情况，更适合使用 Azure SignalR 服务：  

- 你已使用 ASP.NET 或 ASP.NET Core SignalR（主要是使用 .NET），或者需要与 .NET 生态系统（例如 Blazor）相集成。
- 有一个适用于你的平台的 SignalR 客户端。 
- 需要一个已确立的且支持多种调用模式（RPC 和流式处理）和传输（WebSocket、服务器发送的事件和长时间轮询）的协议，以及一个可以代你管理连接生存期的客户端。 

对于以下情况，更适合使用 Azure Web PubSub 服务：  

- 你需要基于 WebSocket 技术或者基于 WebSocket 的发布-订阅模式生成实时应用程序。
- 你需要生成自己的子协议，或使用基于 WebSocket 的现有高级协议（例如，基于 WebSocket 的 MQTT 或 AMQP）。 
- 你正在寻求一台轻型服务器，例如，在不通过配置的后端的情况下将消息发送到客户端。  

##  <a name="where-does-my-data-reside"></a>我的数据存放在什么位置？

Azure Web PubSub 服务以数据处理器服务的形式运行。 它不会存储任何客户内容，且设计包含数据驻留。 如果将 Azure Web PubSub 服务与其他 Azure 服务（例如 Azure 存储）结合来进行诊断，请查看[此白皮书](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)，获取有关如何将数据驻留在 Azure 区域中的指南。
