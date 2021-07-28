---
title: Azure Web PubSub 服务实时演示
description: 有关开始使用 Azure Web PubSub 服务实时演示的快速入门。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: e35ef66c038748e31ba218a56adb8111374d2339
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219587"
---
# <a name="quickstart-get-started-with-chatroom-live-demo"></a>快速入门：通过聊天室实时演示入门

Azure Web PubSub 服务可帮助你轻松地使用 Websocket 和发布-订阅模式生成实时消息传递 Web 应用程序。 [聊天室实时演示](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)演示了 Azure Web PubSub 提供的实时消息传递功能。 使用此实时演示，可以轻松地加入聊天组，并向特定组发送实时消息。 

:::image type="content" source="media/quickstart-live-demo/chat-live-demo.gif" alt-text="使用聊天室实时演示。":::

在本快速入门中，了解如何轻松地开始使用实时演示。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="get-started-with-the-chatroom-live-demo"></a>通过聊天室实时演示入门

### <a name="get-client-url-with-a-temp-access-token"></a>使用临时访问令牌获取客户端 URL

第一步，需要从 Azure Web PubSub 实例获取客户端 URL。 

- 转到 Azure 门户并找出 Azure Web PubSub 实例。
- 转到“`Key`”边栏选项卡中的“`Client URL Generator`”。 
- 设置正确的“`Roles`”：“发送到组”和“加入/退出组” 
- 生成并复制 `Client Access URL`。 

:::image type="content" source="media/quickstart-live-demo/generate-client-url.png" alt-text="生成客户端 URL 的屏幕截图。":::

### <a name="try-the-live-demo"></a>尝试实时演示 

通过此实时演示，可以轻松地加入或退出组，以及将消息发送给组成员。 

- 打开[聊天室实时演示](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)，粘贴 `Client Access URL` 并连接。 

:::image type="content" source="media/quickstart-live-demo/paste-client-access-url.png" alt-text="通过实时演示粘贴客户端 URL 的屏幕截图。":::

> [!NOTE]
>  **客户端访问 URL** 是门户中提供的一种便利工具，用于简化入门体验，还可以使用此客户端访问 URL 执行一些快速连接测试。 为了编写你自己的应用程序，我们提供了 4 种语言的 SDK 来帮助你生成 URL。 

- 尝试加入不同的组，将消息发送给不同的组，再查看收到哪些消息。 例如：
    - 让两个客户加入到同一组。 你将看到该消息可以广播到组成员。 
    - 让两个客户加入到不同组。 如果客户不是组成员，则会看到该客户无法接收消息。 
- 还可以在生成 `Client Access URL` 时尝试取消选中“`Roles`”，以查看加入组或将消息发送到组时会发生的情况。 例如：
    - 取消选中 `Send to Groups` 权限。 你会看到客户端无法向组发送消息。 
    - 取消选中 `Join/Leave Groups` 权限。 你会看到客户无法加入组。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已通过聊天室实时演示学习了实时消息传递功能。 现在，可以开始构建自己的应用程序。 

> [!div class="nextstepaction"]
> [快速入门：在 Azure Web PubSub 中发布和订阅消息](https://azure.github.io/azure-webpubsub/getting-started/publish-messages/js-publish-message)

> [!div class="nextstepaction"]
> [快速入门：使用 Azure Web PubSub 创建简单的聊天室](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [快速入门：使用 Azure Functions 和 Azure Web PubSub 服务创建简单的无服务器聊天应用程序](./quickstart-serverless.md)

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://github.com/Azure/azure-webpubsub/tree/main/samples)