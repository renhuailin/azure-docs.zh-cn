---
title: Azure 数据工厂中的系统变量
description: 本文介绍了 Azure 数据工厂支持的系统变量。 定义数据工厂实体时，可以在表达式中使用这些变量。
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 119ecb3ec9c208340f09f513bf10b3ad24312cb5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201220"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure 数据工厂支持的系统变量

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了 Azure 数据工厂支持的系统变量。 定义数据工厂实体时，可以在表达式中使用这些变量。

## <a name="pipeline-scope"></a>管道范围

可以在管道 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @pipeline().DataFactory |在其中运行管道运行的数据工厂的名称 |
| @pipeline().Pipeline |管道的名称 |
| @pipeline().RunId |特定管道运行的 ID |
| @pipeline().TriggerType |调用了该管道的触发器的类型（例如 `ScheduleTrigger`、`BlobEventsTrigger`）。 有关支持的触发器类型的列表，请参阅 [Azure 数据工厂中的管道执行和触发器](concepts-pipeline-execution-triggers.md)。 触发器类型为 `Manual` 表示管道是手动触发的。 |
| @pipeline().TriggerId|调用了管道的触发器的 ID |
| @pipeline().TriggerName|调用了管道的触发器的名称 |
| @pipeline().TriggerTime|触发器运行调用管道的时间。 这是触发器实际触发以调用管道运行的时间，它可能与触发器的计划时间略有不同。  |

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
| @triggerBody().folderName  |包含 `@triggerBody().fileName` 指定的文件的文件夹路径。 文件夹路径的第一部分是 Azure Blob 存储容器的名称。  |
| @trigger().startTime |触发器触发以调用管道运行的时间。 |

## <a name="next-steps"></a>后续步骤

* 有关这些变量如何用于表达式的信息，请参阅[表达式语言和函数](control-flow-expression-language-functions.md)。
* 若要在管道中使用触发器范围系统变量，请参阅 [在管道中引用触发器元数据](how-to-use-trigger-parameterization.md)
