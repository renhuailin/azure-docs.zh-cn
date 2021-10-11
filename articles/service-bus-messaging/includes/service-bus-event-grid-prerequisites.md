---
title: include 文件
description: include 文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/29/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0b60d1c84ca5b88f0a97d23397a25c3b9f9a5053
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273133"
---
## <a name="prerequisites"></a>先决条件
如果还没有 [Azure 订阅](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间
请遵照以下教程中的说明：[快速入门：使用 Azure 门户创建服务总线主题和主题的订阅](../service-bus-quickstart-topics-subscriptions-portal.md)来执行以下任务：

- 创建一个 **高级** 服务总线命名空间。 
- 获取连接字符串。 
- 创建服务总线主题。
- 创建主题的订阅。 本教程只需要一个订阅，因此无需创建订阅 S2 和 S3。 

## <a name="send-messages-to-the-service-bus-topic"></a>向服务总线主题发送消息
在此步骤中，你将使用示例应用程序将消息发送到在上一步中创建的服务总线主题。 

1. 克隆 [GitHub azure-service-bus 存储库](https://github.com/Azure/azure-service-bus/)，或者下载 zip 文件并从中提取文件。 
2. 在 Visual Studio 中转到 \samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegrationV2 文件夹，然后打开 SBEventGridIntegration.sln 文件 。
3. 在解决方案资源管理器窗口中，展开“MessageSender”项目，然后选择“Program.cs” 。
4. 将 `<SERVICE BUS NAMESPACE - CONNECTION STRING>` 替换为服务总线命名空间的连接字符串，并将 `<TOPIC NAME>` 替换为主题的名称。 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. 生成并运行程序，以将 5 条测试消息 (`const int numberOfMessages = 5;`) 发送到服务总线主题。 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="控制台应用输出":::
