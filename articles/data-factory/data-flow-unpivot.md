---
title: 映射数据流中的逆透视转换
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure 数据工厂映射数据流逆透视转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 07/14/2020
ms.openlocfilehash: 5313af13d02df1ab33764bf268edd601005cb8af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638352"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>映射数据流中的逆透视转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 ADF 映射数据流中使用“逆透视”作为将非标准化数据集转换为更标准化版本的方法，通过将单个记录中多个列的值扩展为单个列中具有相同值的多个记录。

![屏幕截图显示从菜单中选择了“逆透视”。](media/data-flow/unpivot1.png "逆透视选项 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>分组依据

![屏幕截图显示了“逆透视设置”，其中选中了“分组依据”选项卡。](media/data-flow/unpivot5.png "逆透视选项 2")

首先，为你的逆透视聚合设置要用作分组依据的列。 使用列列表旁边的 + 号设置一个或多个列用于取消组合。

## <a name="unpivot-key"></a>逆透视键

![屏幕截图显示了“逆透视设置”，其中选中了“逆透视键”选项卡。](media/data-flow/unpivot6.png "逆透视选项 3")

逆透视键是 ADF 从列透视到行的列。 默认情况下，此字段的数据集中的每个唯一值都将透视到行。 但是，你也可以选择输入此数据集中要透视到行值的值。

## <a name="unpivoted-columns"></a>已逆透视列

![屏幕截图显示了“逆透视设置”，其中选中了“数据预览”选项卡。](media/data-flow//unpivot7.png "逆透视选项 4")

最后，选择列名称，用于存储转换为行的逆透视列的值。

（可选）可以删除具有 NULL 值的行。

例如，SumCost 是上面共享的示例中所选的列名称。

![图像显示了逆透视转换前后的 PO、供应商和水果列，其中使用水果列作为逆透视键。](media/data-flow/unpivot3.png)

将列排列设置为“正常”时，会从单个值中将所有新的逆透视列组合在一起。 将列排列设置为“横向”时，会将从现有列中生成的新逆透视列组合在一起。

![屏幕截图显示转换结果。](media/data-flow//unpivot7.png "逆透视选项 5")

最终的已逆透视数据结果集显示已逆透视到单独行值的列总值。

## <a name="next-steps"></a>后续步骤

使用[透视转换](data-flow-pivot.md)将行转换为列。
