---
title: include 文件
description: include 文件
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900784"
---
Azure Functions 允许创建基于预生成的入口点的仅配置复制任务。 [Azure Functions 的基于配置的复制示例](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)说明如何在你自己的代码中利用[预生成的帮助程序](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)，或者完全避免处理代码，而只使用配置。

## <a name="create-a-replication-task"></a>创建复制任务
若要创建这样的复制任务，请先在项目文件夹下面创建一个新文件夹。 新文件夹的名称是函数的名称，例如 `europeToAsia` 或 `telemetry`。 该名称与正在使用的消息实体没有直接关联，仅供你识别它们。 可以在同一个项目中创建数十个函数。

接下来，在文件夹中创建 `function.json` 文件。 该文件配置函数。 从下面的内容开始：

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

在该文件中，需要完成三个配置步骤，这些步骤取决于要连接的实体：

1. [配置输入方向](#configure-the-input-direction)
2. [配置输出方向](#configure-the-output-direction)
3. [配置入口点](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>配置输入方向

#### <a name="event-hub-input"></a>事件中心输入

如果要从事件中心接收事件，请将配置信息添加到设置的“绑定”中的顶部部分

* **类型** -“eventHubTrigger”类型。
* **连接** - 事件中心连接字符串的应用设置值的名称。 
* **eventHubName** - 连接字符串标识的命名空间中的事件中心的名称。
* **consumerGroup** - 使用者组的名称。 请注意，名称括在百分号中，因此也引用应用设置值。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>服务总线队列输入

如果要从服务总线队列接收事件，请将配置信息添加到设置的“绑定”中的顶部部分

* **类型** -“serviceBusTrigger”类型。
* **连接** - 服务总线连接字符串的应用设置值的名称。
* **queueName** - 连接字符串标识的命名空间中的服务总线队列的名称。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>服务总线主题输入

如果要从服务总线主题接收事件，请将配置信息添加到设置的“绑定”中的顶部部分

* **类型** -“serviceBusTrigger”类型。
* **连接** - 服务总线连接字符串的应用设置值的名称。 如果要使用提供的脚本，则此值必须为 `{FunctionName}-source-connection`。
* **topicName** - 连接字符串标识的命名空间中的服务总线主题的名称。
* **subscriptionName** - 连接字符串标识的命名空间中的给定主题上的服务总线订阅的名称。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>配置输出方向

#### <a name="event-hub-output"></a>事件中心输出

如果要将事件转发到事件中心，请将配置信息添加到设置的“绑定”中的底部部分

* **类型** -“eventHub”类型。
* **连接** - 事件中心连接字符串的应用设置值的名称。 
* **eventHubName** - 连接字符串标识的命名空间中的事件中心的名称。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>服务总线队列输出

如果要将事件转发到服务总线队列，请将配置信息添加到设置的“绑定”中的底部部分

* **类型** -“serviceBus”类型。
* **连接** - 服务总线连接字符串的应用设置值的名称。 
* **queueName** - 连接字符串标识的命名空间中的服务总线队列的名称。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>服务总线主题输出

如果要将事件转发到服务总线主题，请将配置信息添加到设置的“绑定”中的底部部分

* **类型** -“serviceBus”类型。
* **连接** - 服务总线连接字符串的应用设置值的名称。 
* **topicName** - 连接字符串标识的命名空间中的服务总线主题的名称。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>配置入口点

入口点配置选择一个标准复制任务。 如果要修改 `Azure.Messaging.Replication` 项目，还可以添加任务并在此处引用它们。 例如：

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

下表提供了源和目标组合的正确值：

| Source      | 目标      | 入口点 
|-------------|-------------|------------------------------------------------------------------------
| 事件中心   | 事件中心   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 事件中心   | 服务总线 | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| 服务总线 | 事件中心   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| 服务总线 | 服务总线 | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>重试策略

请参阅[有关重试的 Azure Functions 文档](../articles/azure-functions/functions-bindings-error-pages.md)，以配置重试策略。 在此存储库的整个项目中选择的策略设置配置了指数退避策略，重试间隔从 5 秒到 5 分钟，并无限重试，以避免数据丢失。

对于服务总线，请查看[“在触发器复原能力的基础上使用重试支持”](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience)部分，以了解触发器的交互和为队列定义的最大传递计数。

### <a name="build-deploy-and-configure"></a>构建、部署和配置

虽然侧重于配置，任务仍然需要构建可部署的应用程序并配置 Azure Functions 主机，使它具有连接到给定终结点所需的所有信息。 

在 [Azure Functions 的基于配置的复制示例](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)中，对这一点以及可重用脚本进行了说明。