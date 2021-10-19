---
title: 字符串化映射数据流中的数据转换
description: 字符串化复杂数据类型
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: e2d0a5993dddccc65109eb623d8f04f11715ac47
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660032"
---
# <a name="stringify-transformation-in-mapping-data-flow"></a>字符串化映射数据流中的转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用字符串化转换将复杂的数据类型转换为字符串。 需要将列数据作为可能源自结构、映射或数组类型的单个字符串实体来存储或发送时，这可能非常有用。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMTs9]

## <a name="configuration"></a>配置

在字符串化转换配置面板中，首先选择要以内联方式分析的列中包含的数据类型。 分析转换还包含以下配置设置。

:::image type="content" source="media/data-flow/stringify.png" alt-text="字符串化设置":::

### <a name="column"></a>列

与派生列和聚合类似，你可以在此处通过从下拉选取器中选择现有列来对其进行修改。 或者，也可以在此处键入新列的名称。 ADF 将已字符串化的源数据存储在此列中。 在大多数情况下，需要定义一个新列来对传入的复杂字段类型进行字符串化。

### <a name="expression"></a>Expression

使用表达式构建器设置要字符串化的源复杂字段。 可以只需简单地选择包含要字符串化的自包含数据的源列，也可以创建复杂的表达式进行分析。

:::image type="content" source="media/data-flow/stringify-2.png" alt-text="字符串化表达式":::

#### <a name="example-expression"></a>示例表达式

在此示例中，```body.properties.periods``` 是从 REST 源返回的结构中的数组。

```
body.properties.periods
```

## <a name="data-flow-script"></a>数据流脚本

```
stringify(mydata = body.properties.periods ? string,
    format: 'json') ~> Stringify1
```

## <a name="next-steps"></a>后续步骤

* 使用[平展转换](data-flow-flatten.md)将行转换为列。
* 使用[分析转换](data-flow-parse.md)将复杂的嵌入类型转换为单独的列。
* 使用[派生列转换](data-flow-derived-column.md)将列转换为行。
