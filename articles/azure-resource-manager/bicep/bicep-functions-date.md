---
title: Bicep 函数 - date
description: 描述在 Bicep 文件中用于处理日期的函数。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 0eb995db6584dd056fa0b1563aec8c117fddeb66
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357263"
---
# <a name="date-functions-for-bicep"></a>Bicep 的日期函数

本文介绍用于处理日期的 Bicep 函数。

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

向基础值加上一个持续时间。 需要 ISO 8601 格式。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| base | 是 | string | 用于相加的起始日期/时间值。 使用 [ISO 8601 时间戳格式](https://en.wikipedia.org/wiki/ISO_8601)。 |
| duration | 是 | string | 要与 base 相加的时间值。 它可以是负值。 使用 [ISO 8601 持续时间格式](https://en.wikipedia.org/wiki/ISO_8601#Durations)。 |
| format | 否 | string | 日期时间结果的输出格式。 如果未提供，则将使用 base 值的格式。 使用[标准格式字符串](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式字符串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

### <a name="return-value"></a>返回值

将 duration 值与 base 值相加后得到的日期/时间值。

### <a name="examples"></a>示例

以下示例展示了增加时间值的各种方式。

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

如果使用基本时间 `2020-04-07 14:53:14Z` 部署了前面的示例，则输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| add3YearsOutput | String | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | String | 3/29/2020 2:53:14 PM |
| add1HourOutput | String | 4/7/2020 3:53:14 PM |

下一示例展示了如何设置自动化计划的开始时间。

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

以指定的格式返回当前的 (UTC) 日期时间值。 如果未提供格式，则使用 ISO 8601 (`yyyyMMddTHHmmssZ`) 格式。 **此函数只能在参数的默认值中使用。**

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| format |否 |string |要转换为字符串的 URI 编码值。 使用[标准格式字符串](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式字符串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

### <a name="remarks"></a>备注

只能在表达式中对参数的默认值使用此函数。 在 Bicep 文件中的其他任何位置使用此函数都会返回错误。 不允许在 Bicep 文件的其他部分使用该函数，因为每次调用该函数，都会返回不同的值。 使用相同的参数部署同一 Bicep 文件不能可靠地生成相同的结果。

如果使用[出错时回退选项](../templates/rollback-on-error.md)回退到以前成功的部署，而以前的部署包含一个使用 utcNow 的参数，则不会重新评估该参数， 而是在回滚部署中自动重复使用以前部署中的参数值。

重新部署依赖于 utcNow 函数提供默认值的 Bicep 文件时，请保持谨慎。 如果重新部署且不提供参数的值，则会重新评估该函数。 若要更新现有的资源而不是新建资源，请传入以前部署中的参数值。

### <a name="return-value"></a>返回值

当前的 UTC 日期时间值。

### <a name="examples"></a>示例

以下示例显示了日期时间值的不同格式。

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

上述示例的输出根据每个部署的不同而异，但类似于：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

以下示例演示在设置标记值时如何使用函数中的值。

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 文件中各部分的说明，请参阅[了解 Bicep 文件的结构和语法](./file.md)。
