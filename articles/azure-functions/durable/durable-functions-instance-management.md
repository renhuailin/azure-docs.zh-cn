---
title: 在 Durable Functions 中管理实例 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中管理实例。
author: cgillum
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 04c3e9f1a5c5a1a23a618f3274057a5e03a9f0e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752319"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>在 Azure 中管理 Durable Functions 中的实例

Durable Functions 中的业务流程是长时间运行的有状态函数，可使用内置管理 API 来启动、查询和终止。 Durable Functions [业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)还公开了其他几种实例管理 API，例如向实例发送外部事件、清除实例历史记录等。本文详细介绍了所有受支持的实例管理操作。

## <a name="start-instances"></a>启动实例

[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)上的 `StartNewAsync` (.NET)、`startNew` (JavaScript) 或 `start_new` (Python) 方法可启动新业务流程实例。 在内部，此方法通过 [Durable Functions 存储提供程序](durable-functions-storage-providers.md)写入消息，然后将其返回。 此消息以异步方式触发具有指定名称的[业务流程函数](durable-functions-types-features-overview.md#orchestrator-functions)的启动过程。

用于启动新业务流程实例的参数如下所示：

* **名称**：要计划的业务流程协调程序函数的名称。
* **输入**：应作为输入传递给业务流程协调程序函数的任何 JSON 可序列化数据。
* **InstanceId**：（可选）实例的唯一 ID。 如果未指定此参数，该方法将使用随机 ID。

> [!TIP]
> 尽可能为实例 ID 使用随机标识符。 随机实例 ID 有助于确保在多个 VM 上缩放业务流程协调程序函数时均衡分配负载。 使用非随机实例 ID 的适当时机是 ID 必须来自外部源，或实现[单一实例业务流程协调程序](durable-functions-singletons.md)模式时。

以下代码是一个示例函数，用于启动新的业务流程实例：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>除非另外指定，否则本页上的示例通过以下 function.json 使用 HTTP 触发器。

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> 此示例以 Durable Functions 2.x 版为目标。 在 1.x 版中，使用 `orchestrationClient` 而不是 `durableClient`。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>除非另外指定，否则本页上的示例通过以下 function.json 使用 HTTP 触发器。

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> 此示例以 Durable Functions 2.x 版为目标。 在 1.x 版中，使用 `orchestrationClient` 而不是 `durableClient`。

__init__.py

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以使用 Core Tools 中的 [`func durable start-new` 命令](../functions-core-tools-reference.md#func-durable-start-new)直接启动实例，该命令采用以下参数：

* **`function-name`（必需）** ：要启动的函数的名称。
* **`input`（可选）** ：以内联方式或通过 JSON 文件提供的函数输入。 对于文件，请使用 `@` 将路径前缀添加到文件，例如 `@path/to/file.json`。
* **`id`（可选）** ：业务流程实例的 ID。 如果未指定此参数，该命令将使用随机 GUID。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认值为 DurableFunctionsHub。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

> [!NOTE]
> Core Tools 命令假设要从函数应用的根目录运行这些命令。 如果显式提供了 `connection-string-setting` 和 `task-hub-name` 参数，则可以从任何目录运行这些命令。 尽管无需运行函数应用主机即可运行这些命令，但除非运行主机，否则可能观察不到某些效果。 例如，`start-new` 命令会在目标任务中心内将某个启动消息排队，但除非某个正在运行的函数应用程序主机进程可以处理该消息，否则业务流程实际上不会运行。

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

以下命令启动名为 HelloWorld 的函数，并将文件 `counter-data.json` 的内容传递到该函数：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>查询实例

启动新的业务流程实例后，你很可能需要查询其运行时状态，以了解它们是否正在运行、已完成或已失败。

[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)上的 `GetStatusAsync` (.NET)、`getStatus` (JavaScript) 或 `get_status` (Python) 方法会查询业务流程实例的状态。

它采用 `instanceId`（必需）、`showHistory`（可选）、`showHistoryOutput`（可选）和 `showInput`（可选）作为参数。

* **`showHistory`** ：如果设置为 `true`，则响应包含执行历史记录。
* **`showHistoryOutput`** ：如果设置为 `true`，则执行历史记录包含活动输出。
* **`showInput`** ：如果设置为 `false`，则响应不会包含函数的输入。 默认值为 `true`。

此方法返回包含以下属性的对象：

* **名称**：业务流程协调程序函数的名称。
* **InstanceId**：业务流程的实例 ID（应与 `instanceId` 输入相同）。
* **CreatedTime**：业务流程协调程序函数开始运行的时间。
* **LastUpdatedTime**：上次创建业务流程检查点的时间。
* **输入**：函数的输入，采用 JSON 值形式。 如果 `showInput` 为 false，则不会填充此字段。
* **CustomStatus**：JSON 格式的自定义业务流程状态。
* **输出**：函数的输出，采用 JSON 值形式（如果该函数已完成）。 如果业务流程协调程序函数失败，则此属性包含失败详细信息。 如果业务流程协调程序函数已终止，则此属性包含终止原因（如果有）。
* **RuntimeStatus**：以下值之一：
  * **Pending**：实例已计划但尚未开始运行。
  * **正在运行**：实例已开始运行。
  * **Completed**：实例已正常完成。
  * **ContinuedAsNew**：实例已重启自身并生成了新历史记录。 此状态是暂时性状态。
  * **失败**：实例失败并出错。
  * **已终止**：实例突然停止。
* **History**：业务流程的执行历史记录。 仅当 `showHistory` 设置为 `true` 时，才填充此字段。

> [!NOTE]
> 业务流程协调程序在其所有计划的任务完成并且业务流程协调程序返回后才会标记为 `Completed`。 换句话说，业务流程协调程序到达其 `return` 语句不足以将其标记为 `Completed`。 这尤其适用于使用 `WhenAny` 的情况；这些业务流程协调程序经常在执行所有计划的任务之前 `return`。

如果实例不存在，此方法会返回 `null` (.NET)、`undefined` (JavaScript) 或 `None` (Python)。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
    // example: if status.runtimeStatus === df.OrchestrationRuntimeStatus.Running: ...
}
```

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
    # example: if (existing_instance.runtime_status is df.OrchestrationRuntimeStatus.Running) { ...
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以直接使用 Core Tools 中的 [`func durable get-runtime-status` 命令](../functions-core-tools-reference.md#func-durable-get-runtime-status)直接获取业务流程实例的状态。

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

此 `durable get-runtime-status` 命令采用以下参数：

* **`id`（必需）** ：业务流程实例的 ID。
* **`show-input`（可选）** ：如果设置为 `true`，则响应包含函数的输入。 默认值为 `false`。
* **`show-output`（可选）** ：如果设置为 `true`，则响应包含函数的输出。 默认值为 `false`。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令检索业务流程实例 ID 为 0ab8c55a66644d68a3a8b220b12d209c 的实例的状态（包括输入和输出）。 假设从函数应用的根目录运行 `func` 命令：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

可以使用 `durable get-history` 命令检索业务流程实例的历史记录。 它采用了以下参数：

* **`id`（必需）** ：业务流程实例的 ID。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 host.json 中设置此参数。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>查询所有实例

可以使用 [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync) (.NET)、[getStatusAll](/javascript/api/durable-functions/durableorchestrationclient#getstatusall--) (JavaScript) 或 `get_status_all` (Python) 方法来查询[任务中心](durable-functions-task-hubs.md)中所有业务流程实例的状态。 此方法会返回对象列表，这些对象表示与查询参数匹配的业务流程实例。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
    
    // Note: ListInstancesAsync only returns the first page of results.
    // To request additional pages provide the result.ContinuationToken
    // to the OrchestrationStatusQueryCondition's ContinuationToken property.
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以使用 Core Tools 中的 [`func durable get-instances` 命令](../functions-core-tools-reference.md#func-durable-get-instances)直接查询实例。

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

此 `durable get-instances` 命令采用以下参数：

* **`top`（可选）** ：此命令支持分页。 此参数对应于每个请求检索的实例数。 默认值为 10。
* **`continuation-token`（可选）** ：用于指示要检索的实例页或节的标记。 每次执行 `get-instances` 都会向下一个实例集返回一个标记。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>使用筛选器查询实例

如果你实际上并不需要标准实例查询可以提供的所有信息，应该怎样做？ 例如，你只需要查找业务流程的创建时间或业务流程的运行时状态。 为此，可以通过应用筛选器来缩小查询范围。

使用 [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET) 或 [getStatusBy](/javascript/api/durable-functions/durableorchestrationclient#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript) 方法获取与一组预定义筛选器匹配的业务流程实例的列表。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2021, 3, 10, 10, 1, 0),
        new Date(2021, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2021, 3, 10, 10, 1, 0),
        datetime(2021, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

在 Azure Functions Core Tools 中，还可以结合筛选器使用 `durable get-instances` 命令。 除了前面提到的 `top`、`continuation-token`、`connection-string-setting` 和 `task-hub-name` 参数以外，还可以使用三个筛选器参数（`created-after`、`created-before` 和 `runtime-status`）。

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

以下是 `durable get-instances` 命令的参数。

* **`created-after`（可选）** ：检索在此日期/时间 (UTC) 之后创建的实例。 接受 ISO 8601 格式的日期时间。
* **`created-before`（可选）** ：检索在此日期/时间 (UTC) 之前创建的实例。 接受 ISO 8601 格式的日期时间。
* **`runtime-status`（可选）** ：检索具有特定状态（例如 running 或 completed）的实例。 可以提供多个状态（用空格分隔）。
* **`top`（可选）** ：每个请求检索的实例数。 默认值为 10。
* **`continuation-token`（可选）** ：用于指示要检索的实例页或节的标记。 每次执行 `get-instances` 都会向下一个实例集返回一个标记。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

如果未提供任何筛选器（`created-after`、`created-before` 或 `runtime-status`），则无论运行时状态或创建时间是什么，该命令都会检索 `top` 实例。

```bash
func durable get-instances --created-after 2021-03-10T13:57:31Z --created-before  2021-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>终止实例

如果需要花费太长的时间来运行某个业务流程实例，或者出于某种原因需要提前将其停止，则可以将其终止。

可以使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)的 `TerminateAsync` (.NET)、`terminate` (JavaScript) 或 `terminate` (Python) 方法来终止实例。 两个参数为 `instanceId` 和 `reason` 字符串，将写入日志和实例状态。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "Found a bug";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Found a bug";
    return client.terminate(instanceId, reason);
};
```

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Found a bug"
    return client.terminate(instance_id, reason)
```

---

终止的实例最终会转换为 `Terminated` 状态。 但是，这种转换不会立即发生。 而是，终止操作将与该实例的其他操作一起在任务中心排队。 可以使用[实例查询](#query-instances) API 来了解已终止的实例实际何时达到 `Terminated` 状态。

> [!NOTE]
> 当前不会传播实例终止。 无论调用活动函数和子业务流程的业务流程实例是否已终止，活动函数和子业务流程都将运行至完成状态。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以使用 Core Tools 中的 [`func durable terminate` 命令](../functions-core-tools-reference.md#func-durable-terminate)直接终止业务流程实例。

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

此 `durable terminate` 命令采用以下参数：

* **`id`（必需）** ：要终止的业务流程实例的 ID。
* **`reason`（可选）** ：终止的原因。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令终止 ID 为 0ab8c55a66644d68a3a8b220b12d209c 的业务流程实例：

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Found a bug"
```

## <a name="send-events-to-instances"></a>将事件发送到实例

在某些方案中，业务流程协调程序函数需要等待和侦听外部事件。 有用的示例方案包括[监视](durable-functions-overview.md#monitoring)和[人机交互](durable-functions-overview.md#human)方案。

可以使用[业务流程客户端](durable-functions-bindings.md#orchestration-client)的 `RaiseEventAsync` (.NET)、`raiseEvent` (JavaScript) 或 `raise_event` (Python) 方法将事件通知发送到正在运行的实例。 可以处理这些事件的实例是正在等待调用 `WaitForExternalEvent` (.NET)、生成 `waitForExternalEvent` (JavaScript) 任务或生成 `wait_for_external_event` (Python) 任务的实例。

`RaiseEventAsync` (.NET) 和 `raiseEvent` (JavaScript) 的参数如下所示：

* **InstanceId**：实例的唯一 ID。
* **EventName**：要发送的事件的名称。
* **EventData**：要发送到实例的 JSON 可序列化有效负载。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> 如果没有具有指定实例 ID 的业务流程实例，则会丢弃事件消息。 如果实例存在但尚未等待事件，则事件会以实例状态进行存储，直到它做好被接收和处理的准备。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以使用 Core Tools 中的 [`func durable raise-event` 命令](../functions-core-tools-reference.md#func-durable-raise-event)直接向业务流程实例引发事件。

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

此 `durable raise-event` 命令采用以下参数：

* **`id`（必需）** ：业务流程实例的 ID。
* **`event-name`** ：要引发的事件的名称。
* **`event-data`（可选）** ：要发送到业务流程实例的数据。 这可以是某个 JSON 文件的路径，或者，你可以直接在命令行中提供数据。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等待业务流程完成

在长时间运行的业务流程中，你可能想要等待并获取业务流程的结果。 对于这种情况，在业务流程中定义超时期限也可能会很有帮助。 如果超过了超时期限，则应返回业务流程的状态而不是结果。

`WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET)、`waitForCompletionOrCreateCheckStatusResponse` (JavaScript) 或 `wait_for_completion_or_create_check_status_response` (Python) 方法可用于同步获取业务流程实例的实际输出。 默认情况下，这些方法会使用默认值 10 秒作为 `timeout`，使用 1 秒作为 `retryInterval`。  

下面的 HTTP 触发型函数示例演示了如何使用此 API：

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

使用以下行调用该函数。 使用 2 秒作为超时，使用 0.5 秒作为重试间隔：

```bash
curl -X POST "http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5"
```

根据从业务流程实例获取响应所需的时间，存在两种情况：

* 业务流程实例在定义的超时（在本例中为 2 秒）内完成，响应是同步传送的实际业务流程实例输出：

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Thu, 14 Dec 2021 06:14:29 GMT
Transfer-Encoding: chunked

[
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
]
```

* 业务流程实例无法在定义的超时内完成，响应是 [HTTP API URL 发现](durable-functions-http-api.md)中所述的默认值：

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Date: Thu, 14 Dec 2021 06:13:51 GMT
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
Retry-After: 10
Transfer-Encoding: chunked

{
    "id": "d3b72dddefce4e758d92f4d411567177",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
}
```

> [!NOTE]
> Webhook URL 的格式可能会有所不同，具体取决于所运行 Azure Functions 主机的版本。 前面的示例适用于 Azure Functions 3.0 主机。

## <a name="retrieve-http-management-webhook-urls"></a>检索 HTTP 管理 Webhook URL

可以使用外部系统来监视或引发到业务流程的事件。 外部系统可以通过作为 [HTTP API URL 发现](durable-functions-http-features.md#http-api-url-discovery)中所述的默认响应一部分的 Webhook URL 来与 Durable Functions 通信。 也可使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)以编程方式访问 Webhook URL。 可以使用 `CreateHttpManagementPayload` (.NET) 或 `createHttpManagementPayload` (JavaScript) 方法获取某个可序列化的对象，其中包含这些 Webhook URL。

`CreateHttpManagementPayload` (.NET) 和 `createHttpManagementPayload` (JavaScript) 方法有一个参数：

* **instanceId**：实例的唯一 ID。

这些方法返回包含以下字符串属性的对象：

* **Id**：业务流程的实例 ID（应与 `InstanceId` 输入相同）。
* **StatusQueryGetUri**：业务流程实例的状态 URL。
* **SendEventPostUri**：业务流程实例的“引发事件”URL。
* **TerminatePostUri**：业务流程实例的“终止”URL。
* **PurgeHistoryDeleteUri**：业务流程实例的“清除历史记录”URL。

函数可以将这些对象的实例发送到外部系统，以监视或引发相应业务流程中的事件，如以下示例所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableActivityContext` 而不是 `IDurableActivityContext`，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>回退实例（预览）

如果意外的原因导致业务流程失败，可以使用相应的 API 将实例回退到以前的正常状态。 

> [!NOTE]
> 此 API 并不旨在取代适当的错误处理和重试策略。 只能在业务流程实例出于意外的原因而失败时才使用此 API。 有关错误处理和重试策略的详细信息，请参阅[错误处理](durable-functions-error-handling.md)一文。

请使用 [业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)的 `RewindAsync` (.NET) 或 `rewind` (JavaScript) 方法将业务流程恢复到 *Running* 状态。 此方法还会重新运行导致业务流程失败的活动或子业务流程执行失败操作。

例如，假设某个工作流涉及到一系列[人工审批](durable-functions-overview.md#human)。 假设有一系列活动函数会通知某人做出审批并等待其实时响应。 在所有审批活动收到响应或超时后，假设另一活动因应用程序配置错误（例如数据库连接字符串无效）而失败。 结果是业务流程在工作流的深入阶段发生失败。 使用 `RewindAsync` (.NET) 或 `rewind` (JavaScript) API，应用程序管理员可以修复配置错误并将失败的业务流程回退到失败前的状态。 无需再次审批任何人工交互步骤，业务流程现可成功完成。

> [!NOTE]
> 回退功能不支持回退使用持久计时器的业务流程实例。 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python 目前不支持此功能。

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以使用 Core Tools 中的 [`func durable rewind` 命令](../functions-core-tools-reference.md#func-durable-rewind)直接后退业务流程实例。

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

此 `durable rewind` 命令采用以下参数：

* **`id`（必需）** ：业务流程实例的 ID。
* **`reason`（可选）** ：回退业务流程实例的原因。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认使用 [host.json](durable-functions-bindings.md#host-json) 文件中的任务中心名称。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除实例历史记录

若要删除与业务流程关联的所有数据，可以清除实例历史记录。 例如，你可能想要删除与完成的实例相关联的任何 Azure 表行和大消息 Blob。 为此，请使用[业务流程客户端](durable-functions-bindings.md#orchestration-client)对象的 `PurgeInstanceHistoryAsync` (.NET)、`purgeInstanceHistory` (JavaScript) 或 `purge_instance_history` (Python) 方法。

此方法有两个重载。 第一个重载按业务流程实例的 ID 清除历史记录：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

有关 function.json 配置，请参见[启动实例](#javascript-function-json)。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

接下来的示例演示一个计时器触发的函数，该函数清除在指定的时间间隔后完成的所有业务流程实例的历史记录。 在本例中，该函数将删除 30 天或更长时间以前完成的所有实例的数据。 此示例函数计划在每天下午 12:00 UTC 运行一次：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")] TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`purgeInstanceHistoryBy` 方法可用于有条件地清除多个实例的实例历史记录。

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> 此示例以 Durable Functions 2.x 版为目标。 在 1.x 版中，使用 `orchestrationClient` 而不是 `durableClient`。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.min
    created_time_to = datetime.today() + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> 若要成功完成清除历史记录的操作，目标实例的运行时状态必须是 **Completed**、**Terminated** 或 **Failed**。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

可以使用 Core Tools 中的 [`func durable purge-history` 命令](../functions-core-tools-reference.md#func-durable-purge-history)清除业务流程实例的历史记录。 类似于前一部分中的第二个 C# 示例，该命令将清除在指定时间间隔内创建的所有业务流程实例的历史记录。 可按运行时状态进一步筛选已清除的实例。

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

此 `durable purge-history` 命令有多个参数：

* **`created-after`（可选）** ：清除此日期/时间 (UTC) 之后创建的实例的历史记录。 接受 ISO 8601 格式的日期时间。
* **`created-before`（可选）** ：清除此日期/时间 (UTC) 之前创建的实例的历史记录。 接受 ISO 8601 格式的日期时间。
* **`runtime-status`（可选）** ：清除具有特定状态（例如 running 或 completed）的实例的历史记录。 可以提供多个状态（用空格分隔）。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认使用 [host.json](durable-functions-bindings.md#host-json) 文件中的任务中心名称。

以下命令将删除在 2021 年 11 月 14 日下午 7:35 (UTC) 之前创建的所有失败实例的历史记录。

```bash
func durable purge-history --created-before 2021-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>删除任务中心

可以使用 Core Tools 中的 [`func durable delete-task-hub` 命令](../functions-core-tools-reference.md#func-durable-delete-task-hub)直接删除与特定任务中心关联的所有存储项目，包括 Azure 存储表、队列和 blob。 

> [!NOTE]
> 当前仅在使用默认的 [Azure 存储提供程序](durable-functions-storage-providers.md)来保持运行时状态时，才支持 Core Tools 命令。

此 `durable delete-task-hub` 命令有两个参数：

* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）** ：要使用的 Durable Functions 任务中心的名称。 默认使用 [host.json](durable-functions-bindings.md#host-json) 文件中的任务中心名称。

以下命令删除与 `UserTest` 任务中心关联的所有 Azure 存储数据。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理版本控制](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [用于实例管理的内置 HTTP API 参考](durable-functions-http-api.md)
