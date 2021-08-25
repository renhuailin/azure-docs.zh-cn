---
title: 映射数据流中的窗口转换
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure 数据工厂映射数据流窗口转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 11/16/2020
ms.openlocfilehash: 8331d3908e484f1b82a4aa73cd6d498dac2a0ad3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638350"
---
# <a name="window-transformation-in-mapping-data-flow"></a>映射数据流中的窗口转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在窗口转换中，将定义数据流中基于窗口的列的聚合。 在表达式生成器中，可以定义基于数据或时间窗口（SQL OVER 子句）的不同类型的聚合，如 LEAD、LAG、NTILE、CUMEDIST、RANK 等。 输出中将生成包含这些聚合的新字段。 还可以包含可选的分组字段。

![屏幕截图显示从菜单选择了“窗口化”。](media/data-flow/windows1.png "windows 1")

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Over
为窗口转换设置列数据的分区。 SQL 等效项是 SQL 中 Over 子句中的 ```Partition By```。 如果希望创建用于分区的计算或表达式，可以将鼠标悬停在列名上并选择“计算列”。

![屏幕截图显示了“窗口设置”，其中已选中“Over”选项卡。](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>排序
Over 子句的另一部分是设置 ```Order By```。 该操作将设置数据排序顺序。 还可以在此列字段中为计算值创建表达式以进行排序。

![屏幕截图显示了“窗口设置”，其中已选中“Sort”选项卡。](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>范围
接下来，将窗口框架设置为“无界”或“有界”。 若要设置无界窗框，请将滑块两端设置为“无界”。 如果在无界和当前行之间选择一个设置，那么必须设置偏移量的开始值和结束值。 这两个值都是正整数。 可以使用数据中的相对数字或值。

窗口滑块有两个要设置的值：当前行之前的值和当前行之后的值。 “开始”和“结束”偏移与滑块上的两个选择器相匹配。

![屏幕截图显示了“窗口设置”，其中已选中“Range by”选项卡。](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>窗口列
最后，使用表达式生成器定义你希望与数据窗口一起使用的聚合，例如 RANK、COUNT、MIN、MAX、DENSE RANK、LEAD 和 LAG 等。

![屏幕截图显示了窗口操作的结果。](media/data-flow/windows7.png "windows 7")

此处列出了可通过表达式生成器采用 ADF 数据流表达式语言使用的聚合和分析函数的完整列表：https://aka.ms/dataflowexpressions。

## <a name="next-steps"></a>后续步骤

如果正在查找简单的分组依据聚合，请使用[聚合转换](data-flow-aggregate.md)
