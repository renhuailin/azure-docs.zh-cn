---
title: 参考 - 适用于 Azure Functions 的 Azure Web PubSub 触发器和绑定
description: 本参考介绍了适用于 Azure Functions 的 Azure Web PubSub 触发器和绑定
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 43fcc70bf88b7e037167a936ebd655b8877b394d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820361"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>适用于 Azure Functions 的 Azure Web PubSub 触发器和绑定

本参考介绍了如何在 Azure Functions 中处理 Web PubSub 事件。

Web PubSub 是一项 Azure 托管服务，可帮助开发人员轻松构建具有实时功能和发布-订阅模式的 Web 应用程序。

| 操作 | 类型 |
|---------|---------|
| 当消息来自服务时运行函数 | [触发器绑定](#trigger-binding) |
| 返回服务终结点 URL 和访问令牌 | [输入绑定](#input-binding)
| 发送 Web PubSub 消息 |[输出绑定](#output-binding) |

[源代码](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/) |
[包](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub) |
[API 参考文档](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Microsoft.Azure.WebJobs.Extensions.WebPubSub/api/Microsoft.Azure.WebJobs.Extensions.WebPubSub.netstandard2.0.cs) |
[产品文档](./index.yml) |
[示例][samples_ref]

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]，预发行版本 | |
| C# Script、JavaScript、Python、PowerShell       | [显式安装扩展]                    | 建议将 [Azure Tools 扩展]用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                                   | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

使用指定的包和版本从 [NuGet](https://www.nuget.org/) 安装客户端库。

```bash
func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
```

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[显式安装扩展]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[更新扩展]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>关键概念

![显示 Azure Web PubSub 服务与 Function 应用协同工作的工作流关系图。](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) 与 HttpTrigger 的 `WebPubSubConnection` 输入绑定以生成客户端连接。

(3)-(4) 与 HttpTrigger 的 `WebPubSubTrigger` 触发器绑定或 `WebPubSubRequest` 输入绑定以处理服务请求。

(5)-(6) `WebPubSub` 输出绑定以请求服务器执行某些工作。

## <a name="trigger-binding"></a>触发器绑定

使用函数触发器处理来自 Azure Web PubSub 服务的请求。 

`WebPubSubTrigger` 在需要处理来自服务端的请求时使用。 触发器终结点模式如下所示，该模式应在 Web PubSub 服务端（门户：“设置”->“事件处理程序”->“URL 模板”）进行设置。 在终结点模式中，出于[安全](../azure-functions/security-concepts.md#system-key)原因使用 Azure Function 应用时，查询部分 `code=<API_KEY>` 是必需的。 密钥可在 Azure 门户中找到。 找到函数应用资源，将函数应用部署到 Azure 后，导航到“函数” -> “应用密钥” -> “系统密钥” -> “webpubsub_extension”。 但是，在使用本地函数时不需要此密钥。

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

### <a name="example"></a>示例


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message: {message}");
    Console.WriteLine($"Request message DataType: {dataType}");
}
```

在某些方案中 `WebPubSubTrigger` 绑定还支持返回值，例如 `Connect` 和 `Message` 事件，在这些方案中，服务器可以检查和拒绝客户端请求，或直接将消息发送到请求客户端。 `Connect` 事件遵守 `ConnectResponse` 和`ErrorResponse`，`Message` 事件遵守 `MessageResponse` 和 `ErrorResponse`，而与当前情况不匹配的其他类型则将忽略。 如果返回 `ErrorResponse`，服务将断开客户端连接。

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    return new MessageResponse
    {
        Message = BinaryData.FromString("ack"),
        DataType = MessageDataType.Text
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 `function.json` 中定义触发器绑定。

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSubTrigger",
      "direction": "in",
      "name": "message",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

在 `index.js` 中定义函数。

```js
module.exports = function (context, message) {
  console.log('Request from: ', context.userId);
  console.log('Request message: ', message);
  console.log('Request message dataType: ', context.bindingData.dataType);
}
```

在某些方案中 `WebPubSubTrigger` 绑定还支持返回值，例如 `Connect` 和 `Message` 事件。 在这些方案中，服务器可以检查和拒绝客户端请求，或直接将消息发送到请求客户端。 在 JavaScript 无类型语言中，将根据对象密钥对其进行反序列化。 相比于其他对象，`ErrorResponse` 具有最高优先级，如果返回中包含 `code`，则会将其解析为 `ErrorResponse` 并断开客户端连接。

```js
module.exports = async function (context) {
  return { 
    "message": "ack",
    "dataType" : "text"
  };
}
```

---


### <a name="attributes-and-annotations"></a>特性和注释

在 [C# 类库](../azure-functions/functions-dotnet-class-library.md)中，使用 `WebPubSubTrigger` 特性。

下面是某个方法签名中的 `WebPubSubTrigger` 特性：

```csharp
[FunctionName("WebPubSubTrigger")]
public static void Run([WebPubSubTrigger("<hub>", "<eventName>", <eventType>)] 
ConnectionContext context, ILogger log)
{
    ...
}
```

有关完整示例，请参阅 C# 示例。

### <a name="configuration"></a>配置

下表解释了在 function.json 文件中设置的绑定配置属性。

| function.json 属性 | Attribute 属性 | 说明 |
|---------|---------|---------|
| type | 不适用 |必需 - 必须设置为 `webPubSubTrigger`。 |
| direction | 不适用 | 必需 - 必须设置为 `in`。 |
| **name** | 不适用 | 必需 - 在函数代码中对接收事件数据的参数使用的变量名称。 |
| hub | 集线器 | 必需 - 该值必须设置为 Web PubSub 中心的名称，用于要触发的函数。 支持在特性中将该值设置为更高优先级，或者在应用设置中将该值设置为全局值。 |
| **eventType** | EventType | 必需 - 该值必须设置为消息的事件类型，用于要触发的函数。 该值应为 `user` 或 `system`。 |
| **eventName** | EventName | 必需 - 该值必须设置为消息事件，用于要触发的函数。 </br> 对于 `system` 事件类型，事件名称应为 `connect`、`connected` 或 `disconnect`。 </br> 对于系统支持的子协议 `json.webpubsub.azure.v1.`，事件名称为用户定义的事件名称。 </br> 对于用户定义的子协议，事件名称为 `message`。 |

### <a name="usages"></a>用途

在 C# 中，`ConnectionContext` 是具有识别类型的绑定参数，其他参数则由参数名称绑定。 请查看下表中的可用参数和类型。

在 JavaScript 等无类型语言中，将使用 `function.json` 中的 `name` 绑定以下映射表中的触发器对象。 当 `name` 设置为 `message` 作为触发器输入的绑定对象时，将遵守 `function.json` 中的 `dataType` 相应地转换消息。 所有参数都可从 `context.bindingData.<BindingName>` 读取并使用 `JObject` 进行转换。 

| 绑定名称 | 绑定类型 | 说明 | 属性 |
|---------|---------|---------|---------|
|connectionContext|`ConnectionContext`|常见请求信息| EventType、EventName、Hub、ConnectionId、UserId、Headers、Signature |
|message|`BinaryData`,`string`,`Stream`,`byte[]`| 从客户端请求消息 | -|
|dataType|`MessageDataType`| 请求消息的数据类型，支持 `binary`、`text` 和 `json` | -|
|声明|`IDictionary<string, string[]>`|`connect` 请求中的用户声明 | -|
|query|`IDictionary<string, string[]>`|`connect` 请求中的用户查询 | -|
|subprotocols|`string[]`|`connect` 请求中的可用子协议 | -|
|clientCertificates|`ClientCertificate[]`|`connect` 请求中客户端的证书指纹列表|-|
|reason|`string`|断开连接请求中的原因|-|

### <a name="return-response"></a> 返回响应

`WebPubSubTrigger` 将遵守`connect` 同步事件和用户事件 `message` 的客户返回响应。 只会将匹配的响应发送回服务，否则将忽略响应。 

| 返回类型 | 说明 | 属性 |
|---------|---------|---------|
|`ConnectResponse`| `connect` 事件的响应 | Groups、Roles、UserId、Subprotocol |
|`MessageResponse`| 用户事件的响应 | DataType、Message |
|`ErrorResponse`| 同步事件的错误响应 | Code、ErrorMessage |
|`ServiceResponse`| 受支持响应的基本响应类型，用于不确定的返回事例 | - |

## <a name="input-binding"></a>输入绑定

扩展提供两个输入绑定，以满足不同的需求。

- `WebPubSubConnection`

  客户端在连接到 Azure Web PubSub 服务之前，必须知道服务终结点 URL 和有效的访问令牌。 `WebPubSubConnection` 输入绑定会生成所需信息，因此客户端无需自行处理令牌生成。 由于此令牌有时间限制，并且可用来对需要连接的特定用户进行身份验证，因此请不要缓存此令牌，也不要在客户端之间共享此令牌。 客户端可以使用处理此输入绑定的 HTTP 触发器来检索连接信息。

- `WebPubSubRequest`

  在使用 Static Web Apps 时，`HttpTrigger` 是唯一受支持的触发器，而且在 Web PubSub 方案中，我们提供的 `WebPubSubRequest` 输入绑定可帮助用户对 Web PubSub 协议下来自服务端的上游 http 请求进行反序列化。 因此，客户可以获得与 `WebPubSubTrigger` 轻松处理函数类似的结果。 请参阅以下[示例](#example---webpubsubrequest)。
  使用 `HttpTrigger` 时，客户需要在上游相应地配置 HttpTrigger 的公开 URL。

### <a name="example---webpubsubconnection"></a>示例 - `WebPubSubConnection`

以下示例演示了一个 C# 函数，该函数使用输入绑定获取 Web PubSub 连接信息，并通过 HTTP 将其返回。

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{query.userid}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 `function.json` 中定义输入绑定。

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "webPubSubConnection",
      "name": "connection",
      "userId": "{query.userid}",
      "hub": "<hub>",
      "direction": "in"
    }
  ]
}
```

在 `index.js` 中定义函数。

```js
module.exports = function (context, req, connection) {
  context.res = { body: connection };
  context.done();
};
```

---

### <a name="authenticated-tokens"></a>已经过身份验证的令牌

如果此函数由经过身份验证的客户端触发，则可向生成的令牌添加用户 ID 声明。 可以轻松地使用应用服务身份验证向函数应用添加身份验证。

应用服务身份验证会设置名为 `x-ms-client-principal-id` 和 `x-ms-client-principal-name`（分别包含经身份验证的用户的客户端主体 ID 和名称）的 HTTP 标头。

可以使用绑定表达式将绑定的 UserId 属性设置为以下任一标头中的值：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`。

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

### <a name="example---webpubsubrequest"></a>示例 - `WebPubSubRequest`

以下示例演示了一个 C# 函数，该函数在连接事件类型下使用输入绑定获取 Web PubSub 请求信息，并通过 HTTP 将其返回。

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubRequestInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubRequest] WebPubSubRequest wpsReq)
{
    if (wpsReq.Request.IsValidationRequest || !wpsReq.Request.Valid)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectResponse
    {
        UserId = wpsReq.ConnectionContext.UserId
    };
    return response;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 `function.json` 中定义输入绑定。

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "webPubSubRequest",
      "name": "wpsReq",
      "direction": "in"
    }
  ]
}
```

在 `index.js` 中定义函数。

```js
module.exports = async function (context, req, wpsReq) {
  if (!wpsReq.request.valid || wpsReq.request.isValidationRequest)
  {
    console.log(`invalid request: ${wpsReq.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${context.bindings.wpsReq.connectionContext.userId} is connecting.`);
  return { body: {"userId": context.bindings.wpsReq.connectionContext.userId} };
};
```

---

### <a name="configuration"></a>配置

#### <a name="webpubsubconnection"></a>WebPubSubConnection

下表解释了在 function.json 文件和 `WebPubSubConnection` 特性中设置的绑定配置属性。

| function.json 属性 | Attribute 属性 | 说明 |
|---------|---------|---------|
| type | 不适用 | 必须设置为 `webPubSubConnection` |
| direction | 不适用 | 必须设置为 `in` |
| **name** | 不适用 | 变量名称，在输入连接绑定对象的函数代码中使用。 |
| hub | 集线器 | 该值必须设置为 Web PubSub 中心的名称，用于要触发的函数。 支持在特性中将该值设置为更高优先级，或者在应用设置中将该值设置为全局值。 |
| **userId** | UserId | 可选 - 用户标识符声明的值，在访问密钥令牌中设置。 |
| **connectionStringSetting** | ConnectionStringSetting | 应用设置的名称，包含 Web PubSub 服务连接字符串（默认为“WebPubSubConnectionString”） |

#### <a name="webpubsubrequest"></a>WebPubSubRequest

下表解释了在 functions.json 文件和 `WebPubSubRequest` 特性中设置的绑定配置属性。

| function.json 属性 | Attribute 属性 | 说明 |
|---------|---------|---------|
| type | 不适用 | 必须设置为 `webPubSubRequest` |
| direction | 不适用 | 必须设置为 `in` |
| **name** | 不适用 | 变量名称，在输入 Web PubSub 请求的函数代码中使用。 |

### <a name="usage"></a>使用情况

#### <a name="webpubsubconnection"></a>WebPubSubConnection

`WebPubSubConnection` 提供以下属性。

绑定名称 | 绑定类型 | 说明
---------|---------|---------
BaseUrl | 字符串 | Web PubSub 客户端连接 URL
URL | string | Web PubSub 连接的绝对 Uri，包含根据请求生成的 `AccessToken`
AccessToken | 字符串 | 根据请求的 UserId 和服务信息生成的 `AccessToken`

#### <a name="webpubsubrequest"></a>WebPubSubRequest

`WebPubSubRequest` 提供以下属性。

绑定名称 | 绑定类型 | 说明 | 属性
---------|---------|---------|---------
connectionContext | `ConnectionContext` | 常见请求信息| EventType、EventName、Hub、ConnectionId、UserId、Headers、Signature
request | `ServiceRequest` | 来自客户端的请求，请参阅下表了解详细信息 | IsValidationRequest、Valid、Unauthorized、BadRequest、ErrorMessage 和 Name 等。
响应 | `HttpResponseMessage` | 扩展生成响应，主要针对 `AbuseProtection` 和错误事例 | -

对于 `ServiceRequest`，会将其反序列化为不同的类，从而提供有关请求方案的不同信息。 对于 `ValidationRequest` 或 `InvalidRequest`，建议直接返回系统生成响应 `WebPubSubRequest.Response`，或者由客户根据需要记录错误。 在不同的方案中，客户可以读取的请求属性如下所示。

派生类 | 说明 | 属性
--|--|--
`ValidationRequest` | 当 `IsValidationRequest` 为 true 时，在 `AbuseProtection` 中使用 | -
`ConnectEventRequest` | 在 `Connect` 事件类型中使用 | Claims、Query、Subprotocols、ClientCertificates
`ConnectedEventRequest` | 在 `Connected` 事件类型中使用 | -
`MessageEventRequest` | 在用户事件类型中使用 | Message、DataType
`DisconnectedEventRequest` | 在 `Disconnected` 事件类型中使用 | Reason
`InvalidRequest` | 当请求无效时使用 | -

## <a name="output-binding"></a>输出绑定

通过 Azure Web PubSub 服务，使用 Web PubSub 输出绑定发送一条或多条消息。 可以将消息广播到：

* 所有连接的客户端
* 对特定用户进行身份验证的已连接客户端
* 加入特定组的已连接客户端

输出绑定还允许管理组，以及授予/撤消针对组的特定 connectionId 的权限。

有关设置和配置的详细信息，请参阅概述。

### <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubOperation> operations)
{
    await operations.AddAsync(new SendToAll
    {
        Message = BinaryData.FromString("Hello Web PubSub"),
        DataType = MessageDataType.Text
    });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 `functions.json` 中定义绑定。

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSub",
      "name": "webPubSubOperation",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

在 `index.js` 中定义函数。

```js
module.exports = async function (context) {
  context.bindings.webPubSubOperation = {
    "operationKind": "sendToAll",
    "message": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsuboperation"></a>WebPubSubOperation 

`WebPubSubOperation` 是输出绑定的基本抽象类型。 派生类型表示希望调用服务的操作服务器。 在 `javascript` 等无类型语言中，`OperationKind` 是解析类型的密钥参数。 在 `csharp` 等强类型语言中，用户可以直接创建目标操作类型，并忽略客户分配的 `OperationKind` 值。

派生类|属性
--|--
`SendToAll`|Message、DataType、Excluded
`SendToGroup`|Group、Message、DataType、Excluded
`SendToUser`|UserId、Message、DataType
`SendToConnection`|ConnectionId、Message、DataType
`AddUserToGroup`|UserId、Group
`RemoveUserFromGroup`|UserId、Group
`RemoveUserFromAllGroups`|UserId
`AddConnectionToGroup`|ConnectionId、Group
`RemoveConnectionFromGroup`|ConnectionId、Group
`CloseClientConnection`|ConnectionId、Reason
`GrantGroupPermission`|ConnectionId、Group、Permission、TargetName
`RevokeGroupPermission`|ConnectionId、Group、Permission、TargetName

### <a name="configuration"></a>配置

#### <a name="webpubsub"></a>WebPubSub

下表解释了在 function.json 文件和 `WebPubSub` 特性中设置的绑定配置属性。

| function.json 属性 | Attribute 属性 | 说明 |
|---------|---------|---------|
| type | 不适用 | 必须设置为 `webPubSub` |
| direction | 不适用 | 必须设置为 `out` |
| **name** | 不适用 | 变量名称，在输出绑定对象的函数代码中使用。 |
| hub | 集线器 | 该值必须设置为 Web PubSub 中心的名称，用于要触发的函数。 支持在特性中将该值设置为更高优先级，或者在应用设置中将该值设置为全局值。 |
| **connectionStringSetting** | ConnectionStringSetting | 应用设置的名称，包含 Web PubSub 服务连接字符串（默认为“WebPubSubConnectionString”） |

## <a name="troubleshooting"></a>故障排除

### <a name="setting-up-console-logging"></a>设置控制台日志记录
如果想要更深入地了解针对服务发出的请求，还可以轻松[启用控制台记录](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging)。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]