---
title: 模板函数 - 日期
description: 介绍了可在 Azure 资源管理器模板（ARM 模板）中用来处理日期的函数。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ed7c9d44458acb6733618ff84cf98b9bed2e2a36
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793410"
---
# <a name="date-functions-for-arm-templates"></a>ARM 模板的日期函数

资源管理器提供了以下可用于在 Azure 资源管理器模板（ARM 模板）中处理日期的函数：

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

向基础值加上一个持续时间。 需要 ISO 8601 格式。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| base | 是 | string | 用于相加的起始日期/时间值。 使用 [ISO 8601 时间戳格式](https://en.wikipedia.org/wiki/ISO_8601)。 |
| duration | 是 | string | 要与 base 相加的时间值。 它可以是负值。 使用 [ISO 8601 持续时间格式](https://en.wikipedia.org/wiki/ISO_8601#Durations)。 |
| format | 否 | string | 日期时间结果的输出格式。 如果未提供，则将使用 base 值的格式。 使用[标准格式字符串](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式字符串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

### <a name="return-value"></a>返回值

将 duration 值与 base 值相加后得到的日期/时间值。

### <a name="examples"></a>示例

以下示例模板展示了增加时间值的各种方式。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd.json":::

在 baseTime为 `2020-04-07 14:53:14Z` 的情况下部署上述模板时，输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| add3YearsOutput | String | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | String | 3/29/2020 2:53:14 PM |
| add1HourOutput | String | 4/7/2020 3:53:14 PM |

下一示例模板展示了如何设置自动化计划的开始时间。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd-automation.json":::

## <a name="utcnow"></a>utcNow

`utcNow(format)`

以指定的格式返回当前的 (UTC) 日期时间值。 如果未提供格式，则使用 ISO 8601 (`yyyyMMddTHHmmssZ`) 格式。 **此函数只能在参数的默认值中使用。**

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| format |否 |string |要转换为字符串的 URI 编码值。 使用[标准格式字符串](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式字符串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

### <a name="remarks"></a>备注

只能在表达式中对参数的默认值使用此函数。 在模板中的其他任何位置使用此函数都会返回错误。 不允许在模板的其他部分使用该函数，因为每次调用该函数，都会返回不同的值。 使用相同的参数部署同一模板不能可靠地生成相同的结果。

如果使用[出错时回退选项](rollback-on-error.md)回退到以前成功的部署，而以前的部署包含一个使用 utcNow 的参数，则不会重新评估该参数， 而是在回滚部署中自动重复使用以前部署中的参数值。

重新部署依赖于 utcNow 函数提供默认值的模板时请保持谨慎。 如果重新部署且不提供参数的值，则会重新评估该函数。 若要更新现有的资源而不是新建资源，请传入以前部署中的参数值。

### <a name="return-value"></a>返回值

当前的 UTC 日期时间值。

### <a name="examples"></a>示例

以下示例模板演示日期时间值的不同格式。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow.json":::

上述示例的输出根据每个部署的不同而异，但类似于：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

以下示例演示在设置标记值时如何使用函数中的值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow-tag.json":::

## <a name="next-steps"></a>后续步骤

* 有关 ARM 模板中各部分的说明，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
