---
title: Azure Functions 的 Azure 服务总线绑定
description: 了解如何在 Azure Functions 中发送 Azure 服务总线触发器和绑定。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 45c7f5a8d6e2c227e07765f6147e58242ffe3d6c
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228909"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服务总线绑定

Azure Functions 通过[触发器和绑定](./functions-triggers-bindings.md)与 [Azure 服务总线](https://azure.microsoft.com/services/service-bus)集成。 与服务总线集成后，你可以构建响应和发送队列或主题消息的函数。

| 操作 | 类型 |
|---------|---------|
| 创建服务总线队列或主题消息时运行函数 | [触发器](./functions-bindings-service-bus-trigger.md) |
| 发送 Azure 服务总线消息 |[输出绑定](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

> [!NOTE]
> 服务总线绑定目前不支持使用托管标识进行身份验证。 请改用[服务总线共享访问签名](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)。

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 4.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展]用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

#### <a name="service-bus-extension-5x-and-higher"></a>服务总线扩展 5.x 及更高版本

新版服务总线绑定扩展以[预览版 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/5.0.0-beta.2)的形式提供。 此预览版引入了[使用标识而不是机密进行连接](./functions-reference.md#configure-an-identity-based-connection)的功能。 对于 .NET 应用程序，它还会更改你可以绑定到的类型，并将 `Microsoft.ServiceBus.Messaging` 和 `Microsoft.Azure.ServiceBus` 中的类型替换为 [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus) 中的新类型。

> [!NOTE]
> 预览包不包括在扩展捆绑包中，必须手动安装。 对于 .NET 应用，请添加对包的引用。 对于所有其他应用类型，请参阅[更新扩展]。

[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[更新扩展]: ./functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 应用会自动引用 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 程序包（版本 2.x）。


<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍版本 2.x 及更高版本中可用于此绑定的全局配置设置。 下面的示例 host.json 文件仅包含此绑定的设置。 有关全局配置设置的详细信息，请参阅 [Azure Functions 版本的 host.json 参考](functions-host-json.md)。

> [!NOTE]
> 有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            },
            "batchOptions": {
                "maxMessageCount": 1000,
                "operationTimeout": "00:01:00",
                "autoComplete": true
            }
        }
    }
}
```

如果将 `isSessionsEnabled` 设置为 `true`，则采用 `sessionHandlerOptions`。  如果将 `isSessionsEnabled` 设置为 `false`，则采用 `messageHandlerOptions`。

|属性  |默认 | 说明 |
|---------|---------|---------|
|prefetchCount|0|获取或设置消息接收方可以同时请求的消息数。|
|messageHandlerOptions.maxAutoRenewDuration|00:05:00|自动续订消息锁的最长持续时间。|
|messageHandlerOptions.autoComplete|是|是触发器在处理后自动调用 complete，还是函数代码手动调用 complete。<br><br>仅在 C# 中支持将其设置为 `false`。<br><br>如果设置为 `true`，则触发器会在函数执行成功完成时自动完成该消息，否则会放弃该消息。<br><br>设置为 `false` 时，你负责调用 [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) 方法来完成、放弃消息或将消息放入死信队列。 如果引发了异常（并且未调用任何 `MessageReceiver` 方法），则锁仍然存在。 锁到期后，消息会重新排队，同时 `DeliveryCount` 会递增，并且锁会自动续订。<br><br>在非 C# 函数中，函数中的异常会导致运行时在后台调用 `abandonAsync`。 如果未发生异常，则在后台调用 `completeAsync`。 |
|messageHandlerOptions.maxConcurrentCalls|16|对于每个缩放实例，消息泵应对回调发起的最大并发调用数。 默认情况下，Functions 运行时同时处理多条消息。|
|sessionHandlerOptions.maxConcurrentSessions|2000|每个缩放实例可以并发处理的最大会话数。|
|batchOptions.maxMessageCount|1000| 触发时发送到函数的最大消息数。 |
|batchOptions.operationTimeout|00:01:00| 以 `hh:mm:ss` 表示的时间跨度值。 |
|batchOptions.autoComplete|是| 请参阅上面对 `messageHandlerOptions.autoComplete` 的说明。 |

### <a name="additional-settings-for-version-5x"></a>版本 5.x+ 的其他设置

下面的示例 host.json 文件仅包含服务总线扩展的 5.0.0 版及更高版本的设置。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "retryOptions":{
                "mode": "exponential",
                "tryTimeout": "00:01:00",
                "delay": "00:00:00.80",
                "maxDelay": "00:01:00",
                "maxRetries": 3
            },
            "prefetchCount": 0,
            "autoCompleteMessages": true,
            "maxAutoLockRenewalDuration": "00:05:00",
            "maxConcurrentCalls": 16,
            "maxConcurrentSessions": 8,
            "maxMessages": 1000,
            "sessionIdleTimeout": "00:01:00"
        }
    }
}
```

使用服务总线扩展 5.x 及更高版本时，除了 `ServiceBusOptions` 中的 2.x 设置外，还支持以下全局配置设置。

|属性  |默认 | 说明 |
|---------|---------|---------|
|prefetchCount|0|获取或设置消息接收方可以同时请求的消息数。|
|autoCompleteMessages|true|确定是否在成功执行函数后自动完成消息。应该用来替换 `autoComplete` 配置设置。|
|maxAutoLockRenewalDuration|00:05:00|此属性应该用来替换 `maxAutoRenewDuration`|
|maxConcurrentCalls|16|对于每个缩放实例，消息泵应对回调发起的最大并发调用数。 默认情况下，Functions 运行时同时处理多条消息。|
|maxConcurrentSessions|8|每个缩放实例可以并发处理的最大会话数。|
|maxMessages|1000|将传递给每个函数调用的消息的最大数量。 这仅适用于接收一批消息的函数。|
|sessionIdleTimeout|不适用|当前活动会话等待某个消息被接收的最长时间。 经过该时间后，处理器将关闭此会话并尝试处理另一个会话。|

### <a name="retry-settings"></a>重试设置

除了使用 5.x 版及更高版本的服务总线扩展时的上述配置属性外，你还可以从 `ServiceBusOptions` 内部配置 `RetryOptions`。 这些设置确定了是否应重试失败的操作。如果应重试，则请确定两次重试之间的等待时间。 这些选项还控制允许用于接收消息以及与服务总线服务进行的其他交互的时间。

|属性  |默认 | 说明 |
|---------|---------|---------|
|mode|指数|用于计算重试延迟的方法。 默认指数模式将根据一个回退策略来重试带延迟的尝试，该策略规定每次尝试都会增加重试前的等待时间。 `Fixed` 模式将按固定间隔重试，每个延迟的持续时间一致。|
|tryTimeout|00:01:00|每次尝试等待操作的最大持续时间。|
|delay|00:00:00.80|要在两次重试之间应用的延迟或回退因子。|
|maxDelay|00:01:00|允许出现在两次重试之间的最大延迟|
|maxRetries|3|将关联的操作视为失败之前的最大重试次数。|

---

## <a name="next-steps"></a>后续步骤

- [创建服务总线队列或主题消息时运行函数（触发器）](./functions-bindings-service-bus-trigger.md)
- [从 Azure Functions 发送 Azure 服务总线消息（输出绑定）](./functions-bindings-service-bus-output.md)
