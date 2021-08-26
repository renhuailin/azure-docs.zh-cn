---
title: 分析映射数据流中的数据转换
description: 分析嵌入的列文档
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 05/10/2021
ms.openlocfilehash: 2058fcade3375f680f616374ba8dee73a4e72dbc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637906"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>分析映射数据流中的转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用“分析”转换分析数据中文档形式的列。 可以分析的嵌入文档的当前支持类型是 JSON、XML 和带分隔符的文本。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>配置

在分析转换配置面板中，首先选择要以内联方式分析的列中包含的数据类型。 分析转换还包含以下配置设置。

![分析设置](media/data-flow/data-flow-parse-1.png "分析")

### <a name="column"></a>列

与派生列和聚合类似，你可以在此处通过从下拉选取器中选择现有列来对其进行修改。 或者，也可以在此处键入新列的名称。 ADF 将已分析的源数据存储在此列中。 在大多数情况下，需定义一个新列来解析传入的嵌入文档字段。

### <a name="expression"></a>表达式

使用表达式生成器设置分析的源。 可以只需简单地选择包含要分析的自包含数据的源列，也可以创建复杂的表达式进行分析。

#### <a name="example-expressions"></a>表达式示例

* 源字符串数据：```chrome|steel|plastic```
  * 表达式：```(desc1 as string, desc2 as string, desc3 as string)```

* 源 JSON 数据：```{"ts":1409318650332,"userId":"309","sessionId":1879,"page":"NextSong","auth":"Logged In","method":"PUT","status":200,"level":"free","itemInSession":2,"registration":1384448}```
  * 表达式：```(level as string, registration as long)```

* 源 XML 数据：```<Customers><Customer>122</Customer><CompanyName>Great Lakes Food Market</CompanyName></Customers>```
  * 表达式：```(Customers as (Customer as integer, CompanyName as string))```

### <a name="output-column-type"></a>输出列类型

你将在此处根据将写入单个列的分析配置目标输出架构。

![分析示例](media/data-flow/data-flow-parse-2.png "分析示例")

在此示例中，我们定义了对传入字段“jsonString”的分析，该字段是纯文本，但格式为 JSON 结构。 我们将使用此架构将分析结果以 JSON 格式存储在一个名为“json”的新列中：

```(trade as boolean, customers as string[])```

请参阅检查选项卡和数据预览，验证是否正确映射输出。

## <a name="examples"></a>示例

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

### <a name="examples"></a>示例

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json|XML|delimited',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>后续步骤

* 使用[平展转换](data-flow-flatten.md)将行转换为列。
* 使用[派生列转换](data-flow-derived-column.md)将列转换为行。
