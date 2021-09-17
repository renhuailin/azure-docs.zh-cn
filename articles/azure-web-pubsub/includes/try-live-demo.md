---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/08/2021
ms.openlocfilehash: 1cd52fc7930407317d426b3e2615202dce37e860
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751175"
---
## <a name="get-the-client-url-with-a-temp-access-token"></a>使用临时访问令牌获取客户端 URL

Azure 门户提供了一个简单的客户端 URL 生成器来生成用于快速测试/验证的临时 URL。 让我们使用此工具获取临时客户端访问 URL 并连接到实例。

- 转到 Azure 门户并找出 Azure Web PubSub 实例。
- 转到“`Key`”边栏选项卡中的“`Client URL Generator`”。 
- 设置正确的“`Roles`”：“发送到组”和“加入/退出组” 
- 生成并复制 `Client Access URL`。 

:::image type="content" source="../media/quickstart-live-demo/generate-client-url.png" alt-text="生成客户端 URL 的屏幕截图。":::

## <a name="try-the-instance-with-an-online-demo"></a>通过联机演示试用实例

通过此实时演示，可以轻松地加入或退出组，以及将消息发送给组成员：

> [!div class="nextstepaction"]
> [打开演示](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)

> [!NOTE]
>  **客户端访问 URL** 是门户中提供的一种便利工具，用于简化入门体验，还可以使用此客户端访问 URL 执行一些快速连接测试。 为了编写你自己的应用程序，我们提供了 4 种语言的 SDK 来帮助你生成 URL。 

- 尝试加入不同的组，将消息发送给不同的组，再查看收到哪些消息。 例如：
    - 让两个客户加入到同一组。 你将看到该消息可以广播到组成员。 
    - 让两个客户加入到不同组。 如果客户不是组成员，则会看到该客户无法接收消息。 
- 还可以在生成 `Client Access URL` 时尝试取消选中“`Roles`”，以查看加入组或将消息发送到组时会发生的情况。 例如：
    - 取消选中 `Send to Groups` 权限。 你会看到客户端无法向组发送消息。 
    - 取消选中 `Join/Leave Groups` 权限。 你会看到客户无法加入组。 
