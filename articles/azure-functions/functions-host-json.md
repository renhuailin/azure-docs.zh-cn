---
title: Azure Functions 2.x 的 host.json 参考
description: 使用 v2 运行时的 Azure Functions host.json 文件的参考文档。
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: b646c4d263896e1bf4d63bdaf965209c005b8228
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742652"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 及更高版本的 host.json 参考 

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [版本 1](functions-host-json-v1.md)
> * [版本 2+](functions-host-json.md)

*host.json* 元数据文件包含对函数应用的所有函数产生影响的全局配置选项。 本文列出了从 Azure Functions 运行时 2.x 版开始可用的设置。  

> [!NOTE]
> 本文适用于 Azure Functions 2.x 及更高版本。  有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md)。

其他函数应用配置选项在[应用设置](functions-app-settings.md)（适用于已部署的应用）或 [local.settings.json](functions-develop-local.md#local-settings-file) 文件（用于本地开发）中进行管理。

host.json 中与绑定相关的配置将同样地应用于函数应用中的每个函数。 

还可以使用应用程序设置来[按环境覆盖或应用设置](#override-hostjson-values)。

## <a name="sample-hostjson-file"></a>示例 host.json 文件

以下版本 2.x+ 的示例 host.json 文件指定了所有可能的选项（不包括仅供内部使用的任何选项）。

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "blobs": {},
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```

本文的以下各部分解释了每个顶级属性。 除非另有说明，否则其中的所有属性都是可选的。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

此设置是[日志记录](#logging)的子项。

Application Insights 的控制选项，包括[采样选项](./configure-monitoring.md#configure-sampling)。

若要了解完整的 JSON 结构，请参阅前面的[示例 host.json 文件](#sample-hostjson-file)。

> [!NOTE]
> 日志采样可能会导致一些执行不会显示在 Application Insights 监视器边栏选项卡中。 若要避免日志采样，请将 `excludedTypes: "Request"` 添加到 `samplingSettings` 值。

| 属性 | 默认 | 说明 |
| --------- | --------- | --------- | 
| samplingSettings | 不适用 | 请参阅 [applicationInsights.samplingSettings](#applicationinsightssamplingsettings)。 |
| enableLiveMetrics | true | 启用实时指标收集。 |
| enableDependencyTracking | true | 自定义依赖项跟踪。 |
| enablePerformanceCountersCollection | true | 启用 Kudu 性能计数器收集。 |
| liveMetricsInitializationDelay | 00:00:15 | 仅限内部使用。 |
| httpAutoCollectionOptions | 不适用 | 请参阅 [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions)。 |
| snapshotConfiguration | 不适用 | 请参阅 [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration)。 |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

有关这些设置的详细信息，请参阅 [Application Insights 中的采样](../azure-monitor/app/sampling.md)。 

|属性 | 默认 | 说明 |
| --------- | --------- | --------- | 
| isEnabled | true | 启用或禁用采样。 | 
| maxTelemetryItemsPerSecond | 20 | 每个服务器主机上每秒记录的遥测项的目标数目。 如果应用在多个主机上运行，请将此值降低至总体目标流量率的范围内。 | 
| evaluationInterval | 01:00:00 | 会重新评估当前遥测速率的间隔。 评估以移动平均线形式进行执行。 可能想要缩短此间隔（如果遥测很容易就激增）。 |
| initialSamplingPercentage| 100.0 | 采样过程开始时应用的初始采样百分比，以动态改变百分比。 不要在调试时减小值。 |
| samplingPercentageIncreaseTimeout | 00:00:01 | 采样百分比值更改时，此属性确定多久之后允许 Application Insights 再次提升采样百分比以捕获更多数据。 |
| samplingPercentageDecreaseTimeout | 00:00:01 | 采样百分比值更改时，此属性确定多久之后允许 Application Insights 再次降低采样百分比以捕获更少数据。 |
| minSamplingPercentage | 0.1 | 随着采样百分比变化，此属性确定允许的最小采样百分比。 |
| maxSamplingPercentage | 100.0 | 随着采样百分比变化，此属性确定允许的最大采样百分比。 |
| movingAverageRatio | 1.0 | 在移动平均线的计算中，权重分配给最新的值。 使用等于或小于 1 的值。 较小的值会使算法不易受突然的更改影响。 |
| excludedTypes | null | 不要采样的分号分隔类型列表。 承认的类型为：`Dependency`、`Event`、`Exception`、`PageView`、`Request` 和 `Trace`。 指定类型的所有实例都会传输；对未指定的类型进行采样。 |
| includedTypes | null | 要采样的分号分隔类型列表；空列表暗示所有类型。 `excludedTypes` 中列出的类型替代此处列出的类型。 承认的类型为：`Dependency`、`Event`、`Exception`、`PageView`、`Request` 和 `Trace`。 指定类型的实例都会采样；未指定或未暗示的类型将进行传输而不采样。 |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|属性 | 默认 | 说明 |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | 启用或禁用 HTTP 触发器的扩展 HTTP 请求信息：传入请求关联标头、多检测密钥支持、HTTP 方法、路径及响应。 |
| enableW3CDistributedTracing | true | 启用或禁用对 W3C 分布式跟踪协议的支持（并开启旧版关联架构）。 如果 `enableHttpTriggerExtendedInfoCollection` 为 true，则默认启用。 如果 `enableHttpTriggerExtendedInfoCollection` 为 false，则此标志仅适用于传出请求，而不适用于传入请求。 |
| enableResponseHeaderInjection | true | 启用或禁用将多组件关联标头注入响应。 启用注入将在使用多个检测密钥时允许 Application Insights 构造应用程序映射。 如果 `enableHttpTriggerExtendedInfoCollection` 为 true，则默认启用。 如果 `enableHttpTriggerExtendedInfoCollection` 为 false，则此设置不适用。 |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguration

有关快照的详细信息，请参阅 [.NET 应用中发生异常时调试快照](../azure-monitor/app/snapshot-debugger.md)和[排查启用 Application Insights Snapshot Debugger 或查看快照时遇到的问题](../azure-monitor/app/snapshot-debugger-troubleshoot.md)。

|属性 | 默认 | 说明 |
| --------- | --------- | --------- | 
| agentEndpoint | null | 用于连接到 Application Insights Snapshot Debugger 服务的终结点。 如果为 null，则使用默认终结点。 |
| captureSnapshotMemoryWeight | 0.5 | 检查是否有足够内存来拍摄快照时，为当前进程内存大小指定的权重。 预期值为大于 0 的正确分数 (0 < CaptureSnapshotMemoryWeight < 1)。 |
| failedRequestLimit | 3 | 禁用遥测处理器之前请求快照的失败请求数限制。|
| handleUntrackedExceptions | true | 启用或禁用不由 Application Insights 遥测跟踪的异常跟踪。 |
| isEnabled | true | 启用或禁用快照收集 | 
| isEnabledInDeveloperMode | false | 启用或禁用在开发人员模式下启用快照收集。 |
| isEnabledWhenProfiling | true | 启用或禁用快照创建，即使 Application Insights Profiler 正在收集详细的分析会话。 |
| isExceptionSnappointsEnabled | false | 启用或禁用异常筛选。 |
| isLowPrioritySnapshotUploader | true | 确定是否使用低于正常优先级运行 SnapshotUploader 进程。 |
| maximumCollectionPlanSize | 50 | 在任何时候可以跟踪的最大问题数，范围为 1 到 9999。 |
| maximumSnapshotsRequired | 3 | 为单个问题收集的最大快照数，范围为 1 到 999。 应用程序中可将问题视为单个 throw 语句。 为问题收集的快照数达到此值后，将不再为该问题收集更多的快照直到重置问题计数器（请参阅 `problemCounterResetInterval`）并再次达到 `thresholdForSnapshotting` 限制。 |
| problemCounterResetInterval | 24:00:00 | 重置问题计数器的频率，范围为一分钟到七天。 达到此间隔时间后，所有问题计数都将重置为零。 已达到拍摄快照的阈值但尚未在 `maximumSnapshotsRequired` 中生成快照数的现有问题仍处于活动状态。 |
| provideAnonymousTelemetry | true | 确定是否将匿名使用情况和错误的遥测数据发送给 Microsoft。 如果与 Microsoft 联系以通过 Snapshot Debugger 帮助解决问题，则可以使用此遥测数据。 它还用于监视使用模式。 |
| reconnectInterval | 00:15:00 | 重新连接到 Snapshot Debugger 终结点的频率。 允许的范围是一分钟到一天。 |
| shadowCopyFolder | null | 指定用于影子副本二进制文件的文件夹。 如未设置，则按顺序尝试下列环境变量指定的文件夹：Fabric_Folder_App_Temp、LOCALAPPDATA、APPDATA、TEMP。 |
| shareUploaderProcess | true | 如果为 true，则只有一个 SnapshotUploader 实例将为共享 InstrumentationKey 的多个应用收集和上传快照。 如果设为 false，则 SnapshotUploader 对于每个 (ProcessName, InstrumentationKey) 元组都是唯一的。 |
| snapshotInLowPriorityThread | true | 确定是否在低 IO 优先级线程中处理快照。 创建快照是一个快速操作，但为了将快照上传到 Snapshot Debugger 服务，必须首先将其以小型转储的形式写入磁盘。 这在 SnapshotUploader 进程中发生。 如果将此值设为 true，则使用低优先级 IO 来写入小型转储，这不会与应用程序争用资源。 如果将此值设为 false，会加速创建小型转储，同时降低应用程序的运行速度。 |
| snapshotsPerDayLimit | 30 | 一天（24 小时）中允许的最大快照数。 此限制还强制用于 Application Insights 服务端。 上传速率限制为每个应用程序（即每个检测密钥）每天 50 个。 此值有助于阻止创建在上传过程中最终会遭到拒绝的其他快照。 如果此值为零则完全删除限制，不建议这样做。 |
| snapshotsPerTenMinutesLimit | 1 | 10 分钟内允许的最大快照数。 尽管此值没有上限，但由于它可能影响应用程序性能，因此在生产工作负荷中谨慎增加它。 创建快照会很快，但创建快照的小型转储并将其上传到 Snapshot Debugger 服务则慢得多，它将与应用程序争用资源（CPU 和 I/O）。 |
| tempFolder | null | 指定用于写入小型转储并上传日志文件的文件夹。 如未设置，则使用 %TEMP%\Dumps。 |
| thresholdForSnapshotting | 1 | Application Insights 请求快照之前需要查看异常的次数。 |
| uploaderProxy | null | 替代 Snapshot Uploader 进程中使用的代理服务器。 如果应用程序通过代理服务器连接到 Internet，则可能需要使用此设置。 Snapshot Collector 在应用程序的进程内运行，并使用相同的代理设置。 但是，Snapshot Uploader 作为单独的进程运行，并且你可能需要手动配置代理服务器。 如果此值为 null，Snapshot Collector 将尝试通过检查 System.Net.WebRequest.DefaultWebProxy 并将值传递到 Snapshot Uploader 来自动检测代理的地址。 如果此值不为 null，则不会使用自动检测，并且将在 Snapshot Uploader 中使用此处指定的代理服务器。 |

## <a name="blobs"></a>Blob

可在[存储 Blob 触发器和绑定](functions-bindings-storage-blob.md#hostjson-settings)中查找配置设置。  

## <a name="console"></a>控制台

此设置是[日志记录](#logging)的子项。 它在未处于调试模式时控制控制台日志记录。

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": false,
          "DisableColors": true
        },
    ...
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|DisableColors|false| 在 Linux 上禁用容器日志的日志格式设置。 如果在 Linux 上运行时希望在容器日志中看到不需要的 ANSI 控制字符，则设置为 true。 |
|isEnabled|false|启用或禁用控制台日志记录。| 

## <a name="cosmosdb"></a>CosmosDB

可在 [Cosmos DB 触发器和绑定](functions-bindings-cosmosdb-v2-output.md#host-json)中查找配置设置。

## <a name="customhandler"></a>customHandler

自定义处理程序的配置设置。 有关详细信息，请参阅 [Azure Functions 自定义处理程序](functions-custom-handlers.md#configuration)。

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|属性 | 默认 | 说明 |
| --------- | --------- | --------- |
| defaultExecutablePath | 不适用 | 要作为自定义处理程序进程启动的可执行文件。 在使用自定义处理程序时，它是必需设置，并且其值与函数应用根有关。 |
| workingDirectory | 函数应用根 | 要在其中启动自定义处理程序进程的工作目录。 它是一个可选设置，其值与函数应用根有关。 |
| 参数 | 不适用 | 要传递给自定义处理程序进程的命令行参数的数组。 |
| enableForwardingHttpRequest | false | 如果设置，则仅包含 HTTP 触发器和 HTTP 输出的所有函数都将转发原始 HTTP 请求而不是自定义处理程序[请求负载](functions-custom-handlers.md#request-payload)。 |

## <a name="durabletask"></a>durableTask

可在 [Durable Functions 的绑定](durable/durable-functions-bindings.md#host-json)中查找配置设置。

## <a name="eventhub"></a>eventHub

可在[事件中心触发器和绑定](functions-bindings-event-hubs.md#host-json)中查找配置设置。 

## <a name="extensions"></a>扩展

返回包含所有特定于绑定的设置的对象的属性，例如 [http](#http) 和 [eventHub](#eventhub)。

## <a name="extensionbundle"></a>extensionBundle 

使用扩展捆绑可将一组兼容的 Functions 绑定扩展添加到函数应用。 若要了解详细信息，请参阅[用于本地开发的扩展捆绑](functions-bindings-register.md#extension-bundles)。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

作业主机运行的函数列表。 空数组表示运行所有函数。 仅供在[本地运行](functions-run-local.md)时使用。 在 Azure 的函数应用中，应改为按照[如何在 Azure Functions 中禁用函数](disable-function.md)中的步骤禁用特定函数，而不是使用此设置。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指示所有函数的超时持续时间。 它采用 timespan 字符串格式。 

| 计划类型 | 默认值（分钟） | 最大值（分钟） |
| -- | -- | -- |
| 消耗 | 5 | 10 |
| 高级<sup>1</sup> | 30 | -1（无限制）<sup>2</sup> |
| 专用（应用服务） | 30 | -1（无限制）<sup>2</sup> |

<sup>1</sup> 高级计划执行只能保证 60 分钟，但技术上不限时长。   
<sup>2</sup> 值为 `-1` 表示无限制执行，但建议保留固定上限。

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

可在 [http 触发器和绑定](functions-bindings-http-webhook-output.md#hostjson-settings)中查找配置设置。

## <a name="logging"></a>日志记录

控制函数应用（包括 Application Insights）的日志记录行为。

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|fileLoggingMode|debugOnly|定义启用哪种级别的文件日志记录。  选项包括 `never`、`always` 和 `debugOnly`。 |
|logLevel|不适用|一个对象，它定义了用于筛选应用中的函数的日志类别。 此设置允许你筛选特定函数的日志记录。 有关详细信息，请参阅[配置日志级别](configure-monitoring.md#configure-log-levels)。 |
|控制台|不适用| [控制台](#console)日志记录设置。 |
|applicationInsights|不适用| [applicationInsights](#applicationinsights) 设置。 |

## <a name="manageddependency"></a>managedDependency

托管依赖项是一项功能，目前仅支持基于 PowerShell 的函数。 它使依赖项可以由服务自动管理。 `enabled` 属性设置为 `true` 时，`requirements.psd1` 文件会被处理。 发布任何次要版本时会更新依赖项。 有关详细信息，请参阅 PowerShell 文章中的[托管依赖项](functions-reference-powershell.md#dependency-management)。

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

可在[存储队列触发器和绑定](functions-bindings-storage-queue.md#host-json)中查找设置。  

## <a name="retry"></a>retry

控制应用中所有执行的[重试策略](./functions-bindings-error-pages.md#retry-policies-preview)选项。

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|properties  |默认 | 描述 |
|---------|---------|---------| 
|strategy|null|必需。 要使用的重试策略。 有效值为 `fixedDelay` or `exponentialBackoff`进行求值的基于 SQL 语言的筛选器表达式。|
|maxRetryCount|null|必需。 每个函数执行允许的最大重试次数。 `-1` 表示无限重试。|
|delayInterval|null|使用 `fixedDelay` 策略时在重试之间使用的延迟。|
|minimumInterval|null|使用 `exponentialBackoff` 策略时的最小重试延迟。|
|maximumInterval|null|使用 `exponentialBackoff` 策略时的最大重试延迟。| 

## <a name="sendgrid"></a>SendGrid

可在 [SendGrid 触发器和绑定](functions-bindings-sendgrid.md#host-json)中查找配置设置。

## <a name="servicebus"></a>serviceBus

可在[服务总线触发器和绑定](functions-bindings-service-bus.md#host-json)中查找配置设置。

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

## <a name="version"></a>版本

此值指示 host.json 的架构版本。 面向 v2 运行时或更高版本的函数应用需要版本字符串 `"version": "2.0"`。 v2 和 v3 之间没有 host.json 架构更改。

## <a name="watchdirectories"></a>watchDirectories

应该监视其更改情况的一组[共享代码目录](functions-reference-csharp.md#watched-directories)。  确保当这些目录中的代码发生更改时，函数会拾取这些更改。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="watchfiles"></a>watchFiles

一个或多个文件名称的数组，系统会监视这些文件，查看是否存在需要重启应用的更改。  这可保证当这些文件中的代码发生更改时，函数会拾取这些更新。

```json
{
    "watchFiles": [ "myFile.txt" ]
}
```

## <a name="override-hostjson-values"></a>重写 host.json 值

某些情况下，可能希望为特定环境配置或修改 host.json 文件中的特定设置，而无需更改 host.json 文件本身。  可以通过创建等效值作为应用程序设置来重写特定的 host.json 值。 当运行时查找 `AzureFunctionsJobHost__path__to__setting` 格式的应用程序设置时，它将重写 JSON 中位于 `path.to.setting` 的等效 host.json 设置。 当表示为应用程序设置时，用于指示 JSON 层次结构的点 (`.`) 将替换为双下划线 (`__`)。 

假设当 Application insights 在本地运行时想要禁用 Application insights 采样。 如果更改了本地 host.json 文件以禁用 Application Insights，则在部署过程中可能将此更改推送到生产应用。 执行此操作更为安全的方法是创建应用程序设置作为 `local.settings.json` 文件中的 `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"`。 可以在以下 `local.settings.json` 文件中查看此内容，但这不会发布：

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何更新 host.json 文件](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [查看环境变量中的全局设置](functions-app-settings.md)
