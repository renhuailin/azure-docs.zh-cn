---
title: 映射数据流中的逆透视转换
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解 Azure 数据工厂和 Synapse Analytics 中的映射数据流逆透视转换。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 28c89622702435f178e241125545f8f0ac86eb18
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059580"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>映射数据流中的逆透视转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

在映射数据流中使用“逆透视”作为将非标准化数据集转换为更标准化版本的方法，方法是将单个记录中多个列的值扩展为单个列中具有相同值的多个记录。

:::image type="content" source="media/data-flow/unpivot1.png" alt-text="屏幕截图显示从菜单中选择了“逆透视”。":::

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>分组依据

:::image type="content" source="media/data-flow/unpivot5.png" alt-text="屏幕截图显示了“逆透视设置”，其中选中了“分组依据”选项卡。":::

首先，为你的逆透视聚合设置要用作分组依据的列。 使用列列表旁边的 + 号设置一个或多个列用于取消组合。

## <a name="unpivot-key"></a>逆透视键

:::image type="content" source="media/data-flow/unpivot6.png" alt-text="屏幕截图显示了“逆透视设置”，其中选中了“逆透视键”选项卡。":::

逆透视键是服务从列透视到行的列。 默认情况下，此字段的数据集中的每个唯一值都将透视到行。 但是，你也可以选择输入此数据集中要透视到行值的值。

## <a name="unpivoted-columns"></a>已逆透视列

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="屏幕截图显示了“逆透视设置”，其中选中了“数据预览”选项卡。":::

最后，选择列名称，用于存储转换为行的逆透视列的值。

（可选）可以删除具有 NULL 值的行。

例如，SumCost 是上面共享的示例中所选的列名称。

:::image type="content" source="media/data-flow/unpivot3.png" alt-text="图像显示了逆透视转换前后的 PO、供应商和水果列，其中使用水果列作为逆透视键。":::

将列排列设置为“正常”时，会从单个值中将所有新的逆透视列组合在一起。 将列排列设置为“横向”时，会将从现有列中生成的新逆透视列组合在一起。

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="屏幕截图显示转换结果。":::

最终的已逆透视数据结果集显示已逆透视到单独行值的列总值。

## <a name="next-steps"></a>后续步骤

使用[透视转换](data-flow-pivot.md)将行转换为列。
