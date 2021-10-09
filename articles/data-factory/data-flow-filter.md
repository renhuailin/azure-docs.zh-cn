---
title: 映射数据流中的筛选转换
titleSuffix: Azure Data Factory & Azure Synapse
description: 在 Azure 数据工厂或 Synapse Analytics 中使用映射数据流中的筛选转换筛选出行。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3cf00a5ea80278973a548a19e9ef0dc82ef877f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060188"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>映射数据流中的筛选转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

筛选转换允许根据条件对行进行筛选。 输出流包括与筛选条件匹配的所有行。 筛选转换类似于 SQL 中的 WHERE 子句。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Configuration

使用数据流表达式生成器为筛选条件输入表达式。 若要打开表达式生成器，请单击蓝色框。 筛选条件必须为布尔型。 有关如何创建表达式的详细信息，请参阅[表达式生成器](concepts-data-flow-expression-builder.md)文档。

:::image type="content" source="media/data-flow/filter1.png" alt-text="筛选转换":::

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>示例

下面的示例是一个名为 `FilterBefore1960` 的筛选转换，它接收传入流 `CleanData`。 筛选条件是 `year <= 1960` 表达式。

在 UI 中，此转换如下图所示：

:::image type="content" source="media/data-flow/filter1.png" alt-text="筛选转换":::

此转换的数据流脚本位于下面的代码片段中：

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>后续步骤

使用[选择转换](data-flow-select.md)筛选掉列
