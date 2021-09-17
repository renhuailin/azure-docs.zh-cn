---
title: 如何在 Azure 数据工厂中使用参数和表达式
description: 本操作指南文章介绍在创建数据工厂实体时可以使用的表达式和函数。
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 41689a842bff067fb36dd97f980c10b71e533f8d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446212"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>如何在 Azure 数据工厂中使用参数、表达式和函数

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-functions-variables.md)
> * [当前版本](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文档的重点主要在于通过各种示例介绍基本概念，以探索在 Azure 数据工厂中创建参数化数据管道的能力。 参数化和动态表达式是对 ADF 的重要补充，因为它们可以节省大量的时间，并实现更灵活的提取、转换、加载 (ETL) 或提取、加载、转换 (ELT) 解决方案，这极大地降低了解决方案的维护成本，加快了新功能在现有管道中的实现。 这些优势是因为参数化最大限度地降低了硬编码数量，并增加了解决方案中可重用对象和进程的数量。

## <a name="azure-data-factory-ui-and-parameters"></a>Azure 数据工厂 UI 和参数

如果你不熟悉 ADF 用户界面中的 Azure 数据工厂参数用法，请查看[使用参数的链接服务的数据工厂 UI](./parameterize-linked-services.md#ui-experience)和[使用参数的元数据驱动管道的数据工厂 UI](./how-to-use-trigger-parameterization.md#data-factory-ui)，以获取直观说明。

## <a name="parameter-and-expression-concepts"></a>参数和表达式概念 

可使用参数将外部值传递到管道、数据集、链接服务和数据流。 参数传递到资源后，就不能更改。 通过将资源参数化，每次就可通过不同的值重复使用它们。 参数可单独使用，也可作为表达式的一部分使用。 定义中的 JSON 值可以是文字，也可以是运行时计算的表达式。

例如：  
  
```json
"name": "value"
```

 或  
  
```json
"name": "@pipeline().parameters.password"
```

表达式可出现在 JSON 字符串值中的任何位置，始终生成另一个 JSON 值。 此处，password 是表达式中的管道参数。 如果某个 JSON 值为表达式，会通过删除 \@ 符号来提取表达式的正文。 如果需要以“\@”开头的文本字符串，则必须使用 \@\@ 将它转义。 以下示例演示了如何计算表达式。  
  
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

## <a name="examples-of-using-parameters-in-expressions"></a>在表达式中使用参数的示例 

### <a name="complex-expression-example"></a>复杂表达式示例
以下示例显示了一个复杂的示例，该示例引用了活动输出的一个深层子字段。 若要引用计算结果为子字段的管道参数，请使用 [] 语法而不是点 (.) 运算符（如 subfield1 和 subfield2 一样）

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

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

### <a name="a-dataset-with--parameters"></a>使用参数的数据集

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

### <a name="a-pipeline-with--parameters"></a>使用参数的管道

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

  
## <a name="calling-functions-within-expressions"></a>在表达式中调用函数 

可以在表达式中调用函数。 以下各节提供了有关可以在表达式中使用的函数的信息。  

### <a name="string-functions"></a>字符串函数  

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

### <a name="collection-functions"></a>集合函数

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

### <a name="logical-functions"></a>逻辑函数  

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
  
### <a name="conversion-functions"></a>转换函数  

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

### <a name="math-functions"></a>数学函数  
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
  
### <a name="date-functions"></a>日期函数  

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

## <a name="detailed-examples-for-practice"></a>练习的详细示例

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>使用参数的 Azure 数据工厂复制管道详情 

此 [Azure 数据工厂复制管道参数传递教程](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf)逐步讲解如何在管道和活动之间以及活动之间传递参数。

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>使用参数的映射数据流管道详情 

请遵循[使用参数的映射数据流](./parameters-data-flow.md)中的说明操作，了解有关如何在数据流中使用参数的全面示例。

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>使用参数的元数据驱动管道详情

请按照[使用参数的元数据驱动管道](./how-to-use-trigger-parameterization.md)中的说明操作，详细了解如何使用参数设计元数据驱动管道。 这是热门的参数用例。


## <a name="next-steps"></a>后续步骤
对于可以在表达式中使用的系统变量列表，请参阅[系统变量](control-flow-system-variables.md)。
