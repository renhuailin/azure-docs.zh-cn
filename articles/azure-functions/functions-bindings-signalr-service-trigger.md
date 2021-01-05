---
title: Azure Functions SignalR 服务触发器绑定
description: 了解如何从 Azure Functions 发送 SignalR 服务消息。
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763501"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Azure Functions 的 SignalR 服务触发器绑定

使用 SignalR 触发器绑定来响应从 Azure SignalR 服务发送的消息。 触发函数时，传递给函数的消息分析为 json 对象。

在 SignalR Service 无服务器模式下，SignalR 服务使用 [上游](../azure-signalr/concept-upstream.md) 功能从客户端向 Function App 发送消息。 和 Function App 使用 SignalR 服务触发器绑定来处理这些消息。 一般的体系结构如下所示： :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="SignalR 触发器体系结构":::

有关设置和配置详细信息，请参阅[概述](functions-bindings-signalr-service.md)。

## <a name="example"></a>示例

下面的示例演示一个函数，该函数使用触发器绑定来接收消息并记录该消息。

# <a name="c"></a>[C#](#tab/csharp)

适用于 C# 的 SignalR 服务触发器绑定具有两种编程模型。 基于类的模型和传统模型。 基于类的模型可提供一致的 SignalR 服务器端编程体验。 传统模型提供更大的灵活性，并与其他函数绑定类似。

### <a name="with-class-based-model"></a>使用基于类的模型

有关详细信息，请参阅[基于类的模型](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model)。

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>对于传统模型

传统模型遵守使用 C# 开发的 Azure Function 的约定。 如果不熟悉该约定，可通过[文档](./functions-dotnet-class-library.md)了解和学习。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>使用特性 `[SignalRParameter]` 简化 `ParameterNames`

由于使用 `ParameterNames` 有点麻烦，可使用 `SignalRParameter` 来实现同一目的。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

C# 脚本代码如下所示：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

下面是 Python 代码：

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>配置

### <a name="signalrtrigger"></a>SignalRTrigger

下表解释了在 function.json 文件和 `SignalRTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type| 不适用 | 必须设置为 `SignalRTrigger`。|
|**direction**| 不适用 | 必须设置为 `in`。|
|**name**| 不适用 | 在函数代码中用于“触发器调用上下文”对象的变量名称。 |
|**hubName**|**HubName**| 此值必须设置为要触发的函数的 SignalR 中心的名称。|
|**category**|**类别**| 此值必须设置为要触发的函数的消息类别。 类别可以是下列值之一： <ul><li>**连接**：包括“已连接”和“已断开连接”的事件 </li><li>**消息**：包含除连接类别中事件的所有其他事件</li></ul> |
|**event**|**事件**| 此值必须设置为要触发的函数的消息事件。 对于消息类别，事件是客户端发送的[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目标 。 对于连接类别，只使用“已连接”和“已断开连接”  。 |
|**parameterNames**|**ParameterNames**| （可选）绑定到参数的名称列表。 |
|**connectionStringSetting**|**ConnectionStringSetting**| 应用设置的名称，该设置包含 SignalR 服务连接字符串（默认为“AzureSignalRConnectionString”） |

## <a name="payload"></a>有效负载

触发器输入类型声明为 `InvocationContext` 或自定义类型。 如果选择 `InvocationContext`，会获得对请求内容的完全访问权限。 对于自定义类型，运行时会尝试分析 JSON 请求正文，以设置对象属性。

### <a name="invocationcontext"></a>InvocationContext

InvocationContext 包含 SignalR 服务发送的消息中的所有内容。

|InvocationContext 中的属性 | 说明|
|------------------------------|------------|
|参数| 可用于消息类别。 包含[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的参数|
|错误| 可用于“已断开连接”事件。 如果连接关闭时未发生错误，可以为空，否则会包含错误消息。|
|Hub| 消息所属的中心的名称。|
|Category| 消息的类别。|
|事件| 消息的事件。|
|ConnectionId| 发送消息的客户端的连接 ID。|
|UserId| 发送消息的客户端的用户标识。|
|头文件| 请求的标头。|
|查询| 客户端连接到服务时的请求的查询。|
|声明| 客户端的声明。|

## <a name="using-parameternames"></a>使用 `ParameterNames`

通过 `SignalRTrigger` 中的属性 `ParameterNames`，可将调用消息的参数绑定到函数的参数。 你定义的名称可用作其他绑定中的 [绑定表达式](../azure-functions/functions-bindings-expressions-patterns.md) 的一部分，或者用作代码中的参数。 这为你提供了更方便的方法来访问 `InvocationContext` 的参数。

假设你有一个 JavaScript SignalR 客户端尝试 `broadcast` 在 Azure 函数中使用两个参数调用方法 `message1` `message2` 。

```javascript
await connection.invoke("broadcast", message1, message2);
```

设置后 `parameterNames` ，您定义的名称将分别对应于客户端发送的参数。 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

然后， `arg1` 将包含的内容 `message1` ，并且 `arg2` 将包含的内容 `message2` 。


### <a name="remarks"></a>备注

对于参数绑定，顺序很重要。 如果使用 `ParameterNames`，则 `ParameterNames` 中的顺序与在客户端中调用参数的顺序相匹配。 如果在 C# 中使用特性 `[SignalRParameter]`，则 Azure Function 方法中的参数顺序与客户端中参数的顺序相匹配。

`ParameterNames` 和特性 `[SignalRParameter]` 不能同时使用，否则将出现异常。

## <a name="signalr-service-integration"></a>SignalR 服务集成

使用 SignalR 服务触发器绑定时，SignalR 服务需要 URL 来访问 Function App。 URL 应在 SignalR 服务端的 **上游设置** 中进行配置。 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="上游门户":::

使用 SignalR 服务触发器时，URL 可以简单和格式化，如下所示：

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

`Function_App_URL`可在 Function App 的 "概述" 页上找到， `API_KEY` 由 Azure Function 生成。 可以 `API_KEY` `signalr_extension` 在 Function App 的 " **应用程序密钥** " 边栏选项卡中获取。
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API 密钥":::

如果要将多个 Function App 与一个 SignalR 服务一起使用，上游还可以支持复杂路由规则。 在 [上游设置](../azure-signalr/concept-upstream.md)中查找更多详细信息。

## <a name="step-by-step-sample"></a>分步示例

可以按照 GitHub 中的示例使用 SignalR 服务触发器绑定和上游功能在 Function App 上部署聊天室： [双向聊天室示例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>后续步骤

* [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](../azure-signalr/signalr-concept-serverless-development-config.md)
* [SignalR 服务触发器绑定示例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
