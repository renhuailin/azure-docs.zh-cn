---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 740f1e3bd8c08ae7d1684613d1920cffd1bba619
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129237286"
---
Azure Functions 中引发的错误可能来自以下任一来源：

- 使用内置 Azure Functions [触发器和绑定](..\articles\azure-functions\functions-triggers-bindings.md)
- 调用底层 Azure 服务的 API
- 调用 REST 终结点
- 调用客户端库、包或第三方 API

遵循良好的错误处理做法对于避免数据丢失或消息遗漏非常重要。 建议的错误处理做法包括下列操作：

- [启用 Application Insights](../articles/azure-functions/functions-monitoring.md)
- [使用结构化错误处理](#use-structured-error-handling)
- [幂等性设计](../articles/azure-functions/functions-idempotent.md)
- [实施重试策略](#retry-policies-preview)（如果适用）

### <a name="use-structured-error-handling"></a>使用结构化错误处理

捕获和记录错误对于监视应用程序的运行状况至关重要。 任何函数代码的最顶层应包含 try/catch 块。 在 catch 块中，可以捕获并记录错误。

## <a name="retry-policies-preview"></a>重试策略（预览版）

可以在函数应用中的任何函数上针对任何触发器类型定义重试策略。  重试策略会反复执行函数，直到成功执行了函数或达到最大重试次数。  可以为应用中的所有函数或单个函数定义重试策略。  默认情况下，函数应用不会重试消息（除非[在触发器源上设置了重试策略的特定触发器](#using-retry-support-on-top-of-trigger-resilience)）。  每当某个执行导致未捕获的异常时，都会对重试策略进行评估。  最佳做法是捕获代码中的所有异常，并再次引发会导致重试的任何错误。  在执行的重试策略完成之前，不会写入事件中心和 Azure Cosmos DB 检查点，这意味着在完成当前批次之前，会暂停该分区上的进度。

### <a name="retry-policy-options"></a>重试策略选项

以下选项可用于定义重试策略。

“最大重试计数”是在最终失败之前重试执行的最大次数。 值为 `-1` 表示重试无限次数。 当前重试计数存储在实例的内存中。 实例可能在重试尝试之间出现故障。  当实例在重试策略实施过程中发生故障时，重试计数会丢失。  出现实例故障时，事件中心、Azure Cosmos DB 和队列存储等触发器能够恢复处理，在新实例上重试该批次，并将重试计数重置为零。  其他触发器（如 HTTP 和计时器）不会在新实例上恢复。  这意味着系统只能尽力完成最大重试次数。在某些罕见情况下，执行的重试次数可能会超过最大值，而对于 HTTP 和计时器之类的触发器，重试次数可能会小于最大值。

“重试策略”控制重试的行为方式。  下面是两个支持的重试选项：

| 选项 | 描述|
|---|---|
|**`fixedDelay`**| 允许在指定的时间过后再进行每次重试。|
| **`exponentialBackoff`**| 首次重试等待的时间（即延迟）最短。 进行后续重试时，对于每次重试，都会以指数方式向初始持续时间添加时间量，直到达到最大延迟。  指数退避使延迟略微随机化，以便在高吞吐量方案中可以错开重试。|

#### <a name="app-level-configuration"></a>应用级配置

可以[使用 `host.json` 文件](../articles/azure-functions/functions-host-json.md#retry)为应用中的所有函数定义重试策略。 

#### <a name="function-level-configuration"></a>函数级配置

可以为特定函数定义重试策略。  特定于函数的配置优先于应用级配置。

#### <a name="fixed-delay-retry"></a>固定延迟重试

# <a name="c"></a>[C#](#tab/csharp)

重试需要 NuGet 包 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

下面是 function.json 文件中的重试策略：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

下面是 function.json 文件中的重试策略：


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

下面是 function.json 文件中的重试策略：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

下面是 function.json 文件中的重试策略：


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

下面是 function.json 文件中的重试策略：


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>指数退避重试

# <a name="c"></a>[C#](#tab/csharp)

重试需要 NuGet 包 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

下面是 function.json 文件中的重试策略：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下面是 function.json 文件中的重试策略：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

下面是 function.json 文件中的重试策略：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

下面是 function.json 文件中的重试策略：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

下面是 function.json 文件中的重试策略：

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.json 属性  |属性 | 描述 |
|---------|---------|---------| 
|strategy|不适用|必需。 要使用的重试策略。 有效值为 `fixedDelay` or `exponentialBackoff`进行求值的基于 SQL 语言的筛选器表达式。|
|maxRetryCount|不适用|必需。 每个函数执行允许的最大重试次数。 `-1` 表示无限重试。|
|delayInterval|不适用|使用 `fixedDelay` 策略时在重试之间使用的延迟。 指定为一个格式为 `HH:mm:ss` 的字符串。|
|minimumInterval|不适用|使用 `exponentialBackoff` 策略时的最小重试延迟。 指定为一个格式为 `HH:mm:ss` 的字符串。|
|maximumInterval|不适用|使用 `exponentialBackoff` 策略时的最大重试延迟。 指定为一个格式为 `HH:mm:ss` 的字符串。| 

### <a name="retry-limitations-during-preview"></a>预览期间的重试限制

- 对于 .NET 项目，可能需要手动拉入版本不低于 3.0.23 的 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)。
- 在消耗计划中，应用可能会在重试队列中最后的消息时纵向缩减到零。
- 在消耗计划中，应用可能会在执行重试时纵向缩减。  为获得最佳结果，请选择一个小于或等于 00:01:00 的重试间隔，以及小于或等于 5 的重试次数。

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>在触发器复原能力的基础上使用重试支持

函数应用重试策略独立于触发器提供的任何重试或复原能力。  函数重试策略所作用的层次只会在触发器可复原重试所作用的层次之上。  例如，如果使用 Azure 服务总线，则默认情况下队列的消息传递计数为 10。  默认传递计数是指在尝试传递队列消息 10 次后，服务总线会将该消息视为死信。  你可以为包含服务总线触发器的函数定义重试策略，但重试会在服务总线传递尝试次数的基础上发挥作用。  

例如，如果你使用的默认服务总线传递计数为 10，并定义了函数重试策略 5。  消息会先取消排队，将服务总线传递计数递增到 1。  如果每个执行都失败，则在 5 次尝试触发同一消息后，该消息会被标记为已放弃。  服务总线会立即将消息重新排队，它会触发函数并将传递计数递增到 2。  最后，在 50 次最终尝试（10 次服务总线传递 * 每次传递时进行的 5 次函数重试）后，消息会被放弃，并在服务总线上触发死信。

> [!WARNING]
> 建议不要将触发器（如服务总线队列）的传递计数设置为 1，这意味着在单次函数重试循环后，消息会被立即视为死信。  这是因为，触发器通过重试提供复原能力，而对于函数重试策略，系统只能做到“尽量遵循”，因此可能导致重试次数少于所需的重试总次数。

### <a name="triggers-with-additional-resiliency-or-retries"></a>具有额外的复原能力或重试次数的触发器

以下触发器支持触发器源处的重试：

* [Azure Blob 存储](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 队列存储](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服务总线（队列/主题）](../articles/azure-functions/functions-bindings-service-bus.md)

默认情况下，多数触发器最多重试请求五次。 第五次重试后，Azure 队列存储会将消息写入[有害队列](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)。  默认的服务总线队列和主题策略会在尝试 10 次后将消息写入[死信队列](../articles/service-bus-messaging/service-bus-dead-letter-queues.md)。
