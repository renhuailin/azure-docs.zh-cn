---
title: Durable Functions 中的监视器 (Python) - Azure
description: 了解如何使用 Azure Functions 的 Durable Functions 扩展实现状态监视 (Python)。
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434757"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Durable Functions 中的监视方案 - GitHub 问题监视示例

监视模式是工作流中灵活的重复过程 - 例如，反复轮询，直到满足特定的条件为止。 本文介绍使用 Durable Functions 实现监视的示例。

## <a name="prerequisites"></a>先决条件

* [完成快速入门文章](quickstart-python-vscode.md)
* [从 GitHub 克隆或下载示例项目](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>方案概述

此示例监视 GitHub 存储库中的问题计数，并在出现 3 个以上的待解决问题时向用户发出警报。 可以使用常规计时器触发的函数定期请求待解决问题计数。 但是，此方法存在 **生存期管理** 方面的问题。 如果只应发送一条提醒，则在检测到 3 个或更多问题后，监视器需要禁用自身。 监视模式可以结束自身的执行，同时还具有其他优点：

* 监视器按时间间隔而不是计划运行：计时器触发器每隔一小时运行；监视器等待一小时，然后执行下一项操作。 除非有指定，否则监视器的操作不会重叠，这对于长时间运行的任务可能很重要。
* 监视器可以使用动态时间间隔：可以根据某种条件更改等待时间。
* 监视器可以在满足某种条件时终止，或者由其他进程终止。
* 监视器可以采用参数。 此示例演示如何将同一个存储库监视进程应用到任何请求的公共 GitHub 存储库和电话号码。
* 监视器可缩放。 由于每个监视器是一个业务流程实例，因此可以创建多个监视器，而无需创建新函数或定义更多的代码。
* 监视器可轻松集成到更大的工作流。 监视器可以是更复杂业务流程函数或[子业务流程](durable-functions-sub-orchestrations.md)的一部分。

## <a name="configuration"></a>Configuration

### <a name="configuring-twilio-integration"></a>配置 Twilio 集成

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E3_Monitor`：定期调用 `E3_TooManyOpenIssues` 的[业务流程协调程序函数](durable-functions-bindings.md#orchestration-trigger)。 如果 `E3_TooManyOpenIssues` 的返回值为 `True`，则它会调用 `E3_SendAlert`。
* `E3_TooManyOpenIssues`：一个[活动函数](durable-functions-bindings.md#activity-trigger)，用于检查存储库是否有过多待解决问题。 出于演示目的，我们认为有超过 3 个待解决问题太多了。
* `E3_SendAlert`：通过 Twilio 发送短信的活动函数。

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor 业务流程协调程序函数


**E3_Monitor** 函数对业务流程协调程序函数使用标准的 *function.json*。

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

实现该函数的代码如下：

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


此业务流程协调程序函数执行以下操作：

1. 获取要监视的存储库，以及要将短信通知发送到的电话号码 。
2. 确定监视器的过期时间。 为简便起见，本示例使用了硬编码值。
3. 调用 E3_TooManyOpenIssues 确定请求的存储库中是否存在过多待解决问题。
4. 如果有太多问题，则调用 E3_SendAlert 将短信通知发送到请求的电话号码。
5. 创建一个持久计时器，以便在下一个轮询间隔恢复业务流程。 为简便起见，本示例使用了硬编码值。
6. 持续运行，直至当前 UTC 时间超过监视器的过期时间，或者发送了短信警报。

通过多次调用业务流程协调程序函数，多个业务流程协调程序实例可以同时运行。 可以指定要监视的存储库，以及要将短信提醒发送到的电话号码。 最后，请务必注意，业务流程协调程序函数在等待计时器时是不运行的，因此不会产生费用。


### <a name="e3_toomanyopenissues-activity-function"></a>E3_TooManyOpenIssues 活动函数

与其他示例一样，帮助器活动函数是使用 `activityTrigger` 触发器绑定的正则函数。 E3_TooManyOpenIssues 函数获取存储库上当前待解决问题的列表，并确定其中是否存在“太多”问题：根据我们的示例，超过 3 个。


function.json 定义如下：

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

实现如下。

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>E3_SendAlert 活动函数

E3_SendAlert 函数使用 Twilio 绑定来发送短信消息，通知最终用户至少有 3 个待解决问题。


该函数的 *function.json* 十分简单：

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

下面是发送短信的代码：

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>运行示例

将需要一个 [GitHub](https://github.com/) 帐户。 使用它，创建一个可解决问题的临时公共存储库。

使用示例中包含的 HTTP 触发型函数，可以通过发送以下 HTTP POST 请求来启动业务流程：

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

例如，如果 GitHub 用户名为 `foo`，存储库为 `bar`，则 `"repo"` 的值应为 `"foo/bar"`。

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

E3_Monitor 实例将启动并在提供的存储库中查询待解决问题。 如果找到至少 3 个待解决问题，它将调用活动函数以发送警报；否则，它将设置计时器。 当计时器过期时，业务流程将会恢复。

可以通过在 Azure Functions 门户中查看函数日志，来了解业务流程的活动。

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

业务流程的超时时间已到，或者检测到 3 个以上待解决问题时，业务流程将会完成。 也可以在另一函数中使用 `terminate` API，或调用上述 202 响应中引用的 terminatePostUri HTTP POST Webhook。 若要使用该 Webhook，请将 `{text}` 替换为提前终止的原因。 HTTP POST URL 大致将如下所示：

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>后续步骤

本示例演示了如何使用 Durable Functions，通过[持久性计时器](durable-functions-timers.md)和条件逻辑来监视外部源的状态。 下一个示例演示如何使用外部事件和[持久计时器](durable-functions-timers.md)处理人工交互。

> [!div class="nextstepaction"]
> [运行人工交互示例](durable-functions-phone-verification.md)
