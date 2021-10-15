---
title: 日志和事件的架构
description: 了解 Azure 数据工厂日志和事件用于监视的架构。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4b64818b505863ad7f7e2878640eeb28224af2f1
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400185"
---
# <a name="schema-of-logs-and-events"></a>日志和事件的架构

本文介绍 Azure 数据工厂日志和事件用于监视的架构。

## <a name="monitor-schema"></a>监视架构
以下属性列表用于监视。

### <a name="activity-run-log-attributes"></a>活动运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |String | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |String | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| 活动运行的 ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 诊断日志的类别。 请将该属性值设置为 `ActivityRuns`。 | `ActivityRuns` |
|**level**| String | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| String | 活动的名称及其状态。 如果活动是启动检测信号，则属性值为 `MyActivity -`。 如果活动是结束检测信号，则属性值为 `MyActivity - Succeeded`。 | `MyActivity - Succeeded` |
|**pipelineName**| String | 管道的名称。 | `MyPipeline` |
|**activityName**| String | 活动的名称。 | `MyActivity` |
|**start**| String | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果诊断日志显示活动已启动但尚未结束，则该属性值为 `1601-01-01T00:00:00Z`。 | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-log-attributes"></a>管道运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |String | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |String | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 诊断日志的类别。 请将该属性值设置为 `PipelineRuns`。 | `PipelineRuns` |
|**level**| String | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| String | 管道的名称及其状态。 管道运行完成后，该属性值为 `Pipeline - Succeeded`。 | `MyPipeline - Succeeded`. |
|**pipelineName**| String | 管道的名称。 | `MyPipeline` |
|**start**| String | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果诊断日志显示活动已启动但尚未结束，则该属性值为 `1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | 管道运行的最终状态。 可能的属性值为 `Succeeded` 和 `Failed`。 | `Succeeded`|

### <a name="trigger-run-log-attributes"></a>触发器运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |String | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |String | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| 触发器运行的 ID。 | `08587023010602533858661257311` |
|**resourceId**| String | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 诊断日志的类别。 请将该属性值设置为 `PipelineRuns`。 | `PipelineRuns` |
|**level**| String | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| String | 触发器的名称及其最终状态（指示是否已成功激发该触发器）。 如果检测信号成功，则该属性值为 `MyTrigger - Succeeded`。 | `MyTrigger - Succeeded` |
|**triggerName**| String | 触发器的名称。 | `MyTrigger` |
|**triggerType**| String | 触发器的类型。 可能的属性值为 `Manual Trigger` 和 `Schedule Trigger`。 | `ScheduleTrigger` |
|**triggerEvent**| String | 触发器的事件。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | 激发触发器的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | 最终状态（指示是否已成功激发该触发器）。 可能的属性值为 `Succeeded` 和 `Failed`。 | `Succeeded`|

### <a name="ssis-integration-runtime-log-attributes"></a>SSIS Integration Runtime 日志特性

以下是 SQL Server Integration Services (SSIS) 集成运行时 (IR) 启动、停止和维护操作的日志属性。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| 属性                   | 类型   | 说明                                                   | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | SSIS IR 操作的名称                            | `Start/Stop/Maintenance/Heartbeat` |
| **category**               | String | 诊断日志的类别                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | 数据工厂的名称                                          | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                      | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                  | `Informational` |
| **resultType**             | String | SSIS IR 操作的结果                          | `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` |
| **message**                | String | SSIS IR 操作的输出消息                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | 数据工厂资源的唯一 ID                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-message-context-log-attributes"></a>SSIS 事件消息上下文日志特性

下面是与 SSIS IR 上的 SSIS 包执行生成的事件消息相关的条件的日志特性。 它们传递的信息与 [SSIS 目录 (SSISDB) 事件消息上下文表或视图](/sql/integration-services/system-views/catalog-event-message-context)类似后者显示许多 SSIS 包属性的运行时值。 它们在选择 `Basic/Verbose` 日志记录级别时生成，可用于调试/符合性检查。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| 属性                   | 类型   | 说明                                                          | 示例                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 设置为 `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | 诊断日志的类别                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | 数据工厂的名称                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                             | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                         | `Informational` |
| **operationId**            | String | 用于跟踪 SSISDB 中的特定操作的唯一 ID          | `1`（1 表示与未存储在 SSISDB 中/通过 T-SQL 调用的包相关的操作。） |
| **contextDepth**           | String | 事件消息上下文的深度                              | `0`（0 表示在包执行开始之前的上下文，1 表示发生错误时的上下文，该数字将随着上下文离错误的距离的增加而增大。） |
| **packagePath**            | String | 作为事件消息上下文源的包对象的路径      | `\Package` |
| **contextType**            | String | 作为事件消息上下文源的包对象的类型      | `60`（请参阅[更多上下文类型](/sql/integration-services/system-views/catalog-event-message-context#remarks)。） |
| **contextSourceName**      | String | 作为事件消息上下文源的包对象的名称      | `MyPackage` |
| **contextSourceId**        | String | 作为事件消息上下文源的包对象的唯一 ID | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | 事件消息上下文源的包属性的名称   | `DelayValidation` |
| **propertyValue**          | String | 事件消息上下文源的包属性的值  | `False` |
| **resourceId**             | String | 数据工厂资源的唯一 ID                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-messages-log-attributes"></a>SSIS 事件消息日志特性

下面是通过 SSIS IR 上的 SSIS 包执行操作生成的事件消息的日志特性。 它们传递的信息与 [SSISDB 事件消息表或视图](/sql/integration-services/system-views/catalog-event-messages)类似，后者显示事件消息的详细文本或元数据。 它们可以在除 `None` 之外的任何日志记录级别生成。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| properties                   | 类型   | 说明                                                        | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 设置为 `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | 诊断日志的类别                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | 数据工厂的名称                                               | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                           | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                       | `Informational` |
| **operationId**            | String | 用于跟踪 SSISDB 中的特定操作的唯一 ID        | `1`（1 表示与未存储在 SSISDB 中/通过 T-SQL 调用的包相关的操作。） |
| **messageTime**            | 字符串 | 创建事件消息的时间（采用 UTC 格式）          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | 字符串 | 事件消息的类型                                     | `70`（请参阅[更多消息类型](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)。） |
| **messageSourceType**      | 字符串 | 事件消息源的类型                              | `20`（请参阅[更多消息源类型](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)。） |
| **message**                | String | 事件消息的文本                                     | `MyPackage:Validation has started.` |
| **packageName**            | 字符串 | 已执行的包文件的名称                             | `MyPackage.dtsx` |
| **eventName**              | 字符串 | 相关运行时事件的名称                                 | `OnPreValidate` |
| **messageSourceName**      | 字符串 | 作为事件消息源的包组件的名称         | `Data Flow Task` |
| **messageSourceId**        | 字符串 | 作为事件消息源的包组件的唯一 ID    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | 字符串 | 作为事件消息源的数据流组件的名称       | `SSIS.Pipeline` |
| **packagePath**            | String | 作为事件消息源的包对象的路径            | `\Package\Data Flow Task` |
| **executionPath**          | 字符串 | 从父包到已执行组件的完整路径            | `\Transformation\Data Flow Task`（此路径还捕获组件迭代。） |
| **threadId**               | 字符串 | 记录事件消息时执行的线程的唯一 ID | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 可执行文件统计信息日志特性

下面是通过 SSIS IR 上的 SSIS 包执行操作生成的可执行文件统计信息的日志特性，其中的可执行文件是包的控制流中的容器或任务。 它们传递的信息与 [SSISDB 可执行文件统计信息表或视图](/sql/integration-services/system-views/catalog-executable-statistics)类似，后者显示每个运行的可执行文件（包括其迭代）的一行。 它们可在除 `None` 之外的任何日志记录级别生成，用于识别任务级瓶颈或失败。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| 属性                   | 类型   | 说明                                                      | 示例                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 设置为 `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | 诊断日志的类别                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | 数据工厂的名称                                             | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                         | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                     | `Informational` |
| **executionId**            | 字符串 | 用于跟踪 SSISDB 中的特定执行的唯一 ID      | `1`（1 表示与未存储在 SSISDB 中/通过 T-SQL 调用的包相关的执行。） |
| **executionPath**          | 字符串 | 从父包到已执行组件的完整路径          | `\Transformation\Data Flow Task`（此路径还捕获组件迭代。） |
| **startTime**              | String | 可执行文件进入执行前阶段的时间（采用 UTC 格式）  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 可执行文件进入执行后阶段的时间（采用 UTC 格式） | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | 字符串 | 可执行文件的运行时间（毫秒）                   | `1,125` |
| **executionResult**        | 字符串 | 运行可执行文件的结果                                 | `0`（0 表示成功，1 表示失败，2 表示完成，3 表示取消。） |
| **executionValue**         | 字符串 | 通过运行可执行文件返回的用户定义的值            | `1` |
| **resourceId**             | String | 数据工厂资源的唯一 ID                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 执行组件阶段日志特性

以下是通过 SSIS IR 上的 SSIS 包执行生成的数据流组件的运行时统计信息的日志属性。 它们传递的信息与 [SSISDB 执行组件阶段表或视图](/sql/integration-services/system-views/catalog-execution-component-phases)类似，后者显示数据流组件在其所有执行阶段中花费的时间。 当你选择 `Performance/Verbose` 日志记录级别时会生成它们，用于捕获数据流执行统计信息。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| properties                   | 类型   | 说明                                                         | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 设置为 `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | 诊断日志的类别                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | 数据工厂的名称                                                | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                            | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                        | `Informational` |
| **executionId**            | 字符串 | 用于跟踪 SSISDB 中的特定执行的唯一 ID         | `1`（1 表示与未存储在 SSISDB 中/通过 T-SQL 调用的包相关的执行。） |
| **packageName**            | 字符串 | 已执行的包文件的名称                              | `MyPackage.dtsx` |
| **taskName**               | 字符串 | 执行的数据流任务的名称                                 | `Data Flow Task` |
| **subcomponentName**       | 字符串 | 数据流组件的名称                                     | `Derived Column` |
| **phase**                  | 字符串 | 执行阶段的名称                                         | `AcquireConnections` |
| **startTime**              | String | 执行阶段的开始时间（采用 UTC 格式）                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 执行阶段的结束时间（采用 UTC 格式）                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | 字符串 | 数据流任务的执行路径                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | 数据工厂资源的唯一 ID                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 执行数据统计信息日志特性

下面是通过数据流管道的每个分支进行的从上游组件到下游组件的数据移动的日志特性，这些特性通过 SSIS IR 上的 SSIS 包执行操作生成。 它们传递的信息与 [SSISDB 执行数据统计信息表或视图](/sql/integration-services/system-views/catalog-execution-data-statistics)类似，后者显示通过数据流任务移动的数据的行数。 当你选择 `Verbose` 日志记录级别时会生成它们，用于计算数据流吞吐量。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| properties                     | 类型   | 说明                                                        | 示例                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | 设置为 `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | 诊断日志的类别                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | 用于跟踪特定操作的唯一 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | 数据工厂的名称                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | SSIS IR 的名称                                           | `MySSISIR` |
| **level**                    | String | 诊断日志的级别                                       | `Informational` |
| **executionId**              | 字符串 | 用于跟踪 SSISDB 中的特定执行的唯一 ID        | `1`（1 表示与未存储在 SSISDB 中/通过 T-SQL 调用的包相关的执行。） |
| **packageName**              | 字符串 | 已执行的包文件的名称                             | `MyPackage.dtsx` |
| **taskName**                 | 字符串 | 执行的数据流任务的名称                                | `Data Flow Task` |
| **dataflowPathIdString**     | 字符串 | 用于跟踪数据流路径的唯一 ID                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | 字符串 | 数据流路径的名称                                         | `ADO NET Source Output` |
| **sourceComponentName**      | 字符串 | 发送数据的数据流组件的名称                    | `SQLDB Table3` |
| **destinationComponentName** | 字符串 | 接收数据的数据流组件的名称                 | `Derived Column` |
| **rowsSent**                 | 字符串 | 由源组件发送的行数                        | `500` |
| **createdTime**              | 字符串 | 获取行值的时间（采用 UTC 格式）                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | 字符串 | 数据流任务的执行路径                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | 数据工厂资源的唯一 ID                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

## <a name="log-analytics-schema"></a>Log Analytics 架构

Log Analytics 从 Azure Monitor 继承架构，但存在以下例外情况：

* 每个列名中的第一个字母会大写。 例如，Monitor 中的列名“correlationId”在 Log Analytics 中为“CorrelationId”。
* 没有“Level”列。
* 动态“properties”列保留为以下动态 JSON Blob 类型。

    | Azure Monitor 列 | Log Analytics 列 | 类型 |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 动态 |
    | $.properties.Annotations | 批注 | 动态 |
    | $.properties.Input | 输入 | 动态 |
    | $.properties.Output | 输出 | 动态 |
    | $.properties.Error.errorCode | ErrorCode | int |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | 错误 | 动态 |
    | $.properties.Predecessors | Predecessors | 动态 |
    | $.properties.Parameters | parameters | 动态 |
    | $.properties.SystemParameters | SystemParameters | 动态 |
    | $.properties.Tags | Tags | 动态 |

## <a name="next-steps"></a>后续步骤

[从 Azure 市场安装 Azure 数据工厂分析解决方案](monitor-install-analytics.md)
