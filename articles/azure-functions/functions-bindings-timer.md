---
title: Azure Functions 的计时器触发器
description: 了解如何在 Azure Functions 中使用计时器触发器。
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f74c751f5b725270cfefcf4bab7353df4c498f01
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638374"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions 的计时器触发器

本文介绍如何在 Azure Functions 中使用计时器触发器。 计时器触发器可以按计划运行函数。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

若要了解如何手动运行计时器触发的函数，请参阅[手动运行非 HTTP 触发的函数](./functions-manually-run-non-http.md)。

## <a name="packages---functions-2x-and-higher"></a>包 - Functions 2.x 及更高版本

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 包 3.x 版中提供了计时器触发器。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>包 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 包 2.x 版中提供了计时器触发器。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例显示了一个 [C# 函数](functions-dotnet-class-library.md)，每当分钟的值可被 5 整除时，就执行该函数（例如，如果函数起始于 18:57:00，则下一次执行函数的时间为 19:00:00）。 [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) 对象将传递到函数中。

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。 [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) 对象将传递到函数中。

下面是 function.json 文件中的绑定数据：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# 脚本代码如下所示：

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

以下示例函数的触发和执行间隔为 5 分钟。 函数上的 `@TimerTrigger` 注释使用与 [CRON 表达式](https://en.wikipedia.org/wiki/Cron#CRON_expression)相同的字符串格式定义计划。

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个计时器触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数将写入日志信息，指示调用此函数是由于错过了计划发生时间。 [计时器对象](#usage)将传递到函数中。

下面是 function.json 文件中的绑定数据：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript 代码如下所示：

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.isPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

以下示例演示如何在 [PowerShell](./functions-reference-powershell.md) 中配置用于计时器触发器的 function.json 和 run.ps1 文件。

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

[计时器对象](#usage)的实例将作为第一个参数传递给函数。

# <a name="python"></a>[Python](#tab/python)

以下示例使用计时器触发器绑定，其配置在 *function.json* 文件中进行了描述。 使用绑定的实际 [Python 函数](functions-reference-python.md)在 init.py 文件中进行了描述。 传入函数的对象的类型为 [azure.functions.TimerRequest 对象](/python/api/azure-functions/azure.functions.timerrequest)。 函数逻辑将写入日志，以指示当前调用是由于错过了计划发生时间。

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

下面是 Python 代码：

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)。

该特性的构造函数采用 CRON 表达式或 `TimeSpan`： 仅当函数应用在应用服务计划中运行时才能使用 `TimeSpan`。 消耗计划或弹性高级函数不支持 `TimeSpan`。

以下示例显示了一个 CRON 表达式：

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

函数上的 `@TimerTrigger` 注释使用与 [CRON 表达式](https://en.wikipedia.org/wiki/Cron#CRON_expression)相同的字符串格式定义计划。

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `TimerTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为“timerTrigger”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“in”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 在函数代码中表示计时器对象的变量的名称。 | 
|**schedule**|**ScheduleExpression**|[CRON 表达式](#ncrontab-expressions)或 [TimeSpan](#timespan) 值。 只能对在应用服务计划中运行的函数应用使用 `TimeSpan`。 可以将计划表达式放在应用设置中并将此属性设置为用 **%** 符号括起的应用设置名称，例如此示例中的“%ScheduleAppSetting%”。 |
|**runOnStartup**|**RunOnStartup**|如果为 `true`，则在运行时启动时调用此函数。 例如，当函数应用从由于无活动而进入的空闲状态醒来后，运行时会启动。 当函数应用由于函数更改而重新启动时，以及当函数应用横向扩展时。因此 **runOnStartup** 应该很少设置为 `true`，特别是在生产中。 |
|**useMonitor**|**UseMonitor**|设置为 `true` 或 `false` 以指示是否应当监视计划。 计划监视在各次计划发生后会持续存在，以帮助确保即使在函数应用实例重新启动的情况下也能正确维护计划。 如果未显式设置，则对于重复周期间隔大于或等于 1 分钟的计划，默认值为 `true`。 对于每分钟触发多次的计划，默认值为 `false`。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> 在生产中不建议将 runOnStartup 设置为 `true`。 使用此设置会使代码在非常不可预测的时间执行。 在某些生产设置中，这些额外执行可能会导致消耗计划中托管的应用产生明显更高的成本。 例如，启用 **runOnStartup** 后，只要缩放函数应用，就会调用触发器。 在生产中启用 runOnStartup 之前，请确保完全了解函数的生产行为。

## <a name="usage"></a>使用情况

调用计时器触发器函数时，计时器对象将传递到函数中。 以下 JSON 是计时器对象的示例表示形式。

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

如果当前函数调用晚于计划时间，则 `isPastDue` 属性为 `true`。 例如，函数应用重新启动可能会导致调用被错过。

## <a name="ncrontab-expressions"></a>NCRONTAB 表达式

Azure Functions 使用 [NCronTab](https://github.com/atifaziz/NCrontab) 库来解释 NCRONTAB 表达式。 NCRONTAB 表达式类似于 CRON 表达式，不同之处在于它在开头包含额外的第六个字段，用于以秒为单位的时间精度：

`{second} {minute} {hour} {day} {month} {day-of-week}`

每个字段可以具有下列类型之一的值：

|类型  |示例  |何时触发  |
|---------|---------|---------|
|一个具体值 |<nobr>`0 5 * * * *`</nobr>| 一天中每小时的第 5 分钟 |
|所有值 (`*`)|<nobr>`0 * 5 * * *`</nobr>| 一小时的每一分钟（从第 5 小时开始） |
|一个范围（`-` 运算符）|<nobr>`5-7 * * * * *`</nobr>| 一分钟三次 - 每天每小时的每分钟的第 5 到 7 秒 |
|一组值（`,` 运算符）|<nobr>`5,8,10 * * * * *`</nobr>| 一分钟三次 - 每天每小时的每分钟的第 5、8 和 10 秒 |
|一个间隔值（`/` 运算符）|<nobr>`0 */5 * * * *`</nobr>| 一小时 12 次 - 每天每小时的每 5分钟的第 0 秒 |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB 示例

以下是一些可用于 Azure Functions 中计时器触发器的 NCRONTAB 表达式示例。

| 示例            | 何时触发                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | 每五分钟一次            |
| `0 0 * * * *`      | 每小时一次（在每小时的开头）      |
| `0 0 */2 * * *`    | 每两小时一次               |
| `0 0 9-17 * * *`   | 从上午 9 点到下午 5 点每小时一次  |
| `0 30 9 * * *`     | 每天上午 9:30               |
| `0 30 9 * * 1-5`   | 每个工作日的上午 9:30           |
| `0 30 9 * Jan Mon` | 在一月份每星期一的上午 9:30 |

> [!NOTE]
> NCRONTAB 表达式同时支持五字段和六字段格式 。 第六个字段的位置是秒的值，该值置于表达式的开头。

### <a name="ncrontab-time-zones"></a>NCRONTAB 时区

CRON 表达式中的数字指的是时间和日期，而不是时间跨度。 例如，`hour` 字段中的 5 指的是 5:00 AM，而不是每 5 小时。

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 只能对在应用服务计划中运行的函数应用使用 `TimeSpan`。

与 CRON 表达式不同，`TimeSpan` 值指定各次函数调用之间的时间间隔。 如果函数的运行时间超出了指定的时间间隔，则在函数完成时，计时器会立即再次调用该函数。

以字符串表示，当 `hh` 小于 24 时，`TimeSpan` 格式为 `hh:mm:ss`。 当前两个数字是 24 或更大的数字时，格式为 `dd:hh:mm`。 下面是一些示例：

| 示例      | 何时触发 |
|--------------|----------------|
| "01:00:00"   | 每小时     |
| "00:01:00"   | 每分钟   |
| "25:00:00"   | 每 25 天  |
| "1.00:00:00" | 每天      |

## <a name="scale-out"></a>横向扩展

如果函数应用横向扩展到多个实例，则在所有实例中只会运行由计时器触发的函数的单个实例。 如果有未完成的调用仍在运行，它将不会再次触发。

## <a name="function-apps-sharing-storage"></a>共享同一存储的函数应用

如果要在未部署到应用服务的函数应用之间共享存储帐户，则可能需要为每个应用显式分配主机 ID。

| Functions 版本 | 设置                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x（及更高版本）  | `AzureFunctionsWebHost__hostid` 环境变量 |
| 1.x               | *host.json* 中的 `id`                                  |

可以省略标识值，也可以手动将每个函数应用的标识配置设置为不同的值。

计时器触发器使用存储锁来确保当函数应用横向扩展到多个实例时将只有一个计时器实例。 如果两个函数应用共享相同的标识配置，并且每个函数应用都使用一个计时器触发器，则只有一个计时器运行。

## <a name="retry-behavior"></a>重试行为

与队列触发器不同，计时器触发器在函数失败后不会重试。 函数失败时，在计划的下次时间到来前，不会再次调用该函数。

## <a name="manually-invoke-a-timer-trigger"></a>手动调用计时器触发器

Azure Functions 的计时器触发器提供了 HTTP Webhook，可以通过调用它来手动触发函数。 这在以下方案中非常有用。

* 集成测试
* 槽交换是冒烟测试或预热活动的一部分
* 函数的初始部署，以立即在数据库中填充缓存或查找表

若要详细了解如何手动调用计时器触发的函数，请参阅[手动运行非 HTTP 触发的函数](./functions-manually-run-non-http.md)。

## <a name="troubleshooting"></a>故障排除

有关在计时器触发器未按预期工作的情况下应采取的措施，请参阅[调查和报告有关计时器触发功能未触发的问题](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到有关使用计时器触发器的快速入门](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
