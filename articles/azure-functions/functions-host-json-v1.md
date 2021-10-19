---
title: Azure Functions 1.x 的 host.json 参考
description: 使用 v1 运行时的 Azure Functions host.json 文件的参考文档。
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 5893bf8edc60e6a86aacccff6a798bc63ee655d8
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660602"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Azure Functions 1.x 的 host.json 参考

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [版本 1](functions-host-json-v1.md)
> * [第 2 版](functions-host-json.md)

host.json 元数据文件包含影响函数应用实例中所有函数的配置选项。 本文列出了可用于版本 1.x 运行时的设置。 JSON 架构位于 http://json.schemastore.org/host 。

> [!NOTE]
> 本文适用于 Azure Functions 1.x。  有关 Functions 2.x 及更高版本中的 host.json 参考，请参阅 [Azure Functions 2.x 的 host.json 参考](functions-host-json.md)。

其他函数应用配置选项是在你的[应用设置](functions-app-settings.md)中管理的。

某些 host.json 设置只有在本地运行时才会在 [local.settings.json](functions-develop-local.md#local-settings-file) 文件中使用。

## <a name="sample-hostjson-file"></a>示例 host.json 文件

以下示例 *host.json* 文件指定了所有可能的选项。


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

本文的以下各部分解释了每个顶级属性。 除非另有说明，否则其中的所有属性都是可选的。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

[Azure Cosmos DB 触发器和绑定](functions-bindings-cosmosdb.md)的配置设置。

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|GatewayMode|网关|连接到 Azure Cosmos DB 服务时该函数使用的连接模式。 选项为 `Direct` 和 `Gateway`|
|协议|Https|连接到 Azure Cosmos DB 服务时该函数使用的连接协议。  参阅[此处，了解两种模式的说明](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|不适用|应用中所有函数要使用的租用前缀。|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

[事件中心触发器和绑定](functions-bindings-event-hubs.md#functions-1x)的配置设置。

## <a name="functions"></a>functions

作业主机运行的函数列表。 空数组表示运行所有函数。 仅供在[本地运行](functions-run-local.md)时使用。 在 Azure 的函数应用中，应改为按照[如何在 Azure Functions 中禁用函数](disable-function.md)中的步骤禁用特定函数，而不是使用此设置。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指示所有函数的超时持续时间。 在无服务器消耗计划中，有效范围为 1 秒至 10 分钟，默认值为 5 分钟。 在应用服务计划中，没有总体限制，默认值为 _null_，表示没有超时。

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[主机运行状况监视器](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)的配置设置。

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|enabled|是|指定是否已启用该功能。 | 
|healthCheckInterval|10 秒|定期后台运行状况检查之间的时间间隔。 | 
|healthCheckWindow|2 分钟|与 `healthCheckThreshold` 设置结合使用的滑动时间窗口。| 
|healthCheckThreshold|6|在启动主机回收之前，运行状况检查可以失败的最大次数。| 
|counterThreshold|0.80|性能计数器将被视为不正常的阈值。| 

## <a name="http"></a>http

[http 触发器和绑定](functions-bindings-http-webhook.md)的配置设置。

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|启用时，将为此设置将导致请求处理管道，以定期检查系统性能计数器类似连接/线程/进程/内存/CPU 等，并通过内置的高阈值 (80%)，如果有任何这些计数器请求拒绝与 429“太忙”响应，直至恢复到正常水平的计数器。|
|maxConcurrentRequests|无限制 (`-1`)|将并行执行的 HTTP 函数的最大数目。 这样，可以控制并发性，从而帮助管理资源利用率。 例如，你可能有一个使用大量系统资源（内存/CPU/套接字）的 HTTP 函数，它在并发度太高时会导致问题。 或者，某个函数向第三方服务发出出站请求，则可能需要限制这些调用的速率。 在这种情况下，应用限制可能有帮助。|
|maxOutstandingRequests|无限制 (`-1`)|在任意给定时间搁置的未完成请求数上限。 此限制包括已排队但尚未开始执行的请求，以及正在执行的所有请求。 超出此限制的任何传入请求将被拒绝，并返回 429“太忙”响应。 允许调用方使用基于时间的重试策略，还可帮助控制最大请求延迟。 此设置仅控制脚本宿主执行路径中发生的排队。 其他队列（例如 ASP.NET 请求队列）仍有效，不受此设置的影响。|
|routePrefix|api|应用到所有路由的路由前缀。 使用空字符串可删除默认前缀。 |

## <a name="id"></a>id

作业宿主的唯一 ID。 可以是不带短划线的小写 GUID。 在本地运行时必须指定。 在 Azure 中运行时，建议你不要设置 ID 值。 当省略 `id` 时，会自动在 Azure 中生成 ID。 

如果跨多个函数应用共享存储帐户，请确保每个函数应用都有不同的 `id`。 可以省略 `id` 属性或手动将每个函数应用的 `id` 设置为不同的值。 计时器触发器使用存储锁来确保当函数应用横向扩展到多个实例时将只有一个计时器实例。 如果两个函数应用共享相同的 `id` 且每个都使用计时器触发器，则只会运行一个计时器。

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

控制由 [ILogger](functions-dotnet-class-library.md#ilogger) 对象或 [context.log](functions-reference-node.md#contextlog-method) 写入的日志的筛选。

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|categoryFilter|n/a|指定按类别进行筛选| 
|defaultLevel|信息|对于 `categoryLevels` 数组中未指定的任何类别，会将此级别和更高级别的日志发送到 Application Insights。| 
|categoryLevels|n/a|一个类别数组，指定每个类别的、要发送到 Application Insights 的最低日志级别。 此处指定的类别控制以相同值开头的所有类别，较长的值优先。 在前面的示例 *host.json* 文件中，将在 `Information` 级别记录以“Host.Aggregator”开头的所有类别的日志。 在 `Error` 级别记录以“Host”开头的其他所有类别（例如“Host.Executor”）的日志。| 

## <a name="queues"></a>queues

[存储队列触发器和绑定](functions-bindings-storage-queue.md)的配置设置。

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|maxPollingInterval|60000|队列轮询的最大间隔时间，以毫秒为单位。| 
|visibilityTimeout|0|消息处理失败时的重试间隔时间。| 
|batchSize|16|Functions 运行时同时检索并并行处理的队列消息数。 当处理的数量下降到 `newBatchThreshold` 时，运行时可获取另一个批，并开始处理这些消息。 因此，每个函数处理的最大并发消息数是 `batchSize` 加上 `newBatchThreshold`。 此限制分别应用于各个队列触发的函数。 <br><br>如果要避免对队列上收到的消息并行执行，可以将 `batchSize` 设置为 1。 但是，只有在函数于单个虚拟机 (VM) 上运行时，此设置才可消除并发。 如果函数应用横向扩展到多个 VM，每个 VM 可运行每个队列触发的函数的一个实例。<br><br>`batchSize` 的最大值为 32。 | 
|maxDequeueCount|5|在将某个消息移到有害队列之前，尝试处理该消息的次数。| 
|newBatchThreshold|batchSize/2|只要同时处理的消息数下降到此数值，运行时即检索另一个批次。| 

## <a name="sendgrid"></a>SendGrid

[SendGrind 输出绑定](functions-bindings-sendgrid.md)的配置设置

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
}    
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|from|n/a|所有函数的发件人电子邮件地址。| 

## <a name="servicebus"></a>serviceBus

[服务总线触发器和绑定](functions-bindings-service-bus.md)的配置设置。

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|消息泵应该对回调发起的最大并发调用数。 默认情况下，Functions 运行时同时处理多条消息。 若要指示运行时一次只处理单个队列或主题消息，请将 `maxConcurrentCalls` 设置为 1。 | 
|prefetchCount|不适用|基础 MessageReceiver 将要使用的默认 PrefetchCount。| 
|autoRenewTimeout|00:05:00|自动续订消息锁的最长持续时间。|
|autoComplete|是|如果为 true，则触发器会在成功执行操作后自动完成消息处理。 如果为 false，则函数负责在返回之前完成消息。|

## <a name="singleton"></a>singleton

单一实例锁行为的配置设置。 有关详细信息，请参阅[有关单一实例支持的 GitHub 问题](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)。

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|占用函数级锁的时间段。 锁自动续订。| 
|listenerLockPeriod|00:01:00|占用侦听器锁的时间段。| 
|listenerLockRecoveryPollingInterval|00:01:00|在启动时无法获取侦听器锁的情况下，用于恢复侦听器锁的时间间隔。| 
|lockAcquisitionTimeout|00:01:00|运行时尝试获取锁的最长时间。| 
|lockAcquisitionPollingInterval|不适用|尝试获取锁的间隔时间。| 

## <a name="tracing"></a>tracing

*版本 1.x*

使用 `TraceWriter` 对象创建的日志的配置设置。 若要了解详细信息，请参阅 [C# 日志记录]。

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|consoleLevel|info|控制台日志记录的跟踪级别。 选项包括：`off`、`error`、`warning`、`info` 和 `verbose`。|
|fileLoggingMode|debugOnly|文件日志记录的跟踪级别。 选项包括 `never`、`always` 和 `debugOnly`。| 

## <a name="watchdirectories"></a>watchDirectories

应该监视其更改情况的一组[共享代码目录](functions-reference-csharp.md#watched-directories)。  确保当这些目录中的代码发生更改时，函数会拾取这些更改。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何更新 host.json 文件](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [环境变量中的持久性设置](functions-app-settings.md)
