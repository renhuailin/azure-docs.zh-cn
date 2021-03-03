---
title: 分析数据流中的数据转换
description: 分析嵌入的列文档
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710186"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>映射数据流中的分析转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用 Parse 转换可以分析文档格式的数据中的列。 可以分析的嵌入文档的当前支持类型是 JSON 和分隔文本。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>配置

在 "分析转换配置" 面板中，您将首先选取要以内联方式分析的列中包含的数据类型。 分析转换还包含以下配置设置。

![分析设置](media/data-flow/data-flow-parse-1.png "分析")

### <a name="column"></a>列

与派生列和聚合类似，你可以通过从下拉选取器中进行选择来修改现有列。 或者，您可以在此处键入新列的名称。 ADF 将分析的源数据存储在此列中。

### <a name="expression"></a>表达式

使用表达式生成器为分析设置源。 这只是选择包含要分析的自包含数据的源列，也可以创建复杂的表达式进行分析。

### <a name="output-column-type"></a>输出列类型

你将在此处配置将写入单列的分析目标输出架构。

![分析示例](media/data-flow/data-flow-parse-2.png "分析示例")

在此示例中，我们定义了对传入字段 "jsonString" 的分析，该字段是纯文本，但格式为 JSON 结构。 我们将使用以下架构将分析结果存储为名为 "JSON" 的新列中的 JSON：

```(trade as boolean, customers as string[])```

请参阅 "检查" 选项卡和 "数据预览"，验证输出是否已正确映射。

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
                format: 'json',
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

* 使用 " [平展转换](data-flow-flatten.md) " 将行透视到列。
* 使用 [派生列转换](data-flow-derived-column.md) 将列透视到行。
