---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356187"
---
Azure Functions 中引发的错误可能来自以下任一来源：

- 使用内置 Azure Functions [触发器和绑定](..\articles\azure-functions\functions-triggers-bindings.md)
- 调用底层 Azure 服务的 API
- 调用 REST 终结点
- 调用客户端库、包或第三方 API

遵循良好的错误处理方法对于避免丢失数据或丢失消息很重要。 建议的错误处理做法包括下列操作：

- [启用 Application Insights](../articles/azure-functions/functions-monitoring.md)
- [使用结构化错误处理](#use-structured-error-handling)
- [幂等性设计](../articles/azure-functions/functions-idempotent.md)
- [实施重试策略](#retry-policies-preview)（如果适用）

### <a name="use-structured-error-handling"></a>使用结构化错误处理

捕获和记录错误对于监视应用程序的运行状况非常重要。 任何函数代码的最顶层应包含 try/catch 块。 在 catch 块中，可以捕获和记录错误。

## <a name="retry-policies-preview"></a>重试策略 (预览) 

可在函数应用中任何触发器类型的任何函数上定义重试策略。  重试策略会重新执行函数，直到成功执行或发生最大重试次数。  可以为应用中的所有函数或单个函数定义重试策略。  默认情况下，函数应用将不会重试 (来自 [触发器源) 上具有重试策略的特定触发器](#using-retry-support-on-top-of-trigger-resilience) 的消息。  每当执行导致未捕获的异常时，都会计算重试策略。  最佳做法是，应捕获代码中的所有异常，并再次引发应该导致重试的任何错误。  在完成执行的重试策略之前，不会写入事件中心和 Azure Cosmos DB 检查点，这意味着在完成当前批处理之前，将暂停该分区上的进度。

### <a name="retry-policy-options"></a>重试策略选项

以下选项可用于定义重试策略。

**最大重试次数** 是在最终失败之前重试执行的最大次数。 值为 `-1` 表示无限期重试。 当前重试计数存储在实例的内存中。 实例可能在重试尝试之间出现故障。  当实例在重试策略过程中失败时，重试计数将丢失。  出现实例失败时，事件中心、Azure Cosmos DB 和队列存储等触发器能够恢复处理，并在新的实例上重试该批，并将重试计数重置为零。  其他触发器（如 HTTP 和计时器）不会在新的实例上恢复。  这意味着最大重试次数是最大努力，在极少数情况下，执行可能会重试超过最大值，或重试次数小于最大值的触发器。

**重试策略** 控制重试的行为方式。  下面是两个受支持的重试选项：

| 选项 | 描述|
|---|---|
|**`fixedDelay`**| 允许在每次重试之间经过指定的时间量|
| **`exponentialBackoff`**| 第一次重试会等待最低延迟。 后续重试时，时间以指数方式添加到每次重试的初始持续时间，直到达到最大延迟。  指数重试增加了一些小的随机化，可延迟在高吞吐量方案中错开重试。|

#### <a name="app-level-configuration"></a>应用级别配置

可以 [使用 `host.json` 文件](../articles/azure-functions/functions-host-json.md#retry)为应用中的所有函数定义重试策略。 

#### <a name="function-level-configuration"></a>函数级别配置

可以为特定函数定义重试策略。  特定于功能的配置优先于应用级配置。

#### <a name="fixed-delay-retry"></a>固定延迟重试

# <a name="c"></a>[C#](#tab/csharp)

重试需要 NuGet [>包 3.0.23](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) =

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

下面是文件中 *function.js* 的重试策略：

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

下面是文件中 *function.js* 的重试策略：


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

下面是文件中 *function.js* 的重试策略：

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

下面是文件中 *function.js* 的重试策略：


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

下面是文件中 *function.js* 的重试策略：


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

#### <a name="exponential-backoff-retry"></a>指数回退重试

# <a name="c"></a>[C#](#tab/csharp)

重试需要 NuGet [>包 3.0.23](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) =

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

下面是文件中 *function.js* 的重试策略：

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

下面是文件中 *function.js* 的重试策略：

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

下面是文件中 *function.js* 的重试策略：

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

下面是文件中 *function.js* 的重试策略：

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

下面是文件中 *function.js* 的重试策略：

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
|delayInterval|不适用|使用策略时，两次重试之间的延迟 `fixedDelay` 。|
|minimumInterval|不适用|使用 `exponentialBackoff` 策略时的最小重试延迟。|
|maximumInterval|不适用|使用 `exponentialBackoff` 策略时的最大重试延迟。| 

### <a name="retry-limitations-during-preview"></a>预览期间的重试限制

- 对于 .NET 项目，可能需要手动拉取版本的 [3.0.23 >=](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) 。
- 在消耗计划中，应用可在重试队列中的最后一条消息时缩小到零。
- 在消耗计划中，应用在执行重试时可能会缩小。  为获得最佳结果，请选择重试间隔 <= 00:01:00，并 <= 5 次重试。

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>在触发器复原能力顶部使用重试支持

函数应用重试策略与触发器提供的任何重试或复原无关。  函数重试策略只会在触发弹性重试的基础上进行分层。  例如，如果使用 Azure 服务总线，则默认情况下队列的消息传递计数为10。  默认传递计数是指在10次尝试传递队列消息后，服务总线会将消息死信。  你可以为包含服务总线触发器的函数定义重试策略，但重试会在服务总线传递尝试的基础上进行层级重试。  

例如，如果你使用了默认的服务总线传递计数10，并定义了函数重试策略5。  消息将首先取消排队，并将服务总线传递帐户递增到1。  如果每个执行都失败，则在5次尝试触发同一消息后，该消息将被标记为已放弃。  服务总线会立即重新排队消息，它将触发函数并将传递计数递增到2。  最后，50最终尝试 (10 个 service bus 传递 * 每个传递) 5 次函数重试，将放弃该消息并在服务总线上触发死信。

> [!WARNING]
> 建议不要将触发器（如服务总线队列）的传递计数设置为1，这意味着在单个函数重试循环后，消息将立即死信。  这是因为，触发器通过重试提供了复原能力，而函数重试策略是最有效的，可能导致的重试次数小于所需的总次数。

### <a name="triggers-with-additional-resiliency-or-retries"></a>具有额外复原或重试的触发器

以下触发器支持在触发器源处重试：

* [Azure Blob 存储](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 队列存储](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure 服务总线（队列/主题）](../articles/azure-functions/functions-bindings-service-bus.md)

默认情况下，大多数触发器最多会重试五次。 第五次重试后，Azure 队列存储将向 [病毒队列](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)写入一条消息。  尝试10次后，默认的服务总线队列和主题策略会将消息写入 [死信队列](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) 。
