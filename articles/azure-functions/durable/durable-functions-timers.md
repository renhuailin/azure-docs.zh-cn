---
title: Durable Functions 中的计时器 - Azure
description: 了解如何实现 Azure Functions 的 Durable Functions 扩展中的持久计时器。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 4fcaf265af6eea6d9bef47858742192103777682
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862682"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Durable Functions 中的计时器 (Azure Functions)

[Durable Functions](durable-functions-overview.md) 提供了供在业务流程协调程序函数中使用的“持久计时器”，这些计时器用来为异步操作实现延迟或设置超时。  在业务流程协调程序函数中应当使用持久计时器，而不是使用 `Thread.Sleep` 和 `Task.Delay` (C#) 或 `setTimeout()` 和 `setInterval()` (JavaScript) 或 `time.sleep()` (Python)。

可以调用[业务流程触发器绑定的](durable-functions-bindings.md#orchestration-trigger)[`CreateTimer` (.NET)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.createtimer)、[`createTimer` (JavaScript)](/javascript/api/durable-functions/durableorchestrationcontext#createTimer_Date_)或[`create_timer` (Python)](/python/api/azure-functions-durable/azure.durable_functions.durableorchestrationcontext#create-timer-fire-at--datetime-datetime-----azure-durable-functions-models-task-task) 方法来创建持久计时器。 该方法返回一个将在指定的日期和时间完成的任务。

## <a name="timer-limitations"></a>计时器限制

创建在下午 4:30 过期的计时器时，基础 Durable Task Framework 会将一条仅在下午 4:30 才变得可见的消息排入队列。 当在 Azure Functions 消耗计划中运行时，新近可见的计时器消息将确保在合适的 VM 上激活函数应用。

> [!NOTE]
> * 从持久性扩展的[版本 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) 开始，.NET 应用的持久计时器便不再受限。 对于 JavaScript、Python 和 PowerShell 应用以及使用早期版本扩展的 .NET 应用，持久计时器的时限为七天。 如果使用的是较旧的扩展版本或非 .NET 语言运行时，并且需要超过七天的延迟时间，请在 `while` 循环中使用计时器 API 来模拟更长的延迟。
> * 计算持久计时器的触发时间时，请始终在 .NET 中使用 `CurrentUtcDateTime` 而非 `DateTime.UtcNow`，在 JavaScript 中使用 `currentUtcDateTime` 而非 `Date.now` 或 `Date.UTC`。 有关详细信息，请参阅[业务流程协调程序函数代码约束](durable-functions-code-constraints.md)一文。

## <a name="usage-for-delay"></a>延迟的用法

下面的示例演示了如何使用持久计时器来延迟执行。 示例连续 10 天每天都会发出账单通知。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> 前面的 C# 示例以 Durable Functions 2.x 为目标。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const { DateTime } = require("luxon");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = DateTime.fromJSDate(context.df.currentUtcDateTime, {zone: 'utc'}).plus({ days: 1 });
        yield context.df.createTimer(deadline.toJSDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

for ($num = 0 ; $num -le 9 ; $num++){    
    $expiryTime =  New-TimeSpan -Days 1
    $timerTask = Start-DurableTimer -Duration $expiryTime
    Invoke-DurableActivity -FunctionName 'SendBillingEvent'
}
```
---

> [!WARNING]
> 请避免在业务流程协调程序函数中出现无限循环。 有关如何安全有效地实现无限循环方案的信息，请参阅[永久业务流程](durable-functions-eternal-orchestrations.md)。

## <a name="usage-for-timeout"></a>超时的用法

此示例演示了如何使用持久计时器来实现超时。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> 前面的 C# 示例以 Durable Functions 2.x 为目标。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const { DateTime } = require("luxon");

module.exports = df.orchestrator(function*(context) {
    const deadline = DateTime.fromJSDate(context.df.currentUtcDateTime, {zone: 'utc'}).plus({ seconds: 30 });

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toJSDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
param($Context)

$expiryTime =  New-TimeSpan -Seconds 30

$activityTask = Invoke-DurableActivity -FunctionName 'GetQuote'-NoWait
$timerTask = Start-DurableTimer -Duration $expiryTime -NoWait

$winner = Wait-DurableTask -Task @($activityTask, $timerTask) -Any

if ($winner -eq $activityTask) {
    Stop-DurableTaskTimer -Task $timerTask
    return $True
}
else {
    return $False
}
```

---

> [!WARNING]
> 使用 `CancellationTokenSource` (.NET) 或对返回的 `TimerTask` (JavaScript) 调用 `cancel()` 来取消持久计时器（如果代码不会等待它完成）。 在所有未完成任务都完成或取消之前，Durable Task Framework 不会将业务流程的状态更改为“已完成”。

此取消机制不会终止正在进行的活动函数执行或子业务流程执行。 它只是允许业务流程协调程序函数忽略结果并继续运行。 如果函数应用使用了消耗计划，则还需要为已放弃的活动函数消耗的任何时间和内存付费。 默认情况下，在消耗计划中运行的函数有五分钟的超时。 如果超出了此限制，则会回收 Azure Functions 主机以停止所有执行并防止出现费用失控的情况。 [函数超时是可配置的](../functions-host-json.md#functiontimeout)。

有关如何在业务流程协调程序函数中实现超时的更深入示例，请参阅[人机交互和超时 - 电话验证](durable-functions-phone-verification.md)一文。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何引发和处理外部事件](durable-functions-external-events.md)
