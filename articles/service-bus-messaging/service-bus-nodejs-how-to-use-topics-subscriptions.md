---
title: 向 Azure 服务总线主题发送消息，并从该主题的订阅接收消息 (JavaScript)
description: 了解如何编写使用最新版 @azure/service-bus 包的 JavaScript 程序，以便将消息发送到服务总线主题，并从主题的订阅接收消息。
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 13664e2a465ae891666e9c296514dc18a14ed191
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950210"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-javascript"></a>向 Azure 服务总线主题发送消息，并从该主题的订阅接收消息 (JavaScript)
本教程介绍如何使用 JavaScript 程序中的 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 包将消息发送到服务总线主题，并从该主题的服务总线订阅接收消息。

## <a name="prerequisites"></a>先决条件
- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 遵循[快速入门：使用 Azure 门户创建一个服务总线主题和多个对该主题的订阅](service-bus-quickstart-topics-subscriptions-portal.md)。 记下连接字符串、主题名称和订阅名称。 本快速入门仅需使用一个订阅。 

> [!NOTE]
> - 本教程将演练可以使用 [Nodejs](https://nodejs.org/) 复制和运行的示例。 有关如何创建 Node.js 应用程序的说明，请参阅[创建 Node.js 应用程序并将其部署到 Azure 网站](../app-service/quickstart-nodejs.md)或[使用 Windows PowerShell 创建 Node.js 云服务](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用节点包管理器 (NPM) 安装包
若要安装服务总线的 npm 包，请打开路径中包含 `npm` 的命令提示符，将目录更改为要包含示例的文件夹，然后运行此命令

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>将消息发送到主题
下面的示例代码演示如何将一批消息发送到服务总线主题。 请参阅代码注释以了解详细信息。 

1. 打开你喜好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建一个名为 `sendtotopic.js` 的文件，并将下面的代码粘贴到其中。 此代码向主题发送一条消息。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. 将 `<SERVICE BUS NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的连接字符串。
1. 将 `<TOPIC NAME>` 替换为主题名称。 
1. 然后，在命令提示符中运行该命令以执行此文件。

    ```console
    node sendtotopic.js 
    ```
1. 你会看到以下输出。

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息
1. 打开你喜好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建名为 receivefromsubscription.js 的文件，然后将以下代码粘贴到其中。 请参阅代码注释以了解详细信息。 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. 将 `<SERVICE BUS NAMESPACE CONNECTION STRING>` 替换为命名空间的连接字符串。 
1. 将 `<TOPIC NAME>` 替换为主题名称。 
1. 将 `<SUBSCRIPTION NAME>` 替换为主题的订阅名称。 
1. 然后，在命令提示符中运行该命令以执行此文件。

    ```console
    node receivefromsubscription.js
    ```
1. 你会看到以下输出。

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

在 Azure 门户中，导航到服务总线命名空间，然后选择底部窗格中的主题，以查看主题的“服务总线主题”页面。 在此页上，应会在“消息”图表中看到三条传入消息和三条传出消息。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="传入和传出消息":::

如果下次仅运行发送应用，那么在“服务总线主题”页上，除 3 条传出消息外，你还会看到 6 条传入消息（3 条新消息）。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="已更新的主题页":::

在此页上，如果选择一个订阅，则将转到“服务总线订阅”页。 可以在此页上查看活动消息计数、死信消息计数等。 在此示例中，还有三条活动消息未被接收器接收。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="活动消息计数":::

## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例： 

- [适用于 JavaScript 的 Azure 服务总线客户端库](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript 示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript 示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [API 参考文档](/javascript/api/overview/azure/service-bus)