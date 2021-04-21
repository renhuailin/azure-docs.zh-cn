---
title: 如何通过 JavaScript 使用 azure/service-bus 队列
description: 了解如何编写使用最新版 @azure/service-bus 包的 JavaScript 程序，以便将消息发送到服务总线队列，并从中接收消息。
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: aee67becf7519f03839eafbd897838f938871307
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537232"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>向 Azure 服务总线队列发送消息并从中接收消息 (JavaScript)
本教程介绍如何使用 JavaScript 程序中的 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 包向服务总线队列发送消息并从中接收消息。

## <a name="prerequisites"></a>先决条件
- Azure 订阅。 若要完成本教程，需要一个 Azure 帐户。 可以激活 [MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。 记下服务总线命名空间的连接字符串以及创建的队列的名称 。

> [!NOTE]
> - 本教程将演练可以使用 [Nodejs](https://nodejs.org/) 复制和运行的示例。 有关如何创建 Node.js 应用程序的说明，请参阅[创建 Node.js 应用程序并将其部署到 Azure 网站](../app-service/quickstart-nodejs.md)或[使用 Windows PowerShell 创建 Node.js 云服务](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用节点包管理器 (NPM) 安装包
若要安装服务总线的 npm 包，请打开路径中包含 `npm` 的命令提示符，将目录更改为要包含示例的文件夹，然后运行此命令

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>向队列发送消息
以下示例代码演示如何向队列发送消息。

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)。
2. 创建一个名为 `send.js` 的文件，并将下面的代码粘贴到其中。 此代码将向队列发送一条消息。 消息具有标签（科学家）和正文（爱因斯坦）。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
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
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
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
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
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
3. 将 `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` 替换为服务总线命名空间的连接字符串。
1. 将 `<QUEUE NAME>` 替换为该队列的名称。 
1. 然后，在命令提示符中运行该命令以执行此文件。

    ```console
    node send.js 
    ```
1. 你会看到以下输出。

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>从队列接收消息

1. 打开你喜好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建名为 `receive.js` 的文件，然后将以下代码粘贴到其中。

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
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
3. 将 `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` 替换为服务总线命名空间的连接字符串。
1. 将 `<QUEUE NAME>` 替换为该队列的名称。 
1. 然后，在命令提示符中运行命令以执行此文件。

    ```console
    node receive.js
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

在 Azure 门户中的服务总线命名空间的“概述”页上，可看到传入和传出消息计数  。 可能需要等待一分钟左右，然后刷新页面才会看到最新值。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="传入和传出消息计数":::

在此“概述”页上选择队列，导航到“服务总线队列”页面 。 还可在此页上看到传入和传出消息计数 。 还可看到其他信息，如队列的当前大小、最大大小和活动消息计数等  。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="队列详细信息":::
## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例： 

- [适用于 JavaScript 的 Azure 服务总线客户端库](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript 示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript 示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [API 参考文档](/javascript/api/overview/azure/service-bus)
