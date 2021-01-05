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
ms.openlocfilehash: 9cc19548f0b969421974afe3e274fc5334590cb0
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805663"
---
Azure Functions 允许创建专用于预建入口点的仅配置复制任务。 [Azure Functions 的基于配置的复制示例](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)演示了如何在自己的代码中利用[预建的帮助](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)程序，或者完全避免处理代码，只需使用配置即可。

## <a name="create-a-replication-task"></a>创建复制任务
若要创建此类复制任务，请首先在项目文件夹下创建新文件夹。 新文件夹的名称是函数的名称，例如 `europeToAsia` 或 `telemetry` 。 该名称与正在使用的消息实体不是直接关联的，并且仅用于识别它们。 您可以在同一个项目中创建数十个函数。

接下来， `function.json` 在文件夹中创建一个文件。 文件配置函数。 从以下内容开始：

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

在该文件中，需要完成三个配置步骤，这些步骤依赖于要连接的实体：

1. [配置输入方向](#configure-the-input-direction)
2. [配置输出方向](#configure-the-output-direction)
3. [配置入口点](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>配置输入方向

#### <a name="event-hub-input"></a>事件中心输入

如果要从事件中心接收事件，请将配置信息添加到设置了 "绑定" 的顶部部分

* **类型** -"eventHubTrigger" 类型。
* **连接** -事件中心连接字符串的应用设置值的名称。 
* **eventHubName** -由连接字符串标识的命名空间中的事件中心的名称。
* **consumerGroup** -使用者组的名称。 请注意，名称以百分号括起来，因此也引用应用设置值。

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

如果要从服务总线队列接收事件，请将配置信息添加到设置了 "绑定" 的顶部部分

* **类型** -"serviceBusTrigger" 类型。
* **连接** -服务总线连接字符串的应用设置值的名称。
* **queueName** -由连接字符串标识的命名空间中的服务总线队列的名称。

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

如果要从服务总线主题接收事件，请将配置信息添加到设置的 "绑定" 中的顶部部分

* **类型** -"serviceBusTrigger" 类型。
* **连接** -服务总线连接字符串的应用设置值的名称。 `{FunctionName}-source-connection`如果要使用提供的脚本，此值必须为。
* **topicName** -由连接字符串标识的命名空间中的服务总线主题名称。
* **subscriptionName** -由连接字符串标识的命名空间中给定主题的服务总线订阅的名称。

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

如果要将事件转发到事件中心，请将配置信息添加到设置的 "绑定" 中的底部

* **类型** -"eventHub" 类型。
* **连接** -事件中心连接字符串的应用设置值的名称。 
* **eventHubName** -由连接字符串标识的命名空间中的事件中心的名称。

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

如果要将事件转发到服务总线队列，请将配置信息添加到设置的 "绑定" 中的底部

* **类型** -"双线" 类型。
* **连接** -服务总线连接字符串的应用设置值的名称。 
* **queueName** -由连接字符串标识的命名空间中的服务总线队列的名称。

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
            "eventHubName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>服务总线主题输出

如果要将事件转发到服务总线主题，请将配置信息添加到设置的 "绑定" 中的底部

* **类型** -"双线" 类型。
* **连接** -服务总线连接字符串的应用设置值的名称。 
* **topicName** -由连接字符串标识的命名空间中的服务总线主题名称。

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
            "eventHubName": "queue-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>配置入口点

入口点配置选取标准复制任务之一。 如果要修改 `Azure.Messaging.Replication` 项目，还可以添加任务，并在此处引用它们。 例如：

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

下表提供了源和目标的组合的正确值：

| Source      | 目标      | 入口点 
|-------------|-------------|------------------------------------------------------------------------
| 事件中心   | 事件中心   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 事件中心   | 服务总线 | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| 服务总线 | 事件中心   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| 服务总线 | 服务总线 | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>重试策略

请参阅有关重试配置重试策略的 [Azure Functions 文档](/azure/azure-functions/functions-bindings-error-pages) 。 在此存储库中的整个项目中选择的策略设置将配置一个重试间隔为5秒到5分钟的指数回退策略，以避免数据丢失。

对于服务总线，查看 ["在触发器复原能力顶部使用重试支持"](/azure/azure-functions/functions-bindings-error-pages#using-retry-support-on-top-of-trigger-resilience) 部分，了解触发器的交互和为队列定义的最大传递计数。

### <a name="build-deploy-and-configure"></a>生成、部署和配置

当你重点介绍配置时，任务仍需要生成可部署的应用程序并配置 Azure Functions 主机，使其具有连接到给定终结点所需的所有信息。 

在 [Azure Functions 的基于配置的复制示例](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)中，与可重用脚本一起说明了这一情况。

