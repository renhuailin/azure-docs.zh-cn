---
title: 映射数据流中的有条件拆分转换
titleSuffix: Azure Data Factory & Azure Synapse
description: 在 Azure 数据工厂或 Synapse Analytics 中使用映射数据流中的有条件拆分转换将数据拆分为不同的流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 15864e8dfb694478f8156d5122608dec88493285
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060283"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>映射数据流中的有条件拆分转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

有条件拆分转换根据匹配条件将数据行路由到不同的流。 有条件拆分转换类似于编程语言中的 CASE 决策结构。 转换计算表达式，并根据结果将数据行定向到指定的流。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>配置

“拆分依据”设置可确定数据行是流向第一个匹配流还是其匹配的每个流。

使用数据流表达式生成器为拆分条件输入表达式。 若要添加新条件，请单击现有行中的加号图标。 还可以为与任何条件都不匹配的行添加默认流。

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="有条件拆分":::

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>示例

下面的示例是一个名为 `SplitByYear` 的有条件拆分转换，它采用传入流 `CleanData`。 此转换有两个拆分条件，即 `year < 1960` 和 `year > 1980`。 `disjoint` 为 false，因为数据转到了第一个匹配条件。 每个匹配第一个条件的行都会转到输出流 `moviesBefore1960`。 与第二个条件匹配的所有剩余行都会转到输出流 `moviesAFter1980`。 所有其他行都转到默认流 `AllOtherMovies`。

在服务器 UI 中，此转换如下图所示：

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="有条件拆分":::

此转换的数据流脚本位于下面的代码片段中：

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>后续步骤

与条件拆分一起使用的常见数据流转换有[联接转换](data-flow-join.md)、[查找转换](data-flow-lookup.md)和[选择转换](data-flow-select.md)
