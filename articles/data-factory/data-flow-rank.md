---
title: 映射数据流中的排名转换
description: 了解如何使用映射数据流排名转换在 Azure 数据工厂或 Synapse Analytics 管道中生成排名列。
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3af0355a02badb5aaa5d5b13a51b74392c931d8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059865"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>映射数据流中的排名转换 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

根据用户指定的排序条件，使用排名转换生成已排序的排名。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Configuration

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="排名设置":::

不区分大小写：如果排序列的类型为 string，则大小写会影响排名结果。 

密集：如果启用，则排名列将会是密集排名的排名列。 每个排名计数都将是连续数字，并且在出现不相上下的结果后不会跳过排名值。

排名列：生成的排名列的名称。 此列的类型为 long。

排序条件：选择要作为排序依据的列以及排序顺序。 该顺序决定排序优先级。

上述配置将使用传入的篮球数据，并创建一个名为“pointsRanking”的排名列。 PTS 列值最高的行的 pointsRanking 值将为 1。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>示例

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="排名设置":::

以上排名配置的数据流脚本在下面的代码片段中。

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>后续步骤

使用[筛选器转换](data-flow-filter.md)根据排名值筛选行。
