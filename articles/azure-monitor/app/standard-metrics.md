---
title: Azure Application Insights 标准指标 | Microsoft Docs
description: 本文列出了 Azure Application Insights 指标以及支持的聚合与维度。
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572348"
---
# <a name="application-insights-standard-metrics"></a>Application Insights 标准指标

标准指标在收集期间会预先聚合，它们在查询时具有更好的性能。 因此它们成为仪表板和实时警报的最佳选择。

## <a name="availability-metrics"></a>可用性指标

参考“可用性”类别中的指标可以了解在世界各地预测到的 Web 应用程序运行状况。 [配置可用性测试](../app/monitor-web-app-availability.md)，以开始使用此类别中的任何指标。

### <a name="availability-availabilityresultsavailabilitypercentage"></a>可用性 (availabilityResults/availabilityPercentage)
“可用性”指标显示未检测到任何问题的 Web 测试运行的百分比。 可能的最小值为 0，表示所有 Web 测试运行均失败。 值 100 表示所有 Web 测试运行都符合验证条件。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|百分比|平均值|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>可用性测试持续时间 (availabilityResults/duration)

“可用性测试持续时间”指标显示运行 Web 测试所花费的时间。 对于[多步骤 Web 测试](../app/availability-multistep.md)，该指标反映所有步骤的总执行时间。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|毫秒|Average、Min、Max|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>可用性测试 (availabilityResults/count)

“可用性测试”指标反映 Azure Monitor 执行的 Web 测试运行计数。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|计数|计数|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>浏览器指标

浏览器指标由 Application Insights JavaScript SDK 从实际的最终用户浏览器收集。 它们提供 Web 应用用户体验的深入见解。 通常不会对浏览器指标采样，这意味着，在用量数字方面，与服务器端指标相比，它们提供的精度更高，而后者的采样可能会导致结果有偏差。

> [!NOTE]
> 若要收集浏览器指标，必须使用 [Application Insights JavaScript SDK](../app/javascript.md) 来检测应用程序。

### <a name="browser-page-load-time-browsertimingstotalduration"></a>浏览器页面加载时间 (browserTimings/totalDuration)

|度量单位|支持的聚合| 支持的维度|
|---|---|---|
|毫秒|Average、Min、Max|无|

### <a name="client-processing-time-browsertimingprocessingduration"></a>客户端处理时间 (browserTiming/processingDuration)

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|毫秒|Average、Min、Max|无|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>页面加载网络连接时间 (browserTimings/networkDuration)

|度量单位|支持的聚合| 支持的维度|
|---|---|---|
|毫秒|Average、Min、Max|无|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>接收响应时间 (browserTimings/receiveDuration)

|度量单位|支持的聚合| 支持的维度|
|---|---|---|
|毫秒|Average、Min、Max|无|

### <a name="send-request-time-browsertimingssendduration"></a>发送请求时间 (browserTimings/sendDuration)

|度量单位|支持的聚合| 支持的维度|
|---|---|---|
|毫秒|Average、Min、Max|无|

## <a name="failure-metrics"></a>失败指标

“失败”中的指标显示处理请求、依赖项调用的问题，以及引发的异常。

### <a name="browser-exceptions-exceptionsbrowser"></a>浏览器异常数 (exceptions/browser)

此指标反映浏览器中运行的应用程序代码引发的异常数。 该指标仅包含使用 ```trackException()``` Application Insights API 调用跟踪的异常。

|度量单位|支持的聚合 | 支持的维度|
|---|---|---|---|
| 计数 | 计数 | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>依赖项调用失败数 (dependencies/failed)

失败的依赖项调用数。

|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
|计数|计数| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>异常数 (exceptions/count)

每当你将异常记录到 Application Insights 时，都会调用 SDK 的 [trackException() 方法](../app/api-custom-events-metrics.md#trackexception)。 “异常数”指标显示记录的异常数。

|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
|计数|计数|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>失败的请求数 (请求/失败)

标记为失败的受跟踪服务器请求计数。 默认情况下，Application Insights SDK 会自动将返回 HTTP 响应代码 5xx 或 4xx 的每个服务器请求标记为失败的请求。 可以通过在 [自定义遥测初始化表达式](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)中修改请求遥测项的 *success* 属性来自定义此逻辑。


|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
|计数|计数|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>服务器异常数 (exceptions/server)

此指标显示服务器异常数。

|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
|计数|计数|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>性能计数器

使用“性能计数器”类别中的指标可以访问 [Application Insights 收集的系统性能计数器](../app/performance-counters.md)。

### <a name="available-memory-performancecountersavailablememory"></a>可用内存 (performanceCounters/availableMemory)

|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
|依赖于数据：MB、GB|平均值、最大值、最小值|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>异常率 (performanceCounters/exceptionRate)

|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
| 计数 | 平均值、最大值、最小值 | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 请求执行时间 (performanceCounters/requestExecutionTime)

|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
| 毫秒 | 平均值、最大值、最小值 | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 请求速率 (performanceCounters/requestsPerSecond)

|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
| 每秒请求数 | 平均值、最大值、最小值 | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>应用程序队列中的 HTTP 请求数 (performanceCounters/requestsInQueue)

|度量单位|支持的聚合 | 支持的维度 |
|---|---|---|---|
| 计数 | 平均值、最大值、最小值 | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>进程 CPU (performanceCounters/processCpuPercentage)

该指标显示托管受监视应用的进程消耗的处理器总容量。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|百分比|平均值、最大值、最小值| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>进程 IO 速率 (performanceCounters/processIOBytesPerSecond)

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|每秒字节数|Average、Min、Max|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>进程专用字节数 (performanceCounters/processPrivateBytes)

受监视进程为其数据分配的非共享内存量。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|字节|Average、Min、Max|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>处理器时间 (performanceCounters/processorCpuPercentage)

受监视服务器实例上运行的所有进程的 CPU 消耗量。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|百分比|Average、Min、Max|`Cloud role instance` |

>[!NOTE]
> 处理器时间指标不适用于 Azure 应用服务中托管的应用程序。 使用 [进程 CPU](#process-cpu-performancecountersprocesscpupercentage) 指标可以跟踪应用服务中托管的 Web 应用程序的 CPU 利用率。

## <a name="server-metrics"></a>服务器指标

### <a name="dependency-calls-dependenciescount"></a>依赖项调用数 (dependencies/count)

此指标与依赖项调用数相关。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
| 计数 | 计数 | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>依赖项持续时间 (dependencies/duration)

此指标是指依赖项调用的持续时间。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
| 时间 | Average、Min、Max | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>服务器请求速率（请求数/计数）

此指标反映 Web 应用程序收到的传入服务器请求数。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
| 计数 | 平均值 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>服务器请求数 (requests/count)

|度量单位|支持的聚合|支持的维度|
|---|---|---|
| 计数 | 计数 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>服务器响应时间 (requests/duration)

此指标反映服务器处理传入请求所花费的时间。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
| 时间 | Average、Min、Max | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>使用情况指标

### <a name="page-view-load-time-pageviewsduration"></a>页面查看次数加载时间 (pageViews/duration)

此指标是指加载 PageView 事件所花费的时间。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
| 时间 | Average、Min、Max | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>页面查看次数 (pageViews/count)

使用 TrackPageView () Application Insights API 记录的 PageView 事件计数。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
| 计数 | 计数 | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>跟踪数 (traces/count)

使用 TrackTrace () Application Insights API 调用记录的跟踪语句计数。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
| 计数 | 计数 | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>后续步骤

* 了解[基于日志的指标和预聚合的指标](./pre-aggregated-metrics-log-metrics.md)。
* [基于日志的指标查询和定义](../essentials/app-insights-metrics.md)。