---
title: Azure Web PubSub 服务实时演示
description: 有关开始使用 Azure Web PubSub 服务实时演示的快速入门。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: a516d215a971b2a6cb54d4e73305cc69ce0edf8c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166854"
---
# <a name="quickstart-get-started-with-live-demo"></a>快速入门：实时演示入门

Azure Web PubSub 服务可帮助你轻松地使用 Websocket 和发布-订阅模式生成实时消息传递 Web 应用程序。 在本快速入门中，了解如何轻松地开始使用实时演示。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="get-started-with-the-live-demo"></a>实时演示入门

### <a name="get-client-url-with-a-temp-access-token"></a>使用临时访问令牌获取客户端 URL

第一步，需要从 Azure Web PubSub 实例获取客户端 URL。 

- 转到 Azure 门户并找出 Azure Web PubSub 实例。
- 转到“`Key`”边栏选项卡中的“`Client URL Generator`”。 
- 设置正确的“`Roles`”：“发送到组”和“加入/退出组” 
- 生成并复制 `Client Access URL`。 

### <a name="try-the-live-demo"></a>尝试实时演示 

通过此实时演示，可以轻松地加入或退出组，以及将消息发送给组成员。 

- 打开[客户端发布/订阅演示](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)，粘贴 `Client Access URL` 并进行连接。 
- 尝试加入不同的组，将消息发送给不同的组，再查看收到哪些消息。
- 还可以在生成 `Client Access URL` 时尝试取消选中“`Roles`”，以查看加入/退出组或将消息发送到组时会发生的情况。