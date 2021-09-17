---
title: Durable Functions 概述 - Azure
description: Azure Functions 的 Durable Functions 扩展简介。
author: cgillum
ms.topic: overview
ms.date: 12/23/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 987857c4534ddbef90ef3eca5f73f554db0c4341
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777709"
---
# <a name="what-are-durable-functions"></a>什么是 Durable Functions？

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 的一个扩展，可用于在无服务器计算环境中编写有状态函数。 在该扩展中，可以通过编写[业务流程协调程序函数](durable-functions-orchestrations.md)和有状态实体并使用 Azure Functions 编程模型编写[实体函数](durable-functions-entities.md)，来定义有状态工作流。 在幕后，该扩展可以管理状态、检查点和重启，使你可以专注于业务逻辑。

## <a name="supported-languages"></a><a name="language-support"></a>支持的语言

Durable Functions 目前支持以下语言：

* **C#**：[预编译的类库](../functions-dotnet-class-library.md)和 [C# 脚本](../functions-reference-csharp.md)。
* JavaScript：仅 Azure Functions 运行时的版本 2.x 或更高版本支持此语言。 要求使用 1.7.0 版或更高版本的 Durable Functions 扩展。 
* **Python**：要求使用 2.3.1 版或更高版本的 Durable Functions 扩展。
* **F#** ：预编译的类库和 F# 脚本。 仅 Azure Functions 运行时的版本 1.x 支持 F# 脚本。
* **PowerShell**：仅 Azure Functions 运行时的版本 3.x 和 PowerShell 7 支持。 需要 2.x 版的捆绑包扩展。

若要访问最新的功能和更新，建议使用最新版本的 Durable Functions 扩展和特定于语言的 Durable Functions 库。 请详细了解 [Durable Functions 版本](durable-functions-versions.md)。

Durable Functions 的目标是支持所有 [Azure Functions 语言](../supported-languages.md)。 请参阅 [Durable Functions 问题列表](https://github.com/Azure/azure-functions-durable-extension/issues)，了解支持其他语言所需的最新工作状态。

与 Azure Functions 一样，可以使用 [Visual Studio 2019](durable-functions-create-first-csharp.md)、[Visual Studio Code](quickstart-js-vscode.md) 和 [Azure 门户](durable-functions-create-portal.md)通过模板来开发 Durable Functions。

## <a name="application-patterns"></a>应用程序模式

Durable Functions 的主要用例是简化无服务器应用程序中出现的复杂的有状态协调要求。 以下部分介绍可受益于 Durable Functions 的典型应用程序模式：

* [函数链](#chaining)
* [扇出/扇入](#fan-in-out)
* [异步 HTTP API](#async-http)
* [监视](#monitoring)
* [人机交互](#human)
* [聚合器（有状态实体）](#aggregator)

### <a name="pattern-1-function-chaining"></a>模式 1：函数链

在函数链接模式中，将按特定顺序执行一系列函数。 在此模式中，一个函数的输出将应用到另一函数的输入。

![函数链模式示意图](./media/durable-functions-concepts/function-chaining.png)

可按以下示例所示使用 Durable Functions 来实现函数链模式。

在此示例中，值 `F1`、`F2``F3` 和 `F4` 是同一函数应用中其他函数的名称。 可以使用常规命令性编码构造实现控制流。 代码从上到下执行。 代码可能涉及现有语言控制流语义，如条件语句和循环语句。 可以在 `try`/`catch`/`finally` 块中包含错误处理逻辑。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

可以使用 `context` 参数按名称调用其他函数、传递参数并返回函数输出。 每当代码调用 `await` 时，Durable Functions 框架都会对当前函数实例的进度执行检查点操作。 如果在执行中途回收进程或虚拟机，函数实例将从上一个 `await` 调用继续执行。 有关详细信息，请参阅下一部分“模式 #2：扇出/扇入”。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

可以使用 `context.df` 对象按名称调用其他函数、传递参数并返回函数输出。 每当代码调用 `yield` 时，Durable Functions 框架都会对当前函数实例的进度执行检查点操作。 如果在执行中途回收进程或虚拟机，函数实例将从上一个 `yield` 调用继续执行。 有关详细信息，请参阅下一部分“模式 #2：扇出/扇入”。

> [!NOTE]
> JavaScript 中的 `context` 对象表示整个[函数上下文](../functions-reference-node.md#context-object)。 使用主上下文中的 `df` 属性访问 Durable Functions 上下文。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

可以使用 `context` 对象按名称调用其他函数、传递参数并返回函数输出。 每当代码调用 `yield` 时，Durable Functions 框架都会对当前函数实例的进度执行检查点操作。 如果在执行中途回收进程或虚拟机，函数实例将从上一个 `yield` 调用继续执行。 有关详细信息，请参阅下一部分“模式 #2：扇出/扇入”。

> [!NOTE]
> Python 中的 `context` 对象表示业务流程上下文。 使用业务流程上下文上的 `function_context` 属性访问主 Azure Functions 上下文。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-DurableActivity -FunctionName 'F1'
$Y = Invoke-DurableActivity -FunctionName 'F2' -Input $X
$Z = Invoke-DurableActivity -FunctionName 'F3' -Input $Y
Invoke-DurableActivity -FunctionName 'F4' -Input $Z
```

可以使用 `Invoke-DurableActivity` 命令按名称调用其他函数、传递参数并返回函数输出结果。 每当代码在不使用 `NoWait` 开关的情况下调用 `Invoke-DurableActivity` 时，Durable Functions 框架都会对当前函数实例的进度执行检查点操作。 如果在执行中途回收进程或虚拟机，函数实例将从上一个 `Invoke-DurableActivity` 调用继续执行。 有关详细信息，请参阅下一部分“模式 #2：扇出/扇入”。

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>模式 #2：扇出/扇入

在扇出/扇入模式中，将会并行执行多个函数，然后等待所有函数完成。 通常会对这些函数返回的结果执行一些聚合操作。

![扇出/扇入模式示意图](./media/durable-functions-concepts/fan-out-fan-in.png)

对于普通函数，可通过使函数向一个队列发送多条消息来完成扇出。 扇回的难度要大得多。 若要扇入，需要在普通函数中编写代码，以跟踪队列触发的函数的结束时间，然后存储函数输出。

Durable Functions 扩展可使用相对简单的代码处理这种模式：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

扇出工作将分散到 `F2` 函数的多个实例。 可使用动态任务列表跟踪此工作。 将调用 `Task.WhenAll` 来等待所有被调用函数完成。 然后，从动态任务列表聚合 `F2` 函数输出，并将这些输出传递给 `F3` 函数。

在针对 `Task.WhenAll` 调用 `await` 时自动执行的检查点操作确保中途可能出现的任何崩溃或重新启动无需重启已完成的任务。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

扇出工作将分散到 `F2` 函数的多个实例。 可使用动态任务列表跟踪此工作。 将调用 `context.df.Task.all` API 来等待所有被调用函数完成。 然后，从动态任务列表聚合 `F2` 函数输出，并将这些输出传递给 `F3` 函数。

在针对 `context.df.Task.all` 调用 `yield` 时自动执行的检查点操作确保中途可能出现的任何崩溃或重新启动无需重启已完成的任务。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

扇出工作将分散到 `F2` 函数的多个实例。 可使用动态任务列表跟踪此工作。 将调用 `context.task_all` API 来等待所有被调用函数完成。 然后，从动态任务列表聚合 `F2` 函数输出，并将这些输出传递给 `F3` 函数。

在针对 `context.task_all` 调用 `yield` 时自动执行的检查点操作确保中途可能出现的任何崩溃或重新启动无需重启已完成的任务。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-DurableActivity -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-DurableActivity -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-DurableActivity -FunctionName 'F3' -Input $Total
```

扇出工作将分散到 `F2` 函数的多个实例。 请注意，在 `F2` 函数调用上使用 `NoWait` 开关：此开关允许业务流程协调程序继续调用 `F2`，而无需完成活动。 可使用动态任务列表跟踪此工作。 将调用 `Wait-ActivityFunction` 命令来等待所有被调用函数完成。 然后，从动态任务列表聚合 `F2` 函数输出，并将这些输出传递给 `F3` 函数。

在调用 `Wait-ActivityFunction` 时自动执行的检查点操作确保中途可能出现的任何崩溃或重新启动无需重启已完成的任务。

---

> [!NOTE]
> 在极少数情况下，崩溃可能在活动函数完成之后、其完成状态已保存到业务流程历史记录之前的时段内发生。 如果发生这种情况，则在进程恢复之后，活动函数将从头开始重新运行。

### <a name="pattern-3-async-http-apis"></a>模式 3：异步 HTTP API

异步 HTTP API 模式解决了使用外部客户端协调长时间运行的操作的状态时出现的问题。 实现此模式的一种常用方式是让 HTTP 终结点触发长时间运行的操作。 然后，将客户端重定向到某个状态终结点，客户端可轮询该终结点，以了解操作是何时完成的。

![HTTP API 模式示意图](./media/durable-functions-concepts/async-http-api.png)

Durable Functions **原生支持** 此模式，可以简化甚至消除为了与长时间运行的函数执行进行交互而需要编写的代码。 例如，Durable Functions 快速入门示例（[C#](durable-functions-create-first-csharp.md) 和 [JavaScript](quickstart-js-vscode.md)）演示了可用于启动新业务流程协调程序函数实例的简单 REST 命令。 启动实例后，该扩展会公开 Webhook HTTP API 用于查询业务流程协调程序函数的状态。 

以下示例演示用于启动业务流程协调程序和查询其状态的 REST 命令。 为简明起见，实例中省略了一些协议细节。

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

由于状态由 Durable Functions 运行时管理，因此你无需实现自己的状态跟踪机制。

Durable Functions 扩展公开内置的 HTTP API 用于管理长时间运行的业务流程。 你也可以使用自己的函数触发器（例如 HTTP、队列或 Azure 事件中心）和[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)来自行实现此模式。 例如，可以使用队列消息触发终止。 或者，可以使用受 Azure Active Directory 身份验证策略保护的 HTTP 触发器，而不使用利用生成的密钥进行身份验证的内置 HTTP API。

有关详细信息，请参阅 [HTTP 功能](durable-functions-http-features.md)一文，其中介绍了如何使用 Durable Functions 扩展通过 HTTP 公开异步的长时间运行的进程。

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>模式 #4：监视

监视模式是指工作流中的灵活重复进程。 例如，轮询到满足特定的条件为止。 可以使用常规[计时器触发器](../functions-bindings-timer.md)解决简单方案（例如定期清理作业），但该方案的间隔是静态的，并且管理实例生存期会变得复杂。 可以使用 Durable Functions 创建灵活的重复间隔、管理任务生存期，以及从单个业务流程创建多个监视进程。

监视模式的一个例子是反转前面所述的异步 HTTP API 方案。 监视模式不会公开终结点供外部客户端监视长时间运行的操作，而是让长时间运行的监视器使用外部终结点，然后等待某个状态发生更改。

![监视模式示意图](./media/durable-functions-concepts/monitor.png)

只需编写少量的代码行，即可使用 Durable Functions 创建多个监视器来观察任意终结点。 监视器在满足某个条件时可以结束执行，或者，另一函数可以使用持久业务流程客户端来终止监视器。 可以根据特定的条件（例如指数退避）更改监视器的 `wait` 间隔。 

以下代码实现一个基本的监视器：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$output = @()

$jobId = $Context.Input.JobId
$machineId = $Context.Input.MachineId
$pollingInterval = New-TimeSpan -Seconds $Context.Input.PollingInterval
$expiryTime = $Context.Input.ExpiryTime

while ($Context.CurrentUtcDateTime -lt $expiryTime) {
    $jobStatus = Invoke-DurableActivity -FunctionName 'GetJobStatus' -Input $jobId
    if ($jobStatus -eq "Completed") {
        # Perform an action when a condition is met.
        $output += Invoke-DurableActivity -FunctionName 'SendAlert' -Input $machineId
        break
    }

    # Orchestration sleeps until this time.
    Start-DurableTimer -Duration $pollingInterval
}

# Perform more work here, or let the orchestration end.

$output
```

---

收到请求时，会为该作业 ID 创建新的业务流程实例。 该实例会一直轮询状态，直到满足条件退出循环。 持久计时器控制轮询间隔。 然后可以执行其他操作，或者可以结束业务流程。 当 `nextCheck` 超过 `expiryTime` 时，监视器将结束。

### <a name="pattern-5-human-interaction"></a>模式 5：人机交互

许多自动化过程涉及到某种人机交互。 自动化过程中涉及的人机交互非常棘手，因为人的可用性和响应能力不如云服务那样高。 自动化过程允许使用超时和补偿逻辑来实现这种交互。

审批过程就是涉及到人机交互的业务过程的一个例子。 例如，某份超出特定金额的开支报表需要经理的审批。 如果经理未在 72 小时内审批该开支报表（经理可能正在度假），则会启动上报过程，让其他某人（可能是经理的经理）审批。

![人机交互模式示意图](./media/durable-functions-concepts/approval.png)

在此示例中，可以使用业务流程协调程序函数实现该模式。 业务流程协调程序使用[持久计时器](durable-functions-timers.md)请求审批。 如果发生超时，业务流程协调程序会将事务上报。 业务流程协调程序等待发生某个[外部事件](durable-functions-external-events.md)，例如，人机交互生成的通知。

这些示例创建一个审批过程来演示人机交互模式：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

若要创建持久计时器，请调用 `context.CreateTimer`。 通知由 `context.WaitForExternalEvent` 接收。 然后，调用 `Task.WhenAny` 来确定是上报（首先发生超时）还是处理审批（超时前收到审批）。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

若要创建持久计时器，请调用 `context.df.createTimer`。 通知由 `context.df.waitForExternalEvent` 接收。 然后，调用 `context.df.Task.any` 来确定是上报（首先发生超时）还是处理审批（超时前收到审批）。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

若要创建持久计时器，请调用 `context.create_timer`。 通知由 `context.wait_for_external_event` 接收。 然后，调用 `context.task_any` 来确定是上报（首先发生超时）还是处理审批（超时前收到审批）。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$output = @()

$duration = New-TimeSpan -Seconds $Context.Input.Duration
$managerId = $Context.Input.ManagerId

$output += Invoke-DurableActivity -FunctionName "RequestApproval" -Input $managerId

$durableTimeoutEvent = Start-DurableTimer -Duration $duration -NoWait
$approvalEvent = Start-DurableExternalEventListener -EventName "ApprovalEvent" -NoWait

$firstEvent = Wait-DurableTask -Task @($approvalEvent, $durableTimeoutEvent) -Any

if ($approvalEvent -eq $firstEvent) {
    Stop-DurableTimerTask -Task $durableTimeoutEvent
    $output += Invoke-DurableActivity -FunctionName "ProcessApproval" -Input $approvalEvent
}
else {
    $output += Invoke-DurableActivity -FunctionName "EscalateApproval"
}

$output
```
若要创建持久计时器，请调用 `Start-DurableTimer`。 通知由 `Start-DurableExternalEventListener` 接收。 然后，调用 `Wait-DurableTask` 来确定是上报（首先发生超时）还是处理审批（超时前收到审批）。

---

外部客户端可以使用[内置 HTTP API](durable-functions-http-api.md#raise-event) 将事件通知传递给正在等待的业务流程协调程序函数：

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

也可以在同一函数应用中，使用另一个函数的持久业务流程客户端引发事件：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell

Send-DurableExternalEvent -InstanceId $InstanceId -EventName "ApprovalEvent" -EventData "true"

``````

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>模式 #6：聚合器（有状态实体）

第六种模式涉及到将一段时间的事件数据聚合到单个可寻址的实体。 在此模式中，要聚合的数据可来自多个源、可分批传送，也可以分散在较长的时间段。 聚合器可能需要在事件数据抵达时对其执行操作，而外部客户端可能需要查询聚合数据。

![聚合器示意图](./media/durable-functions-concepts/aggregator.png)

使用普通无状态函数尝试实现此模式的棘手之处在于，并发控制会成为一项巨大的挑战。 你不仅需要考虑到多个线程会同时修改相同的数据，而还要考虑如何确保聚合器每次仅在一个 VM 上运行。

可以使用[持久实体](durable-functions-entities.md)轻松地将此模式实现为单一函数。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

持久实体也可以在 .NET 中建模为类。 如果操作列表是固定的或者会变大，则此模型可能很有用。 以下示例是使用 .NET 类和方法的 `Counter` 实体的等效实现。

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json

import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableOrchestrationContext):

    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
        context.set_result(current_value)
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    
    context.set_state(current_value)

main = df.Entity.create(entity_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 目前不支持持久性实体。

---

客户端可以使用[实体客户端绑定](durable-functions-bindings.md#entity-client)将实体函数的操作排入队列（也称为“信号发送”）。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableEntityClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> .NET 中也可以使用动态生成的代理以类型安全的方式发信号通知实体。 此外，除了发送信号外，客户端还可以对业务流程客户端绑定使用[类型安全方法](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)来查询实体函数的状态。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    entity_id = df.EntityId("Counter", "myCounter")
    instance_id = await client.signal_entity(entity_id, "add", 1)
    return func.HttpResponse("Entity signaled")
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 目前不支持持久性实体。

---

实体函数在 [Durable Functions 2.0](durable-functions-versions.md) 及更高版本中可用于 C#、JavaScript 和 Python。

## <a name="the-technology"></a>技术

在幕后，Durable Functions 扩展构建在 [Durable Task Framework](https://github.com/Azure/durabletask)（GitHub 上的用于在代码中生成工作流的开源库）的基础之上。 如同 Azure Functions 是 Azure WebJobs 的无服务器演进一样，Durable Functions 是 Durable Task Framework 的无服务器演进。 Microsoft 和其他组织广泛使用 Durable Task Framework 来自动处理任务关键型过程。 它天生就很适合无服务器 Azure Functions 环境。

## <a name="code-constraints"></a>代码约束

为了提供可靠且长时间运行的执行保证，业务流程协调程序函数提供一组必须遵循的代码编写规则。 有关详细信息，请参阅[业务流程协调程序函数代码约束](durable-functions-code-constraints.md)一文。

## <a name="billing"></a>计费

Durable Functions 的计费与 Azure Functions 一样。 有关详细信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)。 在 Azure Functions [消耗计划](../consumption-plan.md)中执行业务流程协调程序函数时，需要注意一些计费行为。 有关这些行为的详细信息，请参阅 [Durable Functions 计费](durable-functions-billing.md)一文。

## <a name="jump-right-in"></a>立即投入

可以在不到 10 分钟的时间内开始使用 Durable Functions，只需完成下述某个特定于语言的快速入门教程即可：

* [使用 Visual Studio 2019 的 C#](durable-functions-create-first-csharp.md)
* [使用 Visual Studio Code 的 JavaScript](quickstart-js-vscode.md)
* [Python 使用 Visual Studio Code](quickstart-python-vscode.md)
* [使用 Visual Studio Code 的 PowerShell](quickstart-powershell-vscode.md)

在这些快速入门中，请在本地创建并测试“hello world”持久函数。 然后将函数代码发布到 Azure。 创建的函数将协调对其他函数的调用并将其链接在一起。

## <a name="learn-more"></a>了解详细信息

以下视频重点介绍了 Durable Functions 的优势：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

有关 Durable Functions 和底层技术的更深入介绍，请观看以下视频（重点介绍 .NET，但这些概念同样适用于其他支持的语言）：

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Durable Functions 是 [Azure Functions](../functions-overview.md) 的高级扩展，因此并不适用于所有应用程序。 若要与其他 Azure 业务流程技术进行比较，请参阅[比较 Azure Functions 和 Azure 逻辑应用](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Durable Functions 函数类型和功能](durable-functions-types-features-overview.md)
