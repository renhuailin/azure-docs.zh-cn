---
title: 映射数据流中的排序转换
description: 了解 Azure 数据工厂和 Synapse Analytics 管道中的映射数据排序转换。
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 8781836be58403dfef130f55fa698b281360e641
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059871"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>映射数据流中的排序转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

借助排序转换，可对当前数据流上的传入行进行排序。 可以选择各个列，然后按升序或降序对其进行排序。

> [!NOTE]
> 映射数据流在 Spark 群集上执行，这些数据流跨多个节点和分区分布。 如果选择在后续转换中对数据进行重新分区，则可能会因为数据重排而丢失排序。 若要在数据流中维护排序顺序，最佳方法是在转换的“优化”选项卡中设置单个分区，并使排序转换尽可能接近接收器。

## <a name="configuration"></a>Configuration

:::image type="content" source="media/data-flow/sort.png" alt-text="排序设置":::

**不区分大小写：** 对字符串或文本字段排序时是否要忽略大小写

**仅在分区中排序：** 数据流在 Spark 上运行时，所有数据流都将划分为多个分区。 此设置仅对传入分区中的数据排序，不对整个数据流排序。 

**排序条件：** 选择要排序的列以及排序顺序。 顺序决定排序优先级。 选择数据流的开头或结尾是否显示 null。

### <a name="computed-columns"></a>计算列

若要在排序前修改或提取列值，请将鼠标悬停在列上并选择“计算列”。 这将打开表达式生成器来为排序操作创建表达式，而不是使用列值。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>示例

:::image type="content" source="media/data-flow/sort.png" alt-text="排序设置":::

以上排序配置的数据流脚本位于下面的代码片段中。

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>后续步骤

排序后，建议使用[聚合转换](data-flow-aggregate.md)
