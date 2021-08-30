---
title: Durable Functions 的绑定 - Azure
description: 如何使用 Azure Functions 的 Durable Functions 扩展的触发器和绑定。
ms.topic: conceptual
ms.date: 08/03/2021
ms.author: azfuncdf
ms.openlocfilehash: 097527dbbf4363365e609a1f5aac1d851eb5dc60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742661"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 的绑定

[Durable Functions](durable-functions-overview.md) 扩展引入了三个触发器绑定，用于控制业务流程协调程序、实体和活动函数的执行。 它还引入了输出绑定，充当 Durable Functions 运行时的客户端。

## <a name="orchestration-trigger"></a>业务流程触发器

业务流程触发器可用于创作[持久业务流程协调程序函数](durable-functions-types-features-overview.md#orchestrator-functions)。 安排新业务流程实例且现有业务流程实例接收事件时，便会执行此触发器。 可触发业务流程协调程序函数的事件示例包括持久计时器到期、活动函数响应，以及由外部客户端引发的事件。

在 .NET 中创作函数时，使用 [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .NET 属性配置业务流程触发器。

使用脚本语言（例如 JavaScript、Python 或 PowerShell）编写业务流程协调程序函数时，由 function.json 文件中 `bindings` 数组的以下 JSON 对象定义业务流程触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` 是客户端想要启动此业务流程协调程序函数的新实例时必须使用的业务流程名称。 此属性是可选的。 如果未指定，则使用该函数的名称。

在内部，此触发器绑定轮询已配置的持久存储以查找新的业务流程事件，例如业务流程启动事件、持久计时器到期事件、活动函数响应事件，以及其他函数引发的外部事件。

### <a name="trigger-behavior"></a>触发器行为

以下是有关业务流程触发器的一些注意事项：

* 单线程 - 单个调度程序线程用于单个主机实例上的所有业务流程协调程序函数执行  。 为此，必须确保业务流程协调程序函数代码有效，且不执行任何 I/O 操作。 还必须确保此线程不执行任何异步工作，等待特定于 Durable Functions 的任务类型除外。
* **有害消息处理** - 业务流程触发器中不支持有害消息。
* 消息可见性 - 业务流程触发器消息会取消排队并在可配置的持续时间内保持可见  。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* 返回值 - 返回值序列化为 JSON，并持久保存到 Azure 表存储中的业务流程历史记录表  。 业务流程客户端绑定可以查询这些值，后文会对此进行介绍。

> [!WARNING]
> 业务流程协调程序函数不得使用业务流程触发器绑定之外的任何输入或输出绑定。 这样做有可能导致 Durable Task 扩展出现问题，因为这些绑定可能不遵从单线程处理和 I/O 规则。 若要使用其他绑定，请将它们添加到从业务流程协调程序函数调用的活动函数。 有关业务流程协调程序函数的代码限制的详细信息，请参阅[业务流程协调程序函数代码约束](durable-functions-code-constraints.md)文档。

> [!WARNING]
> 绝不应当将 JavaScript 和 Python 业务流程协调程序函数声明为 `async`。

### <a name="trigger-usage"></a>触发器使用情况

业务流程触发器绑定同时支持输入和输出。 下面是有关输入和输出处理的一些须知事项：

* **输入** - 可以使用通过上下文输入对象获取的输入来调用业务流程触发器。 所有输入必须是 JSON 可序列化类型。
* 输出 - 业务流程触发器支持输出值以及输入  。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。

### <a name="trigger-sample"></a>触发器示例

以下示例代码演示了最简单的“Hello World”业务流程协调程序函数的形式：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    // ... do some work ...
    return $"Hello {name}!";
}
```

> [!NOTE]
> 前面的代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    // ... do some work ...
    return `Hello ${name}!`;
});
```

> [!NOTE]
> 生成器函数退出时，`durable-functions` 库负责调用 `context.done` 方法。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    # Do some work
    return f"Hello {name}!"

main = df.Orchestrator.create(orchestrator_function)
```

---

大多数业务流程协调程序函数会调用活动函数，因此下面的“Hello World”示例演示了如何调用活动函数：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> 前面的代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    result = yield context.call_activity('SayHello', name)
    return result

main = df.Orchestrator.create(orchestrator_function)
```

---

## <a name="activity-trigger"></a>活动触发器

使用活动触发器可以创作业务流程协调程序函数调用的函数（称为[活动函数](durable-functions-types-features-overview.md#activity-functions)）。

如果在 .NET 中创作函数，活动触发器使用 [ActvityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.activitytriggerattribute) .NET 属性进行配置。

如果使用的是 JavaScript、Python 或 PowerShell，则由 function.json 文件中 `bindings` 数组的以下 JSON 对象定义活动触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` 是活动的名称。 此值是业务流程协调程序函数用来调用此活动函数的名称。 此属性是可选的。 如果未指定，则使用该函数的名称。

在内部，此触发器绑定轮询已配置的持久存储以查找新的活动执行事件。

### <a name="trigger-behavior"></a>触发器行为

以下是有关活动触发器的一些注意事项：

* 线程处理 - 与业务流程触发器不同，活动触发器没有关于线程处理或 I/O 的任何限制  。 可以将它们视为常规功能。
* **有害消息处理** - 活动触发器不支持有害消息。
* 消息可见性 - 活动触发器消息会取消排队并在可配置的持续时间内保持可见  。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* **返回值** - 返回值序列化为 JSON，并持久保存到已配置的持久存储。

### <a name="trigger-usage"></a>触发器使用情况

类似于业务流程触发器，活动触发器绑定也同时支持输入和输出。 下面是有关输入和输出处理的一些须知事项：

* **输入** - 可以使用来自业务流程协调程序函数的输入调用活动触发器。 所有输入必须是 JSON 可序列化类型。
* **输出** - 活动函数支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。
* **元数据** - .NET 活动函数可以绑定到 `string instanceId` 参数，以获取调用业务流程的实例 ID。

### <a name="trigger-sample"></a>触发器示例

以下示例代码演示了简单的 `SayHello` 活动函数可能呈现的形式：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

.NET `ActivityTriggerAttribute` 绑定的默认参数类型是 [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext)（对于 Durable Functions v1，则是 [DurableActivityContext](/dotnet/api/microsoft.azure.webjobs.durableactivitycontext?view=azure-dotnet-legacy&preserve-view=true)）。 但是，.NET 活动触发器还支持直接绑定到 JSON 可序列化类型（包括基元类型），因此相同的函数可以简化为如下所示：

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript 绑定可以还作为附加参数进行传入，因此，同一函数可以简化如下：

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

# <a name="python"></a>[Python](#tab/python)

```python
def main(name: str) -> str:
    return f"Hello {name}!"
```

---

### <a name="using-input-and-output-bindings"></a>使用输入和输出绑定

除了活动触发器绑定以外，还可以使用普通的输入和输出绑定。 例如，可将输入提取到活动绑定，并使用“事件中心”输出绑定向事件中心发送消息：

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>业务流程客户端

通过业务流程客户端绑定，可以编写与业务流程协调程序函数进行交互的函数。 这些函数通常称为[客户端函数](durable-functions-types-features-overview.md#client-functions)。 例如，可以通过以下方式对业务流程实例进行操作：

* 启动它们。
* 查询它们的状态。
* 终止它们。
* 当它们正在运行时，向它们发送事件。
* 清除实例历史记录。

如果使用的是 .NET，则可以使用 [DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) 属性（对于 Durable Functions v1.x，则是 [OrchestrationClientAttribute](/dotnet/api/microsoft.azure.webjobs.orchestrationclientattribute?view=azure-dotnet-legacy&preserve-view=true)）绑定到业务流程客户端。

如果使用的是 JavaScript、Python 或 PowerShell 等脚本语言，则由 function.json 文件中 `bindings` 数组的以下 JSON 对象定义持久客户端触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 用于多个函数应用共享同一存储帐户但需要彼此独立的方案。 如果未指定，则使用 `host.json` 中的默认值。 此值必须与目标业务流程协调程序函数所使用的值匹配。
* `connectionName` - 包含存储帐户连接字符串的应用设置的名称。 此连接字符串表示的存储帐户必须与目标业务流程协调程序函数所用的存储帐户相同。 如果未指定，则使用函数应用的默认存储帐户连接字符串。

> [!NOTE]
> 在大多数情况下，建议忽略这些属性，并依靠默认行为。

### <a name="client-usage"></a>客户端使用情况

在 .NET 函数中，你通常会绑定到 [IDurableClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient)（对于 Durable Functions v1.x，则是 [DurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclient?view=azure-dotnet-legacy&preserve-view=true)），它可提供对 Durable Functions 支持的所有业务流程客户端 API 的完全访问权限。 在其他语言中，必须使用特定于语言的 SDK 才能访问客户端对象。

下面的示例是启动“HelloWorld”业务流程的队列触发型函数。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

__init__.py
```python
import json
import azure.functions as func
import azure.durable_functions as df

async def main(msg: func.QueueMessage, starter: str) -> None:
    client = df.DurableOrchestrationClient(starter)
    payload = msg.get_body().decode('utf-8')
    instance_id = await client.start_new("HelloWorld", client_input=payload)
```

---

有关启动实例的更多详细信息，请参阅[实例管理](durable-functions-instance-management.md)。

## <a name="entity-trigger"></a>实体触发器

使用实体触发器可以创作[实体函数](durable-functions-entities.md)。 此触发器支持处理特定实体实例的事件。

> [!NOTE]
> 从 Durable Functions 2.x 开始提供实体触发器。

在内部，此触发器绑定轮询已配置的持久存储以查找需执行的新实体操作。

如果在 .NET 中创作函数，实体触发器会使用 [EntityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.entitytriggerattribute) .NET 属性进行配置。

如果使用的是 JavaScript、Python 或 PowerShell，则由 function.json 的 `bindings` 数组中的以下 JSON 对象定义实体触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "entityName": "<Optional - name of the entity>",
    "type": "entityTrigger",
    "direction": "in"
}
```

默认情况下，实体的名称就是函数的名称。

### <a name="trigger-behavior"></a>触发器行为

以下是有关实体触发器的一些注意事项：

* **单线程**：使用单个调度程序线程来处理特定实体的操作。 如果将多个消息同时发送到单个实体，将会逐个处理操作。
* **有害消息处理** - 实体触发器中不支持有害消息。
* **消息可见性** - 实体触发器消息会取消排队并在可配置的持续时间内保持可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* **返回值** - 实体函数不支持返回值。 可以使用特定的 API 来保存状态，或者将值传回到业务流程。

在执行实体期间对实体所做的任何状态更改将在执行完成后自动保留。

有关定义实体触发器并与之交互的更多信息和示例，请参阅[持久实体](durable-functions-entities.md)文档。

## <a name="entity-client"></a>实体客户端

使用实体客户端绑定可以异步触发[实体函数](#entity-trigger)。 这些函数有时称为[客户端函数](durable-functions-types-features-overview.md#client-functions)。

如果使用 .NET 预编译函数，可以使用 [DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) .NET 属性绑定到实体客户端。

> [!NOTE]
> 还可以使用 `[DurableClientAttribute]` 绑定到[业务流程客户端](#orchestration-client)。

如果使用的是脚本语言（例如 C# 脚本、JavaScript 或 Python）进行开发，则由 function.json 的 `bindings` 数组中的以下 JSON 对象定义实体触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - 用于多个函数应用共享同一存储帐户但需要彼此独立的方案。 如果未指定，则使用 `host.json` 中的默认值。 此值必须与目标实体函数所使用的值匹配。
* `connectionName` - 包含存储帐户连接字符串的应用设置的名称。 此连接字符串表示的存储帐户必须与目标实体函数所用的存储帐户相同。 如果未指定，则使用函数应用的默认存储帐户连接字符串。

> [!NOTE]
> 在大多数情况下，建议忽略可选属性，并依赖默认行为。

有关以客户端形式与实体交互的更多信息和示例，请参阅[持久实体](durable-functions-entities.md#access-entities)文档。

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 设置

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于实例管理的内置 HTTP API 参考](durable-functions-http-api.md)
