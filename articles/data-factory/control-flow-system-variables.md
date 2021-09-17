---
title: 系统变量
titleSuffix: Azure Data Factory & Azure Synapse
description: 本文介绍了 Azure 数据工厂和 Azure Synapse Analytics 支持的系统变量。 在两个服务中定义实体时，可以在表达式中使用这些变量。
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: f0fa5503b52481afafe2a0a6be8e28f8a964464b
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821616"
---
# <a name="system-variables-supported-by-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse Analytics 支持的系统变量

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了 Azure 数据工厂和 Azure Synapse 支持的系统变量。 在两个服务中定义实体时，可以在表达式中使用这些变量。

## <a name="pipeline-scope"></a>管道范围

可以在管道 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @pipeline().DataFactory |在其中运行管道运行的数据工厂或 Synapse 工作区的名称 |
| @pipeline().Pipeline |管道的名称 |
| @pipeline().RunId |特定管道运行的 ID |
| @pipeline().TriggerType |调用了该管道的触发器的类型（例如 `ScheduleTrigger`、`BlobEventsTrigger`）。 有关支持的触发器类型列表，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。 触发器类型为 `Manual` 表示管道是手动触发的。 |
| @pipeline().TriggerId|调用了管道的触发器的 ID |
| @pipeline().TriggerName|调用了管道的触发器的名称 |
| @pipeline().TriggerTime|触发器运行调用管道的时间。 这是触发器实际触发以调用管道运行的时间，它可能与触发器的计划时间略有不同。  |
| @pipeline().GroupId | 管道运行所属组的 ID。 |
| @pipeline()?TriggeredByPipelineName | 触发管道运行的管道的名称。 当管道运行由 ExecutePipeline 活动触发时适用。 在其他环境中使用时，结果为“Null”。 注意 @pipeline() 后面的问号 |
| @pipeline()?TriggeredByPipelineRunId | 运行触发管道运行的管道的运行 ID。 当管道运行由 ExecutePipeline 活动触发时适用。 在其他环境中使用时，结果为“Null”。 注意 @pipeline() 后面的问号 |

>[!NOTE]
>与触发器相关的日期/时间系统变量（在管道和触发器作用域内）以 ISO 8601 格式返回 UTC 日期，例如 `2017-06-01T22:20:00.4061448Z`。

## <a name="schedule-trigger-scope"></a>计划触发器作用域

如果触发器的类型为 [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger)，则可以在触发器 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @trigger().scheduledTime |触发器计划调用管道运行的时间。 |
| @trigger().startTime |触发器实际触发以调用管道运行的时间。 这可能与触发器的计划时间略有不同。 |

## <a name="tumbling-window-trigger-scope"></a>翻转窗口触发器作用域

如果触发器的类型为 [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger)，则可以在触发器 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @trigger().outputs.windowStartTime |与触发器运行关联的窗口的开始时间。 |
| @trigger().outputs.windowEndTime |与触发器运行关联的窗口的结束时间。 |
| @trigger().scheduledTime |触发器计划调用管道运行的时间。 |
| @trigger().startTime |触发器实际触发以调用管道运行的时间。 这可能与触发器的计划时间略有不同。 |

## <a name="storage-event-trigger-scope"></a>存储事件触发器范围

如果触发器的类型为 [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger)，则可以在触发器 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @triggerBody().fileName  |创建或删除时导致触发器触发的文件的名称。   |
| @triggerBody().folderPath  |包含 `@triggerBody().fileName` 指定的文件的文件夹路径。 文件夹路径的第一部分是 Azure Blob 存储容器的名称。  |
| @trigger().startTime |触发器触发以调用管道运行的时间。 |

## <a name="custom-event-trigger-scope"></a>自定义事件触发器范围

如果触发器的类型为 [CustomEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger)，则可以在触发器 JSON 中的任何位置引用这些系统变量。

>[!NOTE]
>服务预期自定义事件的格式采用 [Azure 事件网格事件架构](../event-grid/event-schema.md)。

| 变量名 | 说明
| --- | --- |
| @triggerBody().event.eventType | 触发了自定义事件触发器运行的事件类型。 事件类型为客户定义的字段，可以是字符串类型的任何值。 |
| @triggerBody().event.subject | 导致触发器激发的自定义事件的主题。 |
| @triggerBody().event.data._keyName_ | 自定义事件中的数据字段不存在 JSON blob，可供客户用于发送消息和数据。 请使用 data._keyName_ 来引用每个字段。 例如，@triggerBody().event.data.callback 将返回存储在数据下的回调字段的值 。 |
| @trigger().startTime | 触发器触发以调用管道运行的时间。 |

## <a name="next-steps"></a>后续步骤

* 有关这些变量如何用于表达式的信息，请参阅[表达式语言和函数](control-flow-expression-language-functions.md)。
* 若要在管道中使用触发器范围系统变量，请参阅[在管道中引用触发器元数据](how-to-use-trigger-parameterization.md)
