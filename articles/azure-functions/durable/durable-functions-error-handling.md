---
title: 处理 Durable Functions 中的错误 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中处理错误。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 8ffa633479189ad8391d4c03c59113dc957d77e6
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667128"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>处理 Durable Functions 中的错误 (Azure Functions)

Durable Function 业务流程采用代码实现，并可使用编程语言的内置错误处理功能。 实际上，不需要学习任何新概念，就可以将错误处理和补偿添加到业务流程中。 但应注意以下事项。

## <a name="errors-in-activity-functions"></a>活动函数中的错误

活动函数中引发的任何异常都将封送回业务流程协调程序函数，并作为 `FunctionFailedException` 引发。 可在业务流程协调程序函数中编写满足需要的错误处理和补偿代码。

例如，考虑使用以下业务流程协调程序函数，将一个帐户中的资金转到另一帐户：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> 前面的 C# 示例适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)
$transferDetails = $Context.Input

Invoke-DurableActivity -FunctionName 'DebitAccount' -Input @{ account = transferDetails.sourceAccount; amount = transferDetails.amount }

try {
    Invoke-DurableActivity -FunctionName 'CreditAccount' -Input @{ account = transferDetails.destinationAccount; amount = transferDetails.amount }
} catch {
    Invoke-DurableActivity -FunctionName 'CreditAccount' -Input @{ account = transferDetails.sourceAccount; amount = transferDetails.amount }
}
```


---

如果第一个 **CreditAccount** 函数调用失败，业务流程协调程序函数将通过将资金贷记回源帐户进行补偿。

## <a name="automatic-retry-on-failure"></a>失败时自动重试

调用活动函数或子业务流程函数时，可指定自动重试策略。 以下示例尝试调用某函数多达三次，每次重试之间等待 5 秒：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> 前面的 C# 示例适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$retryOptions = New-DurableRetryOptions `
                    -FirstRetryInterval (New-Timespan -Seconds 5) `
                    -MaxNumberOfAttempts 3

Invoke-DurableActivity -FunctionName 'FlakyFunction' -RetryOptions $retryOptions
```

---

上一示例中的活动函数调用使用一个参数来配置自动重试策略。 可通过多种选项自定义自动重试策略：

* **最大尝试次数**：尝试的最大次数。 如果设置为 1，则不会重试。
* **首次重试间隔**：首次尝试重试前需要等待的时间。
* **回退系数**：用来确定回退增加速率的系数。 默认值为 1。
* **最大重试间隔**：尝试重试之间需要等待的最长时间。
* **重试超时**：执行重试所花费的最长时间。 默认行为是可无限期重试。
* **处理**：可以指定用户定义的回叫来确定是否应该重试函数。 

> [!NOTE]
> JavaScript 中的 Durable Functions 当前不支持用户定义的回叫 (`context.df.RetryOptions`)。


## <a name="function-timeouts"></a>函数超时

如果业务流程协调程序函数内的函数调用耗时太长才能完成，建议放弃该函数调用。 执行此操作的正确方法是将 `context.CreateTimer` (.NET)、`context.df.createTimer` (JavaScript) 或 `context.create_timer` (Python) 与 `Task.WhenAny` (.NET)、`context.df.Task.any` (JavaScript) 或 `context.task_any` (Python) 结合使用，创建[持久计时器](durable-functions-timers.md)，如下例中所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> 前面的 C# 示例适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
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
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$expiryTime =  New-TimeSpan -Seconds 30

$activityTask = Invoke-DurableActivity -FunctionName 'FlakyFunction'-NoWait
$timerTask = Start-DurableTimer -Duration $expiryTime -NoWait

$winner = Wait-DurableTask -Task @($activityTask, $timerTask) -NoWait

if ($winner -eq $activityTask) {
    Stop-DurableTimerTask -Task $timerTask
    return $True
}
else {
    return $False
}
```

---

> [!NOTE]
> 此机制实际上不会终止正在进行的活动函数执行。 它只是允许业务流程协调程序函数忽略结果并继续运行。 有关详细信息，请参阅[计时器](durable-functions-timers.md#usage-for-timeout)文档。

## <a name="unhandled-exceptions"></a>未经处理的异常

如果业务流程协调程序函数失败，出现未经处理的异常，则会记录异常的详细信息，且实例的完成状态为 `Failed`。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解永久业务流程](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何诊断问题](durable-functions-diagnostics.md)
