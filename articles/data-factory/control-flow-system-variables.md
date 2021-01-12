---
title: Azure 数据工厂中的系统变量
description: 本文介绍了 Azure 数据工厂支持的系统变量。 定义数据工厂实体时，可以在表达式中使用这些变量。
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: fc6b2e4c944394d811abc19f70aeb34a0ae3c9a4
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127662"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure 数据工厂支持的系统变量
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了 Azure 数据工厂支持的系统变量。 定义数据工厂实体时，可以在表达式中使用这些变量。

## <a name="pipeline-scope"></a>管道范围
可以在管道 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @pipeline().DataFactory |运行管道的数据工厂的名称 |
| @pipeline().Pipeline |管道的名称 |
| @pipeline().RunId |特定管道运行的 ID |
| @pipeline().TriggerType |调用管道的触发器的类型 (例如， `ScheduleTrigger` `BlobEventsTrigger`) 。 有关支持的触发器类型的列表，请参阅 [Azure 数据工厂中的管道执行和触发器](concepts-pipeline-execution-triggers.md)。 的触发器类型 `Manual` 指示已手动触发管道。 |
| @pipeline().TriggerId|调用管道的触发器的 ID |
| @pipeline().TriggerName|调用管道的触发器的名称 |
| @pipeline().TriggerTime|调用管道的触发器运行的时间。 这是触发器 **实际** 触发以调用管道运行的时间，可能与触发器的计划时间略有不同。  |

>[!NOTE]
>在管道和触发器范围内，与触发器相关的日期/时间系统变量 () 以 ISO 8601 格式返回 UTC 日期，例如， `2017-06-01T22:20:00.4061448Z` 。

## <a name="schedule-trigger-scope"></a>计划触发器范围
对于 [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger)类型的触发器，可以在触发器 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @trigger().scheduledTime |计划调用管道运行的时间。 |
| @trigger().startTime |触发器 **实际** 触发以调用管道运行的时间。 这可能与触发器的计划时间略有不同。 |

## <a name="tumbling-window-trigger-scope"></a>翻转窗口触发器范围
对于 [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger)类型的触发器，可以在触发器 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @trigger().outputs.windowStartTime |与触发器运行关联的窗口的开头。 |
| @trigger().outputs.windowEndTime |与触发器运行关联的窗口的结尾。 |
| @trigger().scheduledTime |计划调用管道运行的时间。 |
| @trigger().startTime |触发器 **实际** 触发以调用管道运行的时间。 这可能与触发器的计划时间略有不同。 |

## <a name="event-based-trigger-scope"></a>基于事件的触发器范围
对于 [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger)类型的触发器，可以在触发器 JSON 中的任何位置引用这些系统变量。

| 变量名 | 描述 |
| --- | --- |
| @triggerBody ( # A1. fileName  |创建或删除导致触发触发器的文件的名称。   |
| @triggerBody ( # A1. 文件夹名  |包含指定文件的文件夹的路径 `@triggerBody().fileName` 。 文件夹路径的第一段是 Azure Blob 存储容器的名称。  |
| @trigger().startTime |触发器触发以调用管道运行的时间。 |

## <a name="next-steps"></a>后续步骤
有关这些变量如何用于表达式的信息，请参阅[表达式语言和函数](control-flow-expression-language-functions.md)。
