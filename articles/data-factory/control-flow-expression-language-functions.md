---
title: 表达式和函数
titleSuffix: Azure Data Factory & Azure Synapse
description: 本文提供有关可用于创建 Azure 数据工厂和 Azure Synapse Analytics 管道实体的表达式和函数的信息。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: aaca4774f6f56d38624b4811375a6661299161cc
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821768"
---
# <a name="expressions-and-functions-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse Analytics 中的表达式和函数

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-functions-variables.md)
> * [当前版本/Synapse 版本](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供了有关 Azure 数据工厂和 Azure Synapse Analytics 支持的表达式和函数的详细信息。 

## <a name="expressions"></a>表达式

定义中的 JSON 值可以是文字，也可以是运行时计算的表达式。 例如：  
  
```json
"name": "value"
```

 或  
  
```json
"name": "@pipeline().parameters.password"
```

表达式可出现在 JSON 字符串值中的任何位置，始终生成另一个 JSON 值。 如果某个 JSON 值为表达式，会通过删除 \@ 符号来提取表达式的正文。 如果需要以“\@”开头的文本字符串，则必须使用 \@\@ 将它转义。 以下示例演示了如何计算表达式。  
  
|JSON 值|结果|  
|----------------|------------|  
|"parameters"|返回字符“parameters”。|  
|"parameters[1]"|返回字符“parameters[1]”。|  
|"\@\@"|返回包含“\@”的、由 1 个字符构成的字符串。|  
|" \@"|返回包含“\@”的、由 2 个字符构成的字符串。|  
  
 如果使用称为字符串内插的功能（其中表达式封装在 `@{ ... }` 内），表达式还可以显示在字符串内。 例如： `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 使用字符串内插，结果始终是字符串。 假设我将 `myNumber` 定义为 `42`，将 `myString` 定义为 `foo`：  
  
|JSON 值|结果|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| 返回字符串形式的 `foo`。|  
|"\@{pipeline().parameters.myString}"| 返回字符串形式的 `foo`。|  
|"\@pipeline().parameters.myNumber"| 返回 *数字* 形式的 `42`。|  
|"\@{pipeline().parameters.myNumber}"| 返回 *字符串* 形式的 `42`。|  
|"Answer is: @{pipeline().parameters.myNumber}"| 返回字符串 `Answer is: 42`。|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| 返回字符串 `Answer is: 42`|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| 返回字符串 `Answer is: @{pipeline().parameters.myNumber}`。|  

在 ForEach 活动之类的控制流活动中，可以提供要针对属性项进行迭代的数组，并使用 @item() 在 ForEach 活动中对单个枚举进行迭代。 例如，如果 items 是数组 [1, 2, 3]，则 @item() 在第一次迭代中返回 1，在第二次迭代中返回 2，在第三次迭代中返回 3。 还可以使用 @range(0,10) 之类的表达式来迭代 10 次，从 0 开始，到 9 结束。

可以使用 @activity('活动名称') 来捕获活动的输出并做出决策。 请考虑名为“Web1”的 Web 活动。 若要将第一个活动的输出放在第二个活动的正文中，表达式通常如下所示：@activity('Web1').output 或 @activity('Web1').output.data，或类似的内容，具体取决于第一个活动的输出的内容。 

## <a name="examples"></a>示例

### <a name="complex-expression-example"></a>复杂表达式示例
以下示例显示了一个复杂的示例，该示例引用了活动输出的一个深层子字段。 若要引用计算结果为子字段的管道参数，请使用 [] 语法而不是点 (.) 运算符（如 subfield1 和 subfield2 一样），作为某个活动输出的组成部分。

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

动态创建文件并命名是一种常见模式。 让我们来探究几个动态文件命名示例。

  1. 将日期追加到文件名：`@concat('Test_',  formatDateTime(utcnow(), 'yyyy-dd-MM'))` 
  
  2. 按照客户时区追加日期时间：`@concat('Test_',  convertFromUtc(utcnow(), 'Pacific Standard Time'))`
  
  3. 追加触发器时间：` @concat('Test_',  pipeline().TriggerTime)`
  
  4. 输出到带有日期的单个文件时，在映射数据流中输出自定义文件名：`'Test_' + toString(currentDate()) + '.csv'`

在上述情况下，会创建 4 个以 Test_ 开头的动态文件名。 

### <a name="dynamic-content-editor"></a>动态内容编辑器

完成编辑后，动态内容编辑器会自动转义内容中的字符。 例如，内容编辑器中的以下内容是具有两个表达式函数的字符串内插。 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

动态内容编辑器将上述内容转换为表达式 `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"`。 此表达式的结果为下面所示的 JSON 格式字符串。

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with-a-parameter"></a>使用参数的数据集
在以下示例中，BlobDataset 采用名为 **path** 的参数。 其值用于使用以下表达式设置 **folderPath** 属性的值：`dataset().path`。 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>使用参数的管道
在以下示例中，管道采用 **inputPath** 和 **outputPath** 参数。 参数化 blob 数据集的 **路径** 使用这些参数的值进行设置。 此处使用的语法是：`pipeline().parameters.parametername`。 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```

### <a name="replacing-special-characters"></a>替换特殊字符

完成编辑后，动态内容编辑器会自动转义内容中的双引号、反斜杠等字符。 如果要在 replace() 函数中使用 \n、\t 替换换行符或制表符，这会导致问题 。 可以在代码视图中编辑动态内容，以删除表达式中多余的 \，也可以按照以下步骤使用表达式语言替换特殊字符：

1. 针对原始字符串值的 URL 编码
1. 替换 URL 编码的字符串，例如换行符 (%0A)、回车符 (%0D)、水平制表符 (%09)。
1. URL 解码

例如，变量 companyName 的值带有换行符，表达式 `@uriComponentToString(replace(uriComponent(variables('companyName')), '%0A', ''))` 可以删除换行符。 

```json
Contoso-
Corporation
```

### <a name="escaping-single-quote-character"></a>转义单引号字符

表达式函数对字符串值参数使用单引号。 使用两个单引号对字符串函数中的 ' 字符进行转义。 例如，表达式 `@concat('Baba', '''s ', 'book store')` 将返回以下结果。

```
Baba's book store
```

### <a name="tutorial"></a>教程
本[教程](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf)逐步讲解如何在管道和活动之间以及活动之间传递参数。  本教程针对专门 Azure 数据工厂演示了步骤，尽管 Synapse 工作区的步骤几乎相同，但用户界面略有不同。
  
## <a name="functions"></a>函数

可以在表达式中调用函数。 以下各节提供了有关可以在表达式中使用的函数的信息。  

## <a name="string-functions"></a>字符串函数  

若要使用字符串，可以使用这些字符串函数以及某些[集合函数](#collection-functions)。
字符串函数仅适用于字符串。

| 字符串函数 | 任务 |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | 组合两个或更多字符串，并返回组合后的字符串。 |
| [endsWith](control-flow-expression-language-functions.md#endswith) | 检查字符串是否以指定的子字符串结尾。 |
| [guid](control-flow-expression-language-functions.md#guid) | 生成字符串形式的全局唯一标识符 (GUID)。 |
| [indexOf](control-flow-expression-language-functions.md#indexof) | 返回子字符串的起始位置。 |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | 返回最后一次出现的子字符串的起始位置。 |
| [replace](control-flow-expression-language-functions.md#replace) | 将子字符串替换为指定的字符串，并返回更新的字符串。 |
| [split](control-flow-expression-language-functions.md#split) | 根据原始字符串中指定的分隔符字符，从较大字符串中返回一个包含子字符串（以逗号分隔）的数组。 |
| [startsWith](control-flow-expression-language-functions.md#startswith) | 检查字符串是否以特定的子字符串开头。 |
| [substring](control-flow-expression-language-functions.md#substring) | 返回字符串中的字符，从指定的位置开始。 |
| [toLower](control-flow-expression-language-functions.md#toLower) | 返回小写格式的字符串。 |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | 返回大写格式的字符串。 |
| [trim](control-flow-expression-language-functions.md#trim) | 从字符串中删除前导和尾随空格，并返回更新后的字符串。 |

## <a name="collection-functions"></a>集合函数

若要使用集合（通常是数组或字符串，有时是字典），可以使用这些集合函数。

| 集合函数 | 任务 |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | 检查集合是否包含某个特定项。 |
| [empty](control-flow-expression-language-functions.md#empty) | 检查集合是否为空。 |
| [first](control-flow-expression-language-functions.md#first) | 返回集合中的第一个项。 |
| [intersection](control-flow-expression-language-functions.md#intersection) | 返回其中仅包含指定集合的共有项的一个集合。 |
| [join](control-flow-expression-language-functions.md#join) | 返回一个字符串，其中包含某个数组中的所有项并以指定的分隔符分隔每个项。 |
| [last](control-flow-expression-language-functions.md#last) | 返回集合中的最后一个项。 |
| [length](control-flow-expression-language-functions.md#length) | 返回字符串或数组中的项数。 |
| [skip](control-flow-expression-language-functions.md#skip) | 删除集合开头的项，并返回所有其他项。 |
| [take](control-flow-expression-language-functions.md#take) | 返回集合开头的项。 |
| [union](control-flow-expression-language-functions.md#union) | 返回一个集合，其中包含指定集合中的所有项。 | 

## <a name="logical-functions"></a>逻辑函数  

这些函数可在条件中使用，并可用于评估任何类型的逻辑。  
  
| 逻辑比较函数 | 任务 |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | 检查所有表达式是否为 true。 |
| [equals](control-flow-expression-language-functions.md#equals) | 检查两个值是否相等。 |
| [greater](control-flow-expression-language-functions.md#greater) | 检查第一个值是否大于第二个值。 |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | 检查第一个值是否大于或等于第二个值。 |
| [if](control-flow-expression-language-functions.md#if) | 检查表达式为 true 还是 false。 根据结果返回指定的值。 |
| [less](control-flow-expression-language-functions.md#less) | 检查第一个值是否小于第二个值。 |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | 检查第一个值是否小于或等于第二个值。 |
| [not](control-flow-expression-language-functions.md#not) | 检查表达式是否为 false。 |
| [or](control-flow-expression-language-functions.md#or) | 检查是否至少一个表达式为 true。 |
  
## <a name="conversion-functions"></a>转换函数  

 这些函数用于在语言中的每个本机类型之间转换：  
-   字符串
-   integer
-   FLOAT
-   boolean
-   arrays
-   dictionaries

| 转换函数 | 任务 |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | 从单个指定的输入返回数组。 对于多个输入，请参阅 [createArray](control-flow-expression-language-functions.md#createArray)。 |
| [base64](control-flow-expression-language-functions.md#base64) | 返回字符串的 base64 编码版本。 |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | 返回 base64 编码字符串的二进制版本。 |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | 返回 base64 编码字符串的字符串版本。 |
| [binary](control-flow-expression-language-functions.md#binary) | 返回输入值的二进制版本。 |
| [bool](control-flow-expression-language-functions.md#bool) | 返回输入值的布尔值版本。 |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | 返回一个或多个参数中的第一个非 null 值。 |
| [createArray](control-flow-expression-language-functions.md#createArray) | 从多个输入返回数组。 |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | 返回输入值的数据 URI。 |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | 返回数据 URI 的二进制版本。 |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | 返回数据 URI 的字符串版本。 |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | 返回 base64 编码字符串的字符串版本。 |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | 返回数据 URI 的二进制版本。 |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | 返回一个字符串，并将其中的转义字符替换为解码后的版本。 |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | 返回一个字符串，并将其中的 URL 不安全字符替换为转义字符。 |
| [float](control-flow-expression-language-functions.md#float) | 返回输入值的浮点数。 |
| [int](control-flow-expression-language-functions.md#int) | 返回字符串的整数版本。 |
| [json](control-flow-expression-language-functions.md#json) | 返回字符串或 XML 的 JavaScript 对象表示法 (JSON) 类型的值或对象。 |
| [string](control-flow-expression-language-functions.md#string) | 返回输入值的字符串版本。 |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | 通过将 URL 不安全字符替换为转义字符来返回输入值的 URI 编码版本。 |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | 返回 URI 编码字符串的二进制版本。 |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | 返回 URI 编码字符串的字符串版本。 |
| [xml](control-flow-expression-language-functions.md#xml) | 返回字符串的 XML 版本。 |
| [xpath](control-flow-expression-language-functions.md#xpath) | 检查 XML 中是否存在与 XPath（XML 路径语言）表达式匹配的节点或值，并返回匹配的节点或值。 |

## <a name="math-functions"></a>数学函数  
 这些函数可用于以下任一数字类型：**整数** 和 **浮点数**。  

| 数学函数 | 任务 |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | 返回两个数字相加的结果。 |
| [div](control-flow-expression-language-functions.md#div) | 返回两个数字相除的结果。 |
| [max](control-flow-expression-language-functions.md#max) | 返回一组数字或数组中的最大值。 |
| [min](control-flow-expression-language-functions.md#min) | 返回一组数字或数组中的最小值。 |
| [mod](control-flow-expression-language-functions.md#mod) | 返回将两个数字相除后的余数。 |
| [mul](control-flow-expression-language-functions.md#mul) | 返回将两个数字相乘得到的乘积。 |
| [rand](control-flow-expression-language-functions.md#rand) | 返回指定范围内的随机整数。 |
| [range](control-flow-expression-language-functions.md#range) | 返回以指定整数开头的一个整数数组。 |
| [sub](control-flow-expression-language-functions.md#sub) | 返回第一个数字减去第二个数字得到的结果。 |
  
## <a name="date-functions"></a>日期函数  

| 日期或时间函数 | 任务 |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | 将天数加到时间戳。 |
| [addHours](control-flow-expression-language-functions.md#addHours) | 将小时数加到时间戳。 |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | 将分钟数加到时间戳。 |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | 将秒数加到时间戳。 |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | 将一定数目的时间单位加到时间戳。 另请参阅 [getFutureTime](control-flow-expression-language-functions.md#getFutureTime)。 |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | 将时间戳从协调世界时 (UTC) 转换为目标时区。 |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | 将时间戳从源时区转换为目标时区。 |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | 将时间戳从源时区转换为协调世界时 (UTC)。 |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | 返回时间戳中月份组成部分的日期。 |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | 返回时间戳中周组成部分的星期日期。 |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | 返回时间戳中年组成部分的日期。 |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | 以可选格式返回字符串形式的时间戳。 |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | 返回当前时间戳加上指定的时间单位。 另请参阅 [addToTime](control-flow-expression-language-functions.md#addToTime)。 |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | 返回当前时间戳减去指定的时间单位。 另请参阅 [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime)。 |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | 返回时间戳中的天的开始时间。 |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | 返回时间戳中的小时的开始时间。 |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | 返回时间戳中的月份的开始时间。 |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | 从时间戳中减去一定数目的时间单位。 另请参阅 [getPastTime](control-flow-expression-language-functions.md#getPastTime)。 |
| [ticks](control-flow-expression-language-functions.md#ticks) | 返回指定时间戳的 `ticks` 属性值。 |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | 返回字符串形式的当前时间戳。 |

## <a name="function-reference"></a>函数参考

本部分按字母顺序列出所有可用函数。

<a name="add"></a>

### <a name="add"></a>add

返回两个数字相加的结果。

```
add(<summand_1>, <summand_2>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | 是 | 整数、浮点数或混合类型 | 要相加的数字 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| <*result-sum*> | 整数或浮点数 | 指定数字相加的结果。 |
||||

*示例*

此示例将指定的数字相加：

```
add(1, 1.5)
```

并返回以下结果：`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

将天数加到时间戳。

```
addDays('<timestamp>', <days>, '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*days*> | 是 | Integer | 要加上的正负天数 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 时间戳加上指定的天数  |
||||

*示例 1*

此示例将 10 天加到指定的时间戳：

```
addDays('2018-03-15T13:00:00Z', 10)
```

并返回以下结果：`"2018-03-25T00:00:0000000Z"`

*示例 2*

此示例从指定的时间戳减去 5 天：

```
addDays('2018-03-15T00:00:00Z', -5)
```

并返回以下结果：`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

将小时数加到时间戳。

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*hours*> | 是 | Integer | 要加上的正负小时数 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 时间戳加上指定的小时数  |
||||

*示例 1*

此示例将 10 小时加到指定的时间戳：

```
addHours('2018-03-15T00:00:00Z', 10)
```

并返回以下结果：`"2018-03-15T10:00:0000000Z"`

*示例 2*

此示例从指定的时间戳减去 5 小时：

```
addHours('2018-03-15T15:00:00Z', -5)
```

并返回以下结果：`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

将分钟数加到时间戳。

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*minutes*> | 是 | Integer | 要加上的正负分钟数 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 时间戳加上指定的分钟数 |
||||

*示例 1*

此示例将 10 分钟加到指定的时间戳：

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

并返回以下结果：`"2018-03-15T00:20:00.0000000Z"`

*示例 2*

此示例从指定的时间戳减去 5 分钟：

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

并返回以下结果：`"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

将秒数加到时间戳。

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*seconds*> | 是 | Integer | 要加上的正负秒数 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 时间戳加上指定的秒数  |
||||

*示例 1*

此示例将 10 秒加到指定的时间戳：

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

并返回以下结果：`"2018-03-15T00:00:10.0000000Z"`

*示例 2*

此示例从指定的时间戳减去 5 秒：

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

并返回以下结果：`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

将一定数目的时间单位加到时间戳。
另请参阅 [getFutureTime()](#getFutureTime)。

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*间隔*> | 是 | Integer | 要添加的指定时间单位数 |
| <*timeUnit*> | 是 | 字符串 | 间隔使用的时间单位：“秒”、“分钟”、“小时”、“日”、“周”、“月”、“年” |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 时间戳加上指定的时间单位数  |
||||

*示例 1*

此示例将 1 天加到指定的时间戳：

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

并返回以下结果：`"2018-01-02T00:00:00.0000000Z"`

*示例 2*

此示例将 1 天加到指定的时间戳：

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

并使用可选的“D”格式返回结果：`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>and

检查两个表达式是否均为 true。
当两个表达式均为 true 时返回 true，当至少一个表达式为 false 时返回 false。

```
and(<expression1>, <expression2>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <expression1>, <expression2> | 是 | 布尔 | 要检查的表达式 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| true 或 false | 布尔 | 当两个表达式均为 true 时返回 true。 当至少一个表达式为 false 时返回 false。 |
||||

*示例 1*

这些示例检查指定的布尔值是否均为 true：

```
and(true, true)
and(false, true)
and(false, false)
```

并返回以下结果：

* 第一个示例：两个表达式均为 true，因此返回 `true`。
* 第二个示例：一个表达式为 false，因此返回 `false`。
* 第三个示例：两个表达式均为 false，因此返回 `false`。

*示例 2*

这些示例检查指定的表达式是否均为 true：

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

并返回以下结果：

* 第一个示例：两个表达式均为 true，因此返回 `true`。
* 第二个示例：一个表达式为 false，因此返回 `false`。
* 第三个示例：两个表达式均为 false，因此返回 `false`。

<a name="array"></a>

### <a name="array"></a>array

从单个指定的输入返回数组。
对于多个输入，请参阅 [createArray()](#createArray)。

```
array('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 用于创建数组的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | 一个包含单一指定输入的数组 |
||||

*示例*

此示例基于“hello”字符串创建数组：

```
array('hello')
```

并返回以下结果：`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

返回字符串的 base64 编码版本。

```
base64('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 输入字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*base64-string*> | 字符串 | 返回输入字符串的 base64 编码版本 |
||||

*示例*

此示例将“hello”字符串转换为 base64 编码的字符串：

```
base64('hello')
```

并返回以下结果：`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

返回 base64 编码字符串的二进制版本。

```
base64ToBinary('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换的 base64 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | 字符串 | base64 编码字符串的二进制版本 |
||||

*示例*

此示例将 base64 编码的“aGVsbG8=”字符串转换为二进制字符串：

```
base64ToBinary('aGVsbG8=')
```

并返回以下结果：

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

返回 base64 编码字符串的字符串版本，有效地对 base64 字符串进行解码。
请使用此函数而非 [decodeBase64()](#decodeBase64)。
虽然这两个函数的工作方式相同，但首选 `base64ToString()`。

```
base64ToString('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要解码的 base64 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | 字符串 | base64 编码字符串的字符串版本 |
||||

*示例*

此示例将 base64 编码的“aGVsbG8=”字符串转换为单纯的字符串：

```
base64ToString('aGVsbG8=')
```

并返回以下结果：`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

返回字符串的二进制版本。

```
binary('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | 字符串 | 指定字符串的二进制版本 |
||||

*示例*

此示例将“hello”字符串转换为二进制字符串：

```
binary('hello')
```

并返回以下结果：

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

返回值的布尔版本。

```
bool(<value>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 任意 | 要转换的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 指定的值的布尔版本 |
||||

*示例*

这些示例将指定的值转换为布尔值：

```
bool(1)
bool(0)
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

返回一个或多个参数中的第一个非 null 值。
空字符串、空数组和空对象不为 null。

```
coalesce(<object_1>, <object_2>, ...)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | 是 | 任意，可以混用各种类型 | 要检查是否为 null 的一个或多个项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | 任意 | 第一个不为 null 的项或值。 如果所有参数均为 null，则此函数返回 null。 |
||||

*示例*

这些示例返回指定值中的第一个非 null 值，当所有值均为 null 时返回 null：

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`"hello"`
* 第三个示例：`null`

<a name="concat"></a>

### <a name="concat"></a>concat

组合两个或更多字符串，并返回组合后的字符串。

```
concat('<text1>', '<text2>', ...)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | 是 | 字符串 | 至少两个要组合的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*text1text2...* > | 字符串 | 基于组合后的输入字符串创建的字符串 |
||||

*示例*

此示例将字符串“Hello”和“World”组合到一起：

```
concat('Hello', 'World')
```

并返回以下结果：`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

检查集合是否包含某个特定项。
当找到该项目时返回 true，找不到该项目时返回 false。
此函数区分大小写。

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

具体而言，此函数对以下集合类型起作用：

* 字符串，在其中查找子字符串
* 数组，在其中查找值
* 字典，在其中查找键

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字符串、数组或字典 | 要检查的集合 |
| <*value*> | 是 | 分别为字符串、数组或字典 | 要查找的项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当找到该项时返回 true。 找不到时返回 false。 |
||||

*示例 1*

此示例检查字符串“hello world”中是否有子字符串“world”并返回 true：

```
contains('hello world', 'world')
```

*示例 2*

此示例检查字符串“hello world”中是否有子字符串“universe”并返回 false：

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

将时间戳从协调世界时 (UTC) 转换为目标时区。

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*destinationTimeZone*> | 是 | 字符串 | 目标时区的名称。 有关时区名称，请参阅 [Microsoft 时区索引值](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)，但你可能需要删除时区名称中的任何标点。 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | 字符串 | 已转换为目标时区的时间戳 |
||||

*示例 1*

此示例将时间戳转换为指定的时区：

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

并返回以下结果：`"2018-01-01T00:00:00Z"`

*示例 2*

此示例将时间戳转换为指定的时区和格式：

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

并返回以下结果：`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

将时间戳从源时区转换为目标时区。

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*sourceTimeZone*> | 是 | 字符串 | 源时区的名称。 有关时区名称，请参阅 [Microsoft 时区索引值](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)，但你可能需要删除时区名称中的任何标点。 |
| <*destinationTimeZone*> | 是 | 字符串 | 目标时区的名称。 有关时区名称，请参阅 [Microsoft 时区索引值](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)，但你可能需要删除时区名称中的任何标点。 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | 字符串 | 已转换为目标时区的时间戳 |
||||

*示例 1*

此示例将源时区转换为目标时区：

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

并返回以下结果：`"2018-01-01T00:00:00.0000000"`

*示例 2*

此示例将时区转换为指定的时区和格式：

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

并返回以下结果：`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

将时间戳从源时区转换为协调世界时 (UTC)。

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*sourceTimeZone*> | 是 | 字符串 | 源时区的名称。 有关时区名称，请参阅 [Microsoft 时区索引值](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)，但你可能需要删除时区名称中的任何标点。 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | 字符串 | 已转换为 UTC 的时间戳 |
||||

*示例 1*

此示例将时间戳转换为 UTC：

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

并返回以下结果：`"2018-01-01T08:00:00.0000000Z"`

*示例 2*

此示例将时间戳转换为 UTC：

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

并返回以下结果：`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

从多个输入返回数组。
对于单输入数组，请参阅 [array()](#array)。

```
createArray('<object1>', '<object2>', ...)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | 是 | 任意，但不能混用 | 至少两个用于创建数组的项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | 基于所有输入项创建的数组 |
||||

*示例*

此示例基于以下输入创建数组：

```
createArray('h', 'e', 'l', 'l', 'o')
```

并返回以下结果：`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

返回字符串的数据统一资源标识符 (URI)。

```
dataUri('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*data-uri*> | 字符串 | 输入字符串的数据 URI |
||||

*示例*

此示例创建“hello”字符串的数据 URI：

```
dataUri('hello')
```

并返回以下结果：`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

返回数据统一资源标识符 (URI) 的二进制版本。
请使用此函数而非 [decodeDataUri()](#decodeDataUri)。
虽然这两个函数的工作方式相同，但首选 `dataUriBinary()`。

```
dataUriToBinary('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换的数据 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | 字符串 | 数据 URI 的二进制版本 |
||||

*示例*

此示例创建以下数据 URI 的二进制版本：

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

并返回以下结果：

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

返回数据统一资源标识符 (URI) 的字符串版本。

```
dataUriToString('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换的数据 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | 字符串 | 数据 URI 的字符串版本 |
||||

*示例*

此示例创建以下数据 URI 的字符串：

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

并返回以下结果：`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

基于时间戳返回月中的某天。

```
dayOfMonth('<timestamp>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Integer | 基于指定的时间戳返回月中的某天 |
||||

*示例*

此示例基于以下时间戳返回月中的某天：

```
dayOfMonth('2018-03-15T13:27:36Z')
```

并返回以下结果：`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

基于时间戳返回周几。

```
dayOfWeek('<timestamp>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Integer | 指定的时间戳中的周几，其中周日为 0，周一为 1，依此类推 |
||||

*示例*

此示例基于以下时间戳返回周几：

```
dayOfWeek('2018-03-15T13:27:36Z')
```

并返回以下结果：`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

基于时间戳返回年中的某天。

```
dayOfYear('<timestamp>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | 基于指定的时间戳返回年中的某天 |
||||

*示例*

此示例基于以下时间戳返回年中的某天：

```
dayOfYear('2018-03-15T13:27:36Z')
```

并返回以下结果：`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

返回 base64 编码字符串的字符串版本，有效地对 base64 字符串进行解码。
请考虑使用 [base64ToString()](#base64ToString) 而非 `decodeBase64()`。
虽然这两个函数的工作方式相同，但首选 `base64ToString()`。

```
decodeBase64('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要解码的 base64 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | 字符串 | base64 编码字符串的字符串版本 |
||||

*示例*

此示例创建一个 base64 编码字符串的字符串：

```
decodeBase64('aGVsbG8=')
```

并返回以下结果：`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

返回数据统一资源标识符 (URI) 的二进制版本。
请考虑使用 [dataUriToBinary()](#dataUriToBinary) 而非 `decodeDataUri()`。
虽然这两个函数的工作方式相同，但首选 `dataUriToBinary()`。

```
decodeDataUri('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要解码的数据 URI 字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | 字符串 | 数据 URI 字符串的二进制版本 |
||||

*示例*

此示例返回以下数据 URI 的二进制版本：

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

并返回以下结果：

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

返回一个字符串，并将其中的转义字符替换为解码后的版本。

```
decodeUriComponent('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 包含要解码的转义字符的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | 字符串 | 包含解码后的转义字符的更新后字符串 |
||||

*示例*

此示例将以下字符串中的转义字符替换为解码后的版本：

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

并返回以下结果：`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

返回将两个数字相除后的整数结果。
若要获取余数结果，请参阅 [mod()](#mod)。

```
div(<dividend>, <divisor>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | 是 | 整数或浮点数 | 要用作 *divisor* 的被除数的数字 |
| <*divisor*> | 是 | 整数或浮点数 | 用作 *dividend* 的除数的数字，但不能为 0 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer | 将第一个数字除以第二个数字后得到的整数结果 |
||||

*示例*

两个示例都将第一个数字除以第二个数字：

```
div(10, 5)
div(11, 5)
```

并返回以下结果：`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

通过将 URL 不安全字符替换为转义字符来返回字符串的统一资源标识符 (URI) 编码版本。
请考虑使用 [uriComponent()](#uriComponent) 而非 `encodeUriComponent()`。
虽然这两个函数的工作方式相同，但首选 `uriComponent()`。

```
encodeUriComponent('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换为 URI 编码格式的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | 字符串 | 带有转义字符的 URI 编码字符串 |
||||

*示例*

此示例创建以下字符串的 URI 编码版本：

```
encodeUriComponent('https://contoso.com')
```

并返回以下结果：`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

检查集合是否为空。
当集合为空时返回 true，不为空时返回 false。

```
empty('<collection>')
empty([<collection>])
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字符串、数组或对象 | 要检查的集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当集合为空时返回 true。 不为空时返回 false。 |
||||

*示例*

这些示例检查指定的集合是否为空：

```
empty('')
empty('abc')
```

并返回以下结果：

* 第一个示例：传递一个空字符串，所以此函数返回 `true`。
* 第二个示例：传递字符串“abc”，所以此函数返回 `false`。

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

检查字符串是否以特定的子字符串结尾。
当找到该子字符串时返回 true，找不到时返回 false。
此函数不区分大小写。

```
endsWith('<text>', '<searchText>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 要检查的字符串 |
| <*searchText*> | 是 | 字符串 | 要查找的结尾子字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false  | 布尔 | 当找到结尾子字符串时返回 true。 找不到时返回 false。 |
||||

*示例 1*

此示例检查“hello world”字符串是否以“world”字符串结尾：

```
endsWith('hello world', 'world')
```

并返回以下结果：`true`

*示例 2*

此示例检查“hello world”字符串是否以“universe”字符串结尾：

```
endsWith('hello world', 'universe')
```

并返回以下结果：`false`

<a name="equals"></a>

### <a name="equals"></a>equals

检查两个值、表达式或对象是否相等。
当两者相等时返回 true，两者不相等时返回 false。

```
equals('<object1>', '<object2>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | 是 | 各种 | 要比较的值、表达式或对象 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当两者相等时返回 true。 不相等时返回 false。 |
||||

*示例*

这些示例检查指定的输入是否相等。

```
equals(true, 1)
equals('abc', 'abcd')
```

并返回以下结果：

* 第一个示例：两个值相等，所以此函数返回 `true`。
* 第二个示例：两个值不相等，所以此函数返回 `false`。

<a name="first"></a>

### <a name="first"></a>first

返回字符串或数组中的第一项。

```
first('<collection>')
first([<collection>])
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字符串或数组 | 要在其中查找第一项的集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | 任意 | 集合中的第一项 |
||||

*示例*

这些示例查找以下集合中的第一项：

```
first('hello')
first(createArray(0, 1, 2))
```

并返回以下结果：

* 第一个示例：`"h"`
* 第二个示例：`0`

<a name="float"></a>

### <a name="float"></a>FLOAT

将浮点数的字符串版本转换为实际的浮点数。

```
float('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 包含要转换的有效浮点数的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | 指定的字符串的浮点数 |
||||

*示例*

此示例创建以下浮点数的字符串版本：

```
float('10.333')
```

并返回以下结果：`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

以指定的格式返回时间戳。

```
formatDateTime('<timestamp>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | 字符串 | 采用指定格式的更新后时间戳 |
||||

*示例*

此示例将时间戳转换为指定的格式：

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

并返回以下结果：`"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

返回当前时间戳加上指定的时间单位。

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*间隔*> | 是 | Integer | 要添加的指定时间单位数 |
| <*timeUnit*> | 是 | 字符串 | 间隔使用的时间单位：“秒”、“分钟”、“小时”、“日”、“周”、“月”、“年” |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 当前时间戳加上指定的时间单位数 |
||||

*示例 1*

假设当前时间戳是“2018-03-01T00:00:00.0000000Z”。
此示例将 5 天加到该时间戳：

```
getFutureTime(5, 'Day')
```

并返回以下结果：`"2018-03-06T00:00:00.0000000Z"`

*示例 2*

假设当前时间戳是“2018-03-01T00:00:00.0000000Z”。
此示例加上 5 天，并将结果转换为“D”格式：

```
getFutureTime(5, 'Day', 'D')
```

并返回以下结果：`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

返回当前时间戳减去指定的时间单位。

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*间隔*> | 是 | Integer | 要减去的指定时间单位数 |
| <*timeUnit*> | 是 | 字符串 | 间隔使用的时间单位：“秒”、“分钟”、“小时”、“日”、“周”、“月”、“年” |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 当前时间戳减去指定的时间单位数 |
||||

*示例 1*

假设当前时间戳是“2018-02-01T00:00:00.0000000Z”。
此示例从该时间戳减去 5 天：

```
getPastTime(5, 'Day')
```

并返回以下结果：`"2018-01-27T00:00:00.0000000Z"`

*示例 2*

假设当前时间戳是“2018-02-01T00:00:00.0000000Z”。
此示例减去 5 天，并将结果转换为“D”格式：

```
getPastTime(5, 'Day', 'D')
```

并返回以下结果：`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

检查第一个值是否大于第二个值。
如果第一个值大，则返回 true；如果第一个值小，则返回 false。

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 整数、浮点数或字符串 | 要检查是否大于第二个值的第一个值。 |
| <*compareTo*> | 是 | 分别为整数、浮点数或字符串 | 比较值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当第一个值大于第二个值时返回 true。 当第一个值等于或小于第二个值时返回 false。 |
||||

*示例*

这些示例检查第一个值是否大于第二个值：

```
greater(10, 5)
greater('apple', 'banana')
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

检查第一个值是否大于或等于第二个值。
当第一个值大于或等于第二个值时返回 true，当第一个值小于第二个值时返回 false。

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 整数、浮点数或字符串 | 要检查是否大于或等于第二个值的第一个值 |
| <*compareTo*> | 是 | 分别为整数、浮点数或字符串 | 比较值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当第一个值大于或等于第二个值时返回 true。 当第一个值小于第二个值时返回 false。 |
||||

*示例*

这些示例检查第一个值是否大于或等于第二个值：

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="guid"></a>

### <a name="guid"></a>guid

生成一个字符串形式的全局唯一标识符 (GUID)，例如“c2ecc88d-88c8-4096-912c-d6f2e2b138ce”：

```
guid()
```

此外，还可以为 GUID 指定与默认格式“D”（由连字符分隔的 32 位数字）不同的格式。

```
guid('<format>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*format*> | 否 | 字符串 | 表示返回的 GUID 的单一[格式说明符](/dotnet/api/system.guid.tostring)。 默认情况下，格式为“D”，但可以使用“N”、“D”、“B”、“P”或“X”。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*GUID-value*> | 字符串 | 随机生成的 GUID |
||||

*示例*

此示例生成相同的 GUID，但它是 32 位的，以连字符分隔并括在括号中：

```
guid('P')
```

并返回以下结果：`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

检查表达式为 true 还是 false。
根据结果返回指定的值。

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | 是 | 布尔 | 要检查的表达式 |
| <*valueIfTrue*> | 是 | 任意 | 当表达式为 true 时要返回的值 |
| <*valueIfFalse*> | 是 | 任意 | 当表达式为 false 时要返回的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | 任意 | 根据表达式为 true 或 false 返回的指定值 |
||||

*示例*

此示例返回 `"yes"`，因为指定的表达式返回 true。
否则，此示例返回 `"no"`：

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

返回子字符串的起始位置或索引值。
此函数不区分大小写，并且索引从数字 0 开始。

```
indexOf('<text>', '<searchText>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 包含要查找的子字符串的字符串 |
| <*searchText*> | 是 | 字符串 | 要查找的子字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | 指定的子字符串的起始位置或索引值。 <p>如果未找到该字符串，则返回数字 -1。 |
||||

*示例*

此示例查找“hello world”字符串中的“world”子字符串的起始索引值：

```
indexOf('hello world', 'world')
```

并返回以下结果：`6`

<a name="int"></a>

### <a name="int"></a>int

返回字符串的整数版本。

```
int('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | 指定的字符串的整数版本 |
||||

*示例*

此示例创建字符串“10”的整数版本：

```
int('10')
```

并返回以下结果：`10`

<a name="json"></a>

### <a name="json"></a>json

返回字符串或 XML 的 JavaScript 对象表示法 (JSON) 类型的值或对象。

```
json('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串或 XML | 要转换的字符串或 XML |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*JSON-result*> | JSON 本机类型或对象 | 指定的字符串或 XML 的 JSON 本机类型的值或对象。 如果字符串为 null，则此函数返回一个空对象。 |
||||

*示例 1*

此示例将以下字符串转换为 JSON 值：

```
json('[1, 2, 3]')
```

并返回以下结果：`[1, 2, 3]`

*示例 2*

此示例将以下字符串转换为 JSON：

```
json('{"fullName": "Sophia Owen"}')
```

并返回以下结果：

```
{
  "fullName": "Sophia Owen"
}
```

*示例 3*

此示例将以下 XML 转换为 JSON：

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

并返回以下结果：

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>intersection

返回其中仅包含指定集合的共有项的一个集合。
某个项必须出现在传递给此函数的所有集合中才会出现在结果中。
如果一个或多个项具有相同的名称，则具有该名称的最后一项将出现在结果中。

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | 是 | 数组或对象，但不能为两者 | 仅需从中获取共有项的各个集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*common-items*> | 分别为数组或对象 | 其中仅包含指定集合的共有项的一个集合 |
||||

*示例*

此示例查找以下数组中的共有项：

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

并返回“仅”包含这些项的数组：`[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

返回一个字符串，它包含某个数组中的所有项并且以分隔符分隔每个字符。

```
join([<collection>], '<delimiter>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | Array | 包含要联接的项的数组 |
| <*delimiter*> | 是 | 字符串 | 出现在结果字符串中的每个字符之间的分隔符 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | 字符串 | 基于指定数组中的所有项创建的结果字符串 |
||||

*示例*

此示例基于以下数组中的所有项创建字符串，并且以指定的字符作为分隔符：

```
join(createArray('a', 'b', 'c'), '.')
```

并返回以下结果：`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

返回集合中的最后一个项。

```
last('<collection>')
last([<collection>])
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字符串或数组 | 要在其中查找最后一项的集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | 分别为字符串或数组 | 集合中的最后一项 |
||||

*示例*

这些示例查找以下集合中的最后一项：

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

并返回以下结果：

* 第一个示例：`"d"`
* 第二个示例：`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

返回最后一次出现的子字符串的起始位置或索引值。
此函数不区分大小写，并且索引从数字 0 开始。

```
lastIndexOf('<text>', '<searchText>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 包含要查找的子字符串的字符串 |
| <*searchText*> | 是 | 字符串 | 要查找的子字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | 最后一次出现的指定子字符串的起始位置或索引值。 <p>如果未找到该字符串，则返回数字 -1。 |
||||

*示例*

此示例查找“hello world”字符串中最后一次出现的“world”子字符串的起始索引值：

```
lastIndexOf('hello world', 'world')
```

并返回以下结果：`6`

<a name="length"></a>

### <a name="length"></a>length

返回集合中的项数。

```
length('<collection>')
length([<collection>])
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字符串或数组 | 包含要计数的项的集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | 集合中的项数 |
||||

*示例*

这些示例对以下集合中的项数进行计数：

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

并返回以下结果：`4`

<a name="less"></a>

### <a name="less"></a>less

检查第一个值是否小于第二个值。
如果第一个值小，则返回 true；如果第一个值大，则返回 false。

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 整数、浮点数或字符串 | 要检查是否小于第二个值的第一个值 |
| <*compareTo*> | 是 | 分别为整数、浮点数或字符串 | 比较项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当第一个值小于第二个值时返回 true。 当第一个值等于或大于第二个值时返回 false。 |
||||

*示例*

这些示例检查第一个值是否小于第二个值。

```
less(5, 10)
less('banana', 'apple')
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

检查第一个值是否小于或等于第二个值。
当第一个值小于或等于第二个值时返回 true，当第一个值大时返回 false。

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 整数、浮点数或字符串 | 要检查是否小于或等于第二个值的第一个值 |
| <*compareTo*> | 是 | 分别为整数、浮点数或字符串 | 比较项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false  | 布尔 | 当第一个值小于或等于第二个值时返回 true。 当第一个值大于第二个值时返回 false。 |
||||

*示例*

这些示例检查第一个值是否小于或等于第二个值。

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="max"></a>

### <a name="max"></a>max

返回列表中或包括两端数字在内的数组中的最大值。

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | 是 | 整数、浮点数或两者 | 需要从中获取最大值的数字集 |
| [<*number1*>, <*number2*>, ...] | 是 | 数组 - 整数、浮点数或两者 | 需要从中获取最大值的数字数组 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*max-value*> | 整数或浮点数 | 指定的数组或数字集中的最大值 |
||||

*示例*

这些示例获取数字集和数组中的最大值：

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

并返回以下结果：`3`

<a name="min"></a>

### <a name="min"></a>min

返回一组数字或数组中的最小值。

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | 是 | 整数、浮点数或两者 | 需要从中获取最小值的数字集 |
| [<*number1*>, <*number2*>, ...] | 是 | 数组 - 整数、浮点数或两者 | 需要从中获取最小值的数字数组 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*min-value*> | 整数或浮点数 | 指定的数字集或指定的数组中的最小值 |
||||

*示例*

这些示例获取数字集和数组中的最小值：

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

并返回以下结果：`1`

<a name="mod"></a>

### <a name="mod"></a>mod

返回将两个数字相除后的余数。
若要获取整数结果，请参阅 [div()](#div)。

```
mod(<dividend>, <divisor>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | 是 | 整数或浮点数 | 要用作 *divisor* 的被除数的数字 |
| <*divisor*> | 是 | 整数或浮点数 | 用作 *dividend* 的除数的数字，但不能为 0。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*modulo-result*> | 整数或浮点数 | 将第一个数字除以第二个数字后得到的余数 |
||||

*示例*

此示例将第一个数字除以第二个数字：

```
mod(3, 2)
```

并返回以下结果：`1`

<a name="mul"></a>

### <a name="mul"></a>mul

返回将两个数字相乘得到的乘积。

```
mul(<multiplicand1>, <multiplicand2>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | 是 | 整数或浮点数 | 要与 *multiplicand2* 相乘的数字 |
| <*multiplicand2*> | 是 | 整数或浮点数 | 要与 *multiplicand1* 相乘的数字 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*product-result*> | 整数或浮点数 | 将第一个数字乘以第二个数字后得到的乘积 |
||||

*示例*

这些示例将第一个数字乘以第二个数字：

```
mul(1, 2)
mul(1.5, 2)
```

并返回以下结果：

* 第一个示例：`2`
* 第二个示例：`3`

<a name="not"></a>

### <a name="not"></a>not

检查表达式是否为 false。
当表达式为 false 时返回 true，当表达式为 true 时返回 false。

```json
not(<expression>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | 是 | 布尔 | 要检查的表达式 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当表达式为 false 时返回 true。 当表达式为 true 时返回 false。 |
||||

*示例 1*

这些示例检查指定的表达式是否为 false：

```json
not(false)
not(true)
```

并返回以下结果：

* 第一个示例：表达式为 false，所以此函数返回 `true`。
* 第二个示例：表达式为 true，所以此函数返回 `false`。

*示例 2*

这些示例检查指定的表达式是否为 false：

```json
not(equals(1, 2))
not(equals(1, 1))
```

并返回以下结果：

* 第一个示例：表达式为 false，所以此函数返回 `true`。
* 第二个示例：表达式为 true，所以此函数返回 `false`。

<a name="or"></a>

### <a name="or"></a>或

检查是否至少一个表达式为 true。
当至少一个表达式为 true 时返回 true，当两个表达式均为 false 时返回 false。

```
or(<expression1>, <expression2>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <expression1>, <expression2> | 是 | 布尔 | 要检查的表达式 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当至少一个表达式为 true 时返回 true。 当两个表达式均为 false 时返回 false。 |
||||

*示例 1*

这些示例检查是否至少一个表达式为 true：

```json
or(true, false)
or(false, false)
```

并返回以下结果：

* 第一个示例：至少一个表达式为 true，所以此函数返回 `true`。
* 第二个示例：两个表达式均为 false，所以此函数返回 `false`。

*示例 2*

这些示例检查是否至少一个表达式为 true：

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

并返回以下结果：

* 第一个示例：至少一个表达式为 true，所以此函数返回 `true`。
* 第二个示例：两个表达式均为 false，所以此函数返回 `false`。

<a name="rand"></a>

### <a name="rand"></a>rand

返回指定范围（仅包括起端在内）中的一个随机整数。

```
rand(<minValue>, <maxValue>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | 是 | Integer | 范围中的最小整数 |
| <*maxValue*> | 是 | Integer | 此函数可以返回的范围中的最大整数之后的整数 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | 从指定范围中返回的随机整数 |
||||

*示例*

此示例从指定范围内获取一个随机整数，不包括最大值：

```
rand(1, 5)
```

并返回下列数字之一作为结果：`1`、`2`、`3` 或 `4`

<a name="range"></a>

### <a name="range"></a>range

返回以指定整数开头的一个整数数组。

```
range(<startIndex>, <count>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | 是 | Integer | 作为数组开头的第一项的整数值 |
| <*count*> | 是 | Integer | 数组中的整数个数 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | 从指定索引开始的整数组成的数组 |
||||

*示例*

此示例创建一个整数数组，从指定索引开始，并包含指定数目的整数：

```
range(1, 4)
```

并返回以下结果：`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

将子字符串替换为指定字符串，并返回结果字符串。 此函数区分大小写。

```
replace('<text>', '<oldText>', '<newText>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 包含要替换的子字符串的字符串 |
| <*oldText*> | 是 | 字符串 | 要替换的子字符串 |
| <*newText*> | 是 | 字符串 | 替换字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-text*> | 字符串 | 替换子字符串后得到的更新后字符串 <p>如果未找到子字符串，则返回原始字符串。 |
||||

*示例*

此示例查找“the old string”中的“old”子字符串并将“old”替换为“new”：

```
replace('the old string', 'old', 'new')
```

并返回以下结果：`"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

删除集合开头的项，并返回所有其他项。

```
skip([<collection>], <count>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | Array | 要删除其项的集合 |
| <*count*> | 是 | Integer | 要从开头删除的项数（一个正整数） |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | 删除指定项后得到的更新后的集合 |
||||

*示例*

此示例从指定数组的开头删除一个项（数字 0）：

```
skip(createArray(0, 1, 2, 3), 1)
```

并返回包含剩余项的以下数组：`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

根据原始字符串中指定的分隔符字符，返回一个包含子字符串（以逗号分隔）的数组。

```
split('<text>', '<delimiter>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 根据原始字符串中指定的分隔符将分隔成子字符串的字符串 |
| <*delimiter*> | 是 | 字符串 | 原始字符串中用作分隔符的字符 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | 一个数组，其中包含从原始字符串返回以逗号分隔的子字符串 |
||||

*示例*

此示例根据作为分隔符的指定字符，从指定字符串创建具有子字符串的数组：

```
split('a_b_c', '_')
```

并将此数组作为以下结果返回：`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

返回时间戳中的天的开始时间。

```
startOfDay('<timestamp>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 指定的时间戳，但从该天的零小时标记开始 |
||||

*示例*

此示例查找以下时间戳中的天的开始时间：

```
startOfDay('2018-03-15T13:30:30Z')
```

并返回以下结果：`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

返回时间戳中的小时的开始时间。

```
startOfHour('<timestamp>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 指定的时间戳，但从该小时的零分钟标记开始 |
||||

*示例*

此示例查找以下时间戳中的小时的开始时间：

```
startOfHour('2018-03-15T13:30:30Z')
```

并返回以下结果：`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

返回时间戳中的月份的开始时间。

```
startOfMonth('<timestamp>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 指定的时间戳，但从该月第一天的零小时标记开始 |
||||

*示例*

此示例查找以下时间戳中月份的开始时间：

```
startOfMonth('2018-03-15T13:30:30Z')
```

并返回以下结果：`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

检查字符串是否以特定的子字符串开头。
当找到该子字符串时返回 true，找不到时返回 false。
此函数不区分大小写。

```
startsWith('<text>', '<searchText>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 要检查的字符串 |
| <*searchText*> | 是 | 字符串 | 要查找的起始字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false  | 布尔 | 当找到起始子字符串时返回 true。 找不到时返回 false。 |
||||

*示例 1*

此示例检查“hello world”字符串是否以“hello”子字符串开头：

```
startsWith('hello world', 'hello')
```

并返回以下结果：`true`

*示例 2*

此示例检查“hello world”字符串是否以“greetings”子字符串开头：

```
startsWith('hello world', 'greetings')
```

并返回以下结果：`false`

<a name="string"></a>

### <a name="string"></a>字符串

返回值的字符串版本。

```
string(<value>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 任意 | 要转换的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*string-value*> | 字符串 | 指定的值的字符串版本 |
||||

*示例 1*

此示例创建以下数字的字符串版本：

```
string(10)
```

并返回以下结果：`"10"`

*示例 2*

此示例为指定的 JSON 对象创建字符串并使用反斜杠字符 (\\) 作为双引号 (") 的转义字符。

```
string( { "name": "Sophie Owen" } )
```

并返回以下结果：`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

返回第一个数字减去第二个数字得到的结果。

```
sub(<minuend>, <subtrahend>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | 是 | 整数或浮点数 | 要从中减去 *subtrahend* 的数字 |
| <*subtrahend*> | 是 | 整数或浮点数 | 要从 *minuend* 中减去的数字 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*result*> | 整数或浮点数 | 第一个数字减去第二个数字得到的结果 |
||||

*示例*

此示例从第一个数字中减去第二个数字：

```
sub(10.3, .3)
```

并返回以下结果：`10`

<a name="substring"></a>

### <a name="substring"></a>substring

返回字符串中的字符，从指定的位置或索引开始。
索引值从数字 0 开始。

```
substring('<text>', <startIndex>, <length>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 所需字符所在的字符串 |
| <*startIndex*> | 是 | Integer | 一个等于或大于 0 的正数，需将其用作起始位置或索引值 |
| <*length*> | 是 | Integer | 希望子字符串中具有的字符数（正数） |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*substring-result*> | 字符串 | 包含指定字符数的子字符串，从源字符串中的指定索引位置开始 |
||||

*示例*

此示例基于指定的字符串从索引值 6 开始创建一个五字符的子字符串：

```
substring('hello world', 6, 5)
```

并返回以下结果：`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

从时间戳中减去一定数目的时间单位。
另请参阅 [getPastTime](#getPastTime)。

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 包含时间戳的字符串 |
| <*间隔*> | 是 | Integer | 要减去的指定时间单位数 |
| <*timeUnit*> | 是 | 字符串 | 间隔使用的时间单位：“秒”、“分钟”、“小时”、“日”、“周”、“月”、“年” |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | 字符串 | 时间戳减去指定的时间单位数 |
||||

*示例 1*

此示例从以下时间戳中减去 1 天：

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

并返回以下结果：`"2018-01-01T00:00:00:0000000Z"`

*示例 2*

此示例从以下时间戳中减去 1 天：

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

并使用可选的“D”格式返回以下结果：`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

返回集合开头的项。

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字符串或数组 | 所需项所在的集合 |
| <*count*> | 是 | Integer | 从开头算起所需的项数（一个正整数） |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*subset*> or [<*subset*>] | 分别为字符串或数组 | 一个字符串或数组，其中包含来自原始集合开头的指定数目的项 |
||||

*示例*

这些示例从以下集合的开头获取指定的项数：

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

并返回以下结果：

* 第一个示例：`"abc"`
* 第二个示例：`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

返回指定时间戳的 `ticks` 属性值。
一个“时钟周期”是 100 纳秒时间间隔。

```
ticks('<timestamp>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | 字符串 | 时间戳的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | 从指定的时间戳算起已过去的时钟周期数 |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

返回小写格式的字符串。 如果字符串中的某个字符没有对应的小写版本，则该字符在返回的字符串中保持原样。

```
toLower('<text>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 要以小写格式返回的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | 字符串 | 采用小写格式的原始字符串 |
||||

*示例*

此示例将以下字符串转换为小写：

```
toLower('Hello World')
```

并返回以下结果：`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

返回大写格式的字符串。 如果字符串中的某个字符没有对应的大写版本，则该字符在返回的字符串中保持原样。

```
toUpper('<text>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 要以大写格式返回的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | 字符串 | 采用大写格式的原始字符串 |
||||

*示例*

此示例将以下字符串转换为大写：

```
toUpper('Hello World')
```

并返回以下结果：`"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>trim

从字符串中删除前导和尾随空格，并返回更新后的字符串。

```
trim('<text>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | 字符串 | 包含要删除的前导和尾随空格的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updatedText*> | 字符串 | 原始字符串的更新后版本，其中不含前导或尾随空格 |
||||

*示例*

此示例从字符串“Hello World”中删除前导和尾随空格：

```
trim(' Hello World  ')
```

并返回以下结果：`"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

返回一个集合，其中包含指定集合中的所有项。
某个项只要出现在传递给此函数的任一集合中便会出现在结果中。 如果一个或多个项具有相同的名称，则具有该名称的最后一项将出现在结果中。

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | 是 | 数组或对象，但不能为两者 | 需要从中获取所有项的各个集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | 分别为数组或对象 | 一个集合，其中包含指定集合中的所有项 - 无重复项 |
||||

*示例*

此示例获取以下集合中的“所有”项：

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

并返回以下结果：`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

通过将 URL 不安全字符替换为转义字符来返回字符串的统一资源标识符 (URI) 编码版本。
请使用此函数而非 [encodeUriComponent()](#encodeUriComponent)。
虽然这两个函数的工作方式相同，但首选 `uriComponent()`。

```
uriComponent('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换为 URI 编码格式的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | 字符串 | 带有转义字符的 URI 编码字符串 |
||||

*示例*

此示例创建以下字符串的 URI 编码版本：

```
uriComponent('https://contoso.com')
```

并返回以下结果：`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

返回统一资源标识符 (URI) 组件的二进制版本。

```
uriComponentToBinary('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换的 URI 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | 字符串 | URI 编码字符串的二进制版本。 二进制内容是 base64 编码的，并且由 `$content` 表示。 |
||||

*示例*

此示例创建以下 URI 编码字符串的二进制版本：

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

并返回以下结果：

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

返回统一资源标识符 (URI) 编码字符串的字符串版本，有效地对 URI 编码字符串进行解码。

```
uriComponentToString('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要解码的 URI 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | 字符串 | URI 编码字符串的解码后的版本 |
||||

*示例*

此示例创建以下 URI 编码字符串的解码后字符串版本：

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

并返回以下结果：`"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

返回当前时间戳。

```
utcNow('<format>')
```

另外，也可以使用 <*format*> 参数指定不同的格式。

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*format*> | 否 | 字符串 | [单一格式的说明符](/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | 字符串 | 当前日期和时间 |
||||

*示例 1*

假设今天的当前时间为 2018 年 4 月 15 日下午 1:00:00。
此示例获取当前时间戳：

```
utcNow()
```

并返回以下结果：`"2018-04-15T13:00:00.0000000Z"`

*示例 2*

假设今天的当前时间为 2018 年 4 月 15 日下午 1:00:00。
此示例使用可选的“D”格式获取当前时间戳：

```
utcNow('D')
```

并返回以下结果：`"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

返回包含 JSON 对象的字符串的 XML 版本。

```
xml('<value>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字符串 | 要转换的包含 JSON 对象的字符串 <p>该 JSON 对象必须只有一个根属性，该属性不能是数组。 <br>请使用反斜杠字符 (\\) 作为双引号 (") 的转义字符。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*xml-version*> | 对象 | 指定的字符串或 JSON 对象的编码 XML |
||||

*示例 1*

此示例创建包含 JSON 对象的以下字符串的 XML 版本：

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

并返回以下结果 XML：

```xml
<name>Sophia Owen</name>
```

*示例 2*

假设具有以下 JSON 对象：

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

此示例创建包含以下 JSON 对象的字符串的 XML：

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

并返回以下结果 XML：

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

检查 XML 中是否存在与 XPath（XML 路径语言）表达式匹配的节点或值，并返回匹配的节点或值。 XPath 表达式或单纯的“XPath”有助于在 XML 文档结构中导航，以便可以在 XML 内容中选择节点或计算值。

```
xpath('<xml>', '<xpath>')
```

| 参数 | 必需 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*xml*> | 是 | 任意 | 要在其中搜索与 XPath 表达式值匹配的节点或值的 XML 字符串 |
| <*xpath*> | 是 | 任意 | 用来查找匹配的 XML 节点或值的 XPath 表达式 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | 一个 XML 节点，当只有单个节点与指定的 XPath 表达式匹配时 |
| <*value*> | 任意 | 来自一个 XML 节点的值，当只有单个值与指定的 XPath 表达式匹配时 |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-或- </br>[<*value1*>, <*value2*>, ...] | Array | 一个数组，其中包含与指定的 XPath 表达式匹配的 XML 节点或值 |
||||

*示例 1*

在示例 1 的基础上，此示例查找与 `<count></count>` 节点匹配的节点并通过 `sum()` 函数添加这些节点值：

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

并返回以下结果：`30`

*示例 2*

对于此示例，两个表达式都查找与指定参数（其中通过命名空间包括了 XML）中的 `<location></location>` 节点匹配的节点。 表达式使用反斜杠字符 (\\) 作为双引号 (") 的转义字符。

* *表达式 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *表达式 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

下面是参数：

* 以下 XML，其中包括 XML 文档命名空间 `xmlns="http://contoso.com"`：

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* 以下任一 XPath 表达式：

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

下面是与 `<location></location>` 节点匹配的结果节点：

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*示例 3*

在示例 3 的基础上，此示例在 `<location></location>` 节点中查找值：

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

并返回以下结果：`"Paris"`

## <a name="next-steps"></a>后续步骤
对于可以在表达式中使用的系统变量列表，请参阅[系统变量](control-flow-system-variables.md)。
