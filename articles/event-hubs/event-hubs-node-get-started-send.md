---
title: 使用 JavaScript 向/从 Azure 事件中心发送/接收事件（最新版）
description: 本文演练如何创建一个可使用最新 azure/event-hubs 包向/从 Azure 事件中心发送/接收事件的 JavaScript 应用程序。
ms.topic: quickstart
ms.date: 09/16/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7422c10060d59bf7194cfb941061e8c5bc2b82cf
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273981"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs"></a>使用 JavaScript (azure/event-hubs) 向/从事件中心发送/接收事件
本快速入门介绍如何使用 azure/event-hubs JavaScript 包向事件中心发送事件以及从事件中心接收事件。 


## <a name="prerequisites"></a>先决条件
如果不熟悉 Azure 事件中心，请在阅读本快速入门之前参阅[事件中心概述](event-hubs-about.md)。 

若要完成本快速入门，需要具备以下先决条件：

- Microsoft Azure 订阅。 若要使用 Azure 服务（包括 Azure 事件中心），需要一个订阅。  如果没有现有的 Azure 帐户，可以注册[免费试用](https://azure.microsoft.com/free/)帐户，或者在[创建帐户](https://azure.microsoft.com)时使用 MSDN 订阅者权益。
- Node.js 版本 8.x 或更高版本。 下载最新的[长期支持 (LTS) 版本](https://nodejs.org)。  
- Visual Studio Code（推荐）或任何其他集成开发环境 (IDE)。  
- 有效的事件中心命名空间和事件中心。 若要创建它们，请执行以下步骤： 

   1. 在 [Azure 门户](https://portal.azure.com)中，创建“事件中心”类型的命名空间，然后获取应用程序与事件中心进行通信所需的管理凭据。  
   1. 若要创建命名空间和事件中心，请按照以下文章中的说明操作：[快速入门：使用 Azure 门户创建事件中心](event-hubs-create.md)。
   1. 按照本快速入门中的说明继续操作。 
   1. 若要获取事件中心命名空间的连接字符串，请按照[获取连接字符串 ](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的说明操作。 请记下该连接字符串，以便稍后在本快速入门中使用。
- **创建事件中心命名空间和事件中心**。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。 然后，按照以下文章中的说明获取事件中心命名空间的连接字符串：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 稍后将在本快速入门中使用连接字符串。

### <a name="install-the-npm-package"></a>安装 npm 包
若要安装 [适用于事件中心的 Node 包管理器 (npm) 包](https://www.npmjs.com/package/@azure/event-hubs)，请打开路径中包含 *npm* 的命令提示符，将目录切换到用于保存示例的文件夹，然后运行以下命令：

```shell
npm install @azure/event-hubs
```

对于接收端，需要额外安装两个包。 本快速入门将使用 Azure Blob 存储来保存检查点，使程序不会读取已读取的事件。 它在 Blob 中按固定的时间间隔对收到的消息执行元数据检查点。 使用此方式可以很容易地在以后的某个时间从退出的位置继续接收消息。

运行以下命令：

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>发送事件

在本部分中，你将创建一个可向事件中心发送事件的 JavaScript 应用程序。

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。
1. 创建名为 *send.js* 的文件，然后将以下代码粘贴到其中：

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. 在代码中，使用实际值替换以下内容：
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. 运行 `node send.js` 以执行此文件。 此命令将包含三个事件的批发送到事件中心。
1. 在 Azure 门户中，验证事件中心是否已收到消息。 在“指标”部分切换到“消息”视图。   刷新页面以更新图表。 可能需要在几秒钟后才会显示已收到消息。

    [![验证事件中心是否已收到消息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 有关完整源代码（包括附加的参考注释），请参阅 [GitHub sendEvents.js 页](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/event-hubs/samples/v5/javascript/sendEvents.js)。

祝贺你！ 现已向事件中心发送事件。


## <a name="receive-events"></a>接收事件
在本部分中，你将在 JavaScript 应用程序中使用 Azure Blob 存储检查点存储从事件中心接收事件。 该应用程序将在 Azure 存储 Blob 中定期针对收到的消息执行元数据检查点。 使用此方式可以很容易地在以后的某个时间从退出的位置继续接收消息。

> [!WARNING]
> 如果在 Azure Stack Hub 上运行此代码，除非将特定的存储 API 版本作为目标，否则会遇到运行时错误。 这是因为事件中心 SDK 使用 Azure 中提供的最新 Azure 存储 API，而此 API 可能在 Azure Stack Hub 平台上不可用。 Azure Stack Hub 支持的存储 Blob SDK 版本可能与 Azure 上通常提供的版本不同。 如果正在将 Azure Blob 存储用作检查点存储，请检查[支持用于你的 Azure Stack Hub 版本的 Azure 存储 API 版本](/azure-stack/user/azure-stack-acs-differences?#api-version)，并在你的代码中面向该版本。 
>
> 例如，如果在 Azure Stack Hub 版本 2005 上运行，则存储服务的最高可用版本为版本 2019-02-02。 默认情况下，事件中心 SDK 客户端库使用 Azure 上的最高可用版本（在 SDK 发布时为 2019-07-07）。 在这种情况下，除了执行本部分中的步骤以外，还需要添加相关代码，将存储服务 API 版本 2019-02-02 作为目标。 如需通过示例来了解如何以特定的存储 API 版本为目标，请参阅 GitHub 上的 [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript/receiveEventsWithApiSpecificStorage.js) 和 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/typescript/src/receiveEventsWithApiSpecificStorage.ts) 示例。 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>创建 Azure 存储帐户和 Blob 容器
若要创建 Azure 存储帐户并在其中创建 Blob 容器，请执行以下操作：

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [在存储帐户中创建 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md)

请务必记下连接字符串和容器名称，供稍后在接收代码中使用。

### <a name="write-code-to-receive-events"></a>编写用于接收事件的代码

1. 打开偏好的编辑器，例如 [Visual Studio Code](https://code.visualstudio.com)。
1. 创建名为 *receive.js* 的文件，然后将以下代码粘贴到其中：

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            if (events.length === 0) {
              console.log(`No events received within wait time. Waiting for next interval`);
              return;
            }
          
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. 在代码中，使用实际值替换以下值：
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. 在命令提示符下运行 `node receive.js` 以执行此文件。 窗口中应会显示有关已收到事件的消息。

    > [!NOTE]
    > 有关完整源代码（包括附加的参考注释），请参阅 [GitHub receiveEventsUsingCheckpointStore.js 页](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript/receiveEventsUsingCheckpointStore.js)。

祝贺你！ 现已从事件中心收到事件。 接收器程序将从事件中心内默认使用者组的所有分区接收事件。

## <a name="next-steps"></a>后续步骤
查看 GitHub 中的以下示例：

- [JavaScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/javascript)
- [TypeScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/typescript)
