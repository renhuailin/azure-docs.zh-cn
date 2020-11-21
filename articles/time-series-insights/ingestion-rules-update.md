---
title: 即将更改 Azure 时序见解 Gen2 中的引入和平展规则 |Microsoft Docs
description: 引入规则更改
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016711"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>即将针对新环境进行的 JSON 平展和转义规则更改

> [!IMPORTANT]
> 这些更改将仅应用于 *新创建* 的 Microsoft Azure 时序见解 Gen2 环境。 它们不适用于第 1 代环境。

Azure 时序见解 Gen2 环境将按照一组特定的命名约定动态创建存储列。 引入事件时，时序见解将向 JSON 有效负载和属性名称应用一组规则。 2020 年 7 月，已开始在新的 Azure 时序见解第 2 代环境中应用对 JSON 数据的平展和存储方式的更改。 在以下情况下，此更改会对你造成影响：

* JSON 有效负载包含嵌套对象。
* JSON 有效负载包含数组。
* 在 JSON 属性名称中使用下面四个特殊字符中的任意一个：`[` `\` `.` `'`
* 嵌套对象内有一个或多个时序 (TS) ID 属性。

如果你创建一个新环境，并且上述一个或多个事例适用于你的事件负载，则数据将以不同方式进行平展和存储。 下表概述了这些更改：

| 当前规则 | 新规则 | 示例 JSON | 以前的列名 | 新列名
|---|---| ---| ---|  ---|
| 使用下划线作为分割线来平展嵌套的 JSON。 |使用句点作为分割线来平展嵌套的 JSON。  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 不转义特殊字符。 | JSON 属性名称包含特殊字符 `.` `[`  `\`，并使用 `['` 和 `']` 来转义 `'`。 在 `['` 和 `']` 中，额外转义单引号和反斜杠。 单引号将写为 `\'`，反斜杠将写为 `\\`。  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| 基元数组存储为字符串。 | 基元类型的数组存储为动态类型。  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
始终平展对象数组，这会生成多个事件。 | 如果数组中的对象没有 TS ID 或时间戳属性，则对象数组将整体存储为动态类型。 | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>针对新环境的建议更改

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>如果 TS ID 和/或时间戳属性嵌套在对象中

任何新部署都需要匹配新的引入规则。 例如，如果 TS ID 为 `telemetry_tagId`，则需要更新任何 Azure 资源管理器模板或自动部署脚本，将 `telemetry.tagId` 配置为环境 TS ID。 还需要对嵌套的 JSON 中的事件源时间戳进行此更改。

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>如果有效负载包含嵌套的 JSON 或特殊字符，并且你可以自动创作[时序模型](./concepts-model-overview.md)变量表达式

更新执行 [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) 的客户端代码，以匹配新的引入规则。 例如，`"value": {"tsx": "$event.series_value.Double"}` 之前的[时序表达式](/rest/api/time-series-insights/reference-time-series-expression-syntax)应更新为以下选项之一：

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>后续步骤

* 了解 [Azure 时序见解第 2 代存储和引入](./concepts-ingestion-overview.md)。

* 了解如何使用[时序查询 API](./concepts-query-overview.md) 查询数据。

* 详细了解[新的时序表达式语法](/rest/api/time-series-insights/reference-time-series-expression-syntax)。