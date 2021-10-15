---
title: Azure 事件中心 - 客户端 SDK | Microsoft Docs
description: 本文提供有关 Azure 事件中心的客户端 SDK 的信息。
ms.topic: article
ms.date: 09/21/2021
ms.openlocfilehash: b1a802e5d9829f533187f477bb28037c22c02f3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215883"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure 事件中心 - 客户端 SDK
本文提供有关 Azure 事件中心支持的 SDK 的下列信息： 

- 可在应用程序中使用的包的位置 
- GitHub 位置，可在其中查找源代码、示例、自述文件、更改日志、报告的问题，还可以提出新问题 
- 快速入门教程的链接 

## <a name="client-sdks"></a>客户端 SDK
下表介绍所有最新可用的 Azure 事件中心运行时客户端。 这些库的核心功能是通过事件中心发送和接收消息。

| 语言 | 程序包 | 参考 | 
| -------- | ------- | --------------- | 
| . NET Standard | [Azure.Messaging.EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[教程](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure.Messaging.EventHubs.Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[教程](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[教程](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob)</li><li>[教程](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[教程](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[教程](event-hubs-python-get-started-send.md)</li></ul> |
| Javascript | [azure/event-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[教程](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[教程](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-event-hubs-go)</li><li>[教程](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-event-hubs-c)</li><li>[教程](event-hubs-c-getstarted-send.md)</li></ul> |

下表列出了旧版 Azure 事件中心运行时客户端。 虽然这些包可能会收到严重的 bug 修补程序，但它们并未在积极开发中。 建议改为使用上表中列出的最新 SDK。

| 语言 | 程序包 | 参考 | 
| -------- | ------- | --------------- | 
| . NET Standard  | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)（旧版） | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[教程](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor)（旧版） | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[教程](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)（旧版） | |
|   Java   | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs)（旧版） | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[教程](event-hubs-java-get-started-send.md)</li></ul> |

## <a name="management-sdks"></a>管理 SDK
下面是当前可用的管理特定的库的列表。 这些库不包含运行时操作，管理事件中心实体是其唯一的用途。

- [.NET Standard](/dotnet/api/microsoft.azure.management.eventhub)
- [Java](/java/api/com.microsoft.azure.management.eventhub)
- [Python](/python/api/azure-mgmt-eventhub)
- [JavaScript](/javascript/api/@azure/arm-eventhub/)


## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](./event-hubs-about.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.yml)
