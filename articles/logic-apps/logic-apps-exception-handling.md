---
title: 处理工作流中的错误和异常
description: 了解如何处理在使用 Azure 逻辑应用创建的自动任务和工作流中发生的错误和异常
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: estfan, logicappspm, azla
ms.date: 02/18/2021
ms.topic: article
ms.openlocfilehash: fbe797937021763bb97ca09e1da792d9a7010f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702498"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理错误和异常

恰当地处理依赖系统造成的停机或问题对任何集成体系结构而言可能都是一个难题。 为了帮助创建可适当处理问题和故障的可靠、可复原集成，逻辑应用提供了用于处理错误和异常的一流体验。

<a name="retry-policies"></a>

## <a name="retry-policies"></a>重试策略

对于最基本的异常和错误处理，可在任何操作或触发器中使用重试策略（如果支持），请参阅 [HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)查看相关示例。 重试策略指定当原始请求（导致 408、429 或 5xx 响应的任何请求）超时或失败时，操作或触发器是否且如何重试请求。 如果未使用任何其他重试策略，则使用默认策略。

重试策略类型如下所示：

| 类型 | 说明 |
|------|-------------|
| **Default** | 此策略可 *按指数级增长* 间隔发送最多 4 次重试，增幅为 7.5 秒，但范围限定在 5 到 45 秒之间。 |
| **指数间隔**  | 此策略会等待从指数增长的范围中随机选定的时间间隔，然后再发送下一个请求。 |
| **固定间隔**  | 此策略会等待指定的时间间隔，然后再发送下一个请求。 |
| 无  | 不重新发送请求。 |
|||

要了解重试策略限制，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#http-limits)。

### <a name="change-retry-policy"></a>更改重试策略

要选择其他重试策略，请执行以下步骤：

1. 在逻辑应用设计器中打开逻辑应用。

1. 打开操作或触发器的“设置”。

1. 如果操作或触发器支持重试策略，请在“重试策略”下选择所需类型。

或者，可以在支持重试策略的操作或触发器的 `inputs` 部分指定重试策略。 如果不指定重试策略，该操作将使用默认策略。

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*必需*

| Value | 类型 | 说明 |
|-------|------|-------------|
| <*retry-policy-type*> | String | 要使用的重试策略类型：`default`、`none`、`fixed` 或 `exponential` |
| <*retry-interval*> | String | 其中值必须使用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试时间间隔。 默认的最小时间间隔是 `PT5S`，而最大时间间隔是 `PT1D`。 如果使用指数式时间间隔策略，可更改最小值和最大值。 |
| <*retry-attempts*> | Integer | 重试尝试次数，它必须介于 1 和 90 之间 |
||||

*可选*

| Value | 类型 | 说明 |
|-------|------|-------------|
| <*minimum-interval*> | String | 对于指数式时间间隔策略，是指随机选定的时间间隔的最小时间间隔（采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)） |
| <*maximum-interval*> | String | 对于指数式时间间隔策略，是指随机选定的时间间隔的最大时间间隔（采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)） |
||||

下面详细介绍不同的策略类型。

<a name="default-retry"></a>

### <a name="default"></a>默认

如果不指定重试策略，则操作将使用默认策略，它实际上是一个[指数式时间间隔策略](#exponential-interval)，按指数级增长的时间间隔（以 7.5 秒为增幅）最多发送 4 次重试。 时间间隔的范围为 5 到 45 秒。

尽管未在操作或触发器中显式定义，但默认策略在示例 HTTP 操作中的行为方式如下所示：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>None

要指定操作或触发器不重试失败的请求，请将 <retry-policy-type> 设置为 `none`。

### <a name="fixed-interval"></a>固定间隔

要指定操作或触发器在等待指定的时间间隔后再发送下一个请求，请将 <retry-policy-type> 设置为 `fixed`。

*示例*

该重试策略在首次请求失败后再尝试获取最新资讯两次，每次尝试之间延迟 30 秒：

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>指数间隔

要指定操作或触发器在等待随机的时间间隔后再发送下一个请求，请将 <retry-policy-type> 设置为 `exponential`。 随机时间间隔选自呈指数增长的范围。 此外，可通过自行指定最小时间间隔和最大时间间隔替代默认的最小和最大时间间隔。

**随机变量范围**

下表展示了逻辑应用如何为每次重试生成指定范围内的一个统一随机变量（不超过重试次数）：

| 重试次数 | 最小间隔 | 最大间隔 |
|--------------|------------------|------------------|
| 1 | max(0, <minimum-interval>) | min(interval, <maximum-interval>) |
| 2 | max(interval, <minimum-interval>) | min(2 * interval, <maximum-interval>) |
| 3 | max(2 * interval, <minimum-interval>) | min(4 * interval, <maximum-interval>) |
| 4 | max(4 * interval, <minimum-interval>) | min(8 * interval, <maximum-interval>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>通过更改“在其后运行”行为来捕获和处理故障

在逻辑应用设计器中添加操作时，会隐式声明要用于运行这些操作的顺序。 操作完成运行后，该操作会标记为 `Succeeded`、`Failed`、`Skipped` 或 `TimedOut` 等状态。 在每个操作定义中，`runAfter` 属性指定必须首先完成的前置任务操作，以及在后续任务操作可以运行之前该前置任务操作允许的状态。 默认情况下，在设计器中添加的操作仅在前置任务完成且状态为 `Succeeded` 后运行。

当某个操作引发未处理的错误或异常时，该操作会标记为 `Failed`，并且任何后续操作都会标记为 `Skipped`。 如果具有并行分支的操作发生此行为，则逻辑应用引擎将遵循其他分支来确定其完成状态。 例如，如果某个分支以 `Skipped` 操作结尾，则该分支的完成状态将基于该跳过的操作的前置任务状态。 逻辑应用运行完成后，引擎通过评估所有分支状态确定整个运行的状态。 如果任何分支以失败结束，将整个逻辑应用运行都会标记为 `Failed`。

![显示如何评估运行状态的示例](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

若要确保某个操作无论其前置任务状态如何仍可以运行，请[自定义操作的“在其后运行”行为](#customize-run-after)，以处理前置任务的不成功状态。

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>自定义“在其后运行”行为

可以自定义操作的“在其后运行”行为，以便在前置任务的状态为 `Succeeded`、`Failed`、`Skipped` 或 `TimedOut` 时运行该操作。 例如，若要在 Excel Online `Add_a_row_into_a_table` 前置任务操作被标记为 `Failed` 而非 `Succeeded` 后发送电子邮件，请按以下任一步骤更改“在其后运行”行为：

* 在设计视图中，选择省略号 (...) 按钮，然后选择“配置在其后运行” 。

  ![配置操作的“在其后运行”行为](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  操作形状显示前置任务操作所需的默认状态，在此示例中为“向表中添加行”：

  ![操作的默认“在其后运行”行为](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  将“在其后运行”行为更改为所需的状态，在此示例中为“已失败”：

  ![将“在其后运行”行为更改为“已失败”](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  若要将操作指定为在前置任务操作被标记为 `Failed`、`Skipped` 或 `TimedOut` 时都会运行，请选择其他状态：

  ![将“在其后运行”行为更改为具有任何其他状态](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* 在代码视图中，在操作的 JSON 定义中编辑 `runAfter` 属性，该属性遵循以下语法：

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  对于本示例，请将 `runAfter` 属性从 `Succeeded` 更改为 `Failed`：

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  若要将操作指定为在前置任务操作被标记为 `Failed`、`Skipped` 或 `TimedOut` 时都会运行，请添加其他状态：

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>评估具有作用域的操作及其结果

与通过 `runAfter` 属性在个别操作之后运行步骤类似，你可以将操作组合到[作用域](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)中。 如果希望以逻辑方式将各个操作组合在一起，可以使用作用域，评估作用域的聚合状态，并基于该状态执行操作。 当某个作用域中的所有操作都完成运行后，该作用域本身也确定了其自己的状态。

若要检查作用域的状态，可以使用与用来检查逻辑应用运行状态（例如 `Succeeded`、`Failed` 等等）的条件相同的条件。

默认情况下，当作用域的所有操作都成功时，作用域的状态将被标记为 `Succeeded`。 如果作用域中最后一个操作的状态为 `Failed` 或 `Aborted`，则作用域的状态被标记为 `Failed`。

若要捕获状态为 `Failed` 的作用域中的异常并运行用来处理那些错误的操作，可以为该状态为 `Failed` 的作用域使用 `runAfter` 属性。 这样，如果作用域中的任何操作失败并且为该作用域使用了 `runAfter` 属性，则可以创建单个操作来捕获失败。

有关作用域的限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>获取失败的上下文和结果

尽管从作用域中捕获失败非常有用，但可能还需要借助上下文来确切了解失败的操作以及返回的任何错误或状态代码。 [`result()` 函数](../logic-apps/workflow-definition-language-functions-reference.md#result)通过接受单个参数（作用域的名称）并返回一个数组（其中包含来自一级操作的结果）来返回作用域内操作的顶级操作结果。 这些操作对象包含的特性（例如操作的开始时间、结束时间、状态、输入、相关 ID 和输出）与 `actions()` 返回的对象包含的特性相同。 

> [!NOTE]
> `result()` 函数仅返回一级操作的结果，而不从更深的嵌套操作（如切换或条件操作）返回结果。

若要获取作用域中失败操作的上下文，可以使用具有作用域的名称和 `runAfter` 属性的 `@result()` 表达式。 若要从返回的数组中筛选出具有 `Failed` 状态的操作，可以添加[“筛选数组”操作](logic-apps-perform-data-operations.md#filter-array-action)。 若要为返回的失败操作运行操作，请获取返回的筛选数组，并使用 [For each 循环](../logic-apps/logic-apps-control-flow-loops.md)。

以下示例（后附详细说明）发送一个 HTTP POST 请求，其中包含名为“My_Scope”的作用域操作中失败的任何操作的响应正文：

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

下面是描述此示例中发生的情况的详细演练：

1. 要获取“My_Scope”中所有操作的结果，**筛选数组** 操作将使用筛选表达式：`@result('My_Scope')`

1. “筛选数组”的条件是状态等于 `Failed` 的任何 `@result()` 项。 此条件将具有“My_Scope”中所有操作结果的数组筛选为仅包含失败操作结果的数组。

1. 对“筛选后的数组”输出执行 `For_each` 循环操作。 此步骤针对前面筛选的每个失败操作结果执行操作。

   如果作用域中只有一个操作失败，`For_each` 循环中的操作只运行一次。 如果存在多个失败的操作，则将对每个失败执行一次操作。

1. 针对 `For_each` 项响应正文（即 `@item()['outputs']['body']` 表达式）发送 HTTP POST。

   `@result()` 项的形状与 `@actions()` 形状相同，可按相同的方式进行分析。

1. 包括两个自定义标头，其中包含失败操作的名称 (`@item()['name']`) 和失败的运行客户端跟踪 ID (`@item()['clientTrackingId']`)。

下面提供了单个 `@result()` 项的示例供参考，其中显示 `name`、`body` 和 `clientTrackingId` 属性已在前面的示例进行分析。 在 `For_each` 操作外部，`@result()` 会返回这些对象的数组。

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

若要执行不同的异常处理模式，可以使用本文中前面所述的表达式。 可以选择在范围外部执行单个异常处理操作，此操作接受经过筛选的整个失败数组，并删除 `For_each` 操作。 如前面所述，还可以包含 `\@result()` 响应中其他有用的属性。

## <a name="set-up-azure-monitor-logs"></a>设置 Azure Monitor 日志

上述模式非常适合于处理运行中的错误和异常，不过也可以独立于运行本身来标识和响应错误。 [Azure Monitor](../azure-monitor/overview.md) 提供了一种简单方式，将所有工作流事件（包括所有运行和操作状态）发送到 [Log Analytics 工作区](../azure-monitor/logs/data-platform-logs.md)、[Azure 存储帐户](../storage/blobs/storage-blobs-overview.md) 或 [Azure 事件中心](../event-hubs/event-hubs-about.md)。

要评估运行状态，可以监视日志和指标，或将它们发布到你习惯使用的任何监视工具中。 一种潜在选项是通过事件中心将所有事件流式传输到 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)。 在流分析中，可以根据诊断日志中的任何异常、平均值或失败编写实时查询。 可以使用流分析将信息发送到其他数据源，例如队列、主题、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>后续步骤

* [了解如何使用 Azure 逻辑应用构建错误处理逻辑](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [查找更多逻辑应用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)
