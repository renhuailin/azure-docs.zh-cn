---
title: 映射数据流中的逆透视转换
description: Azure 数据工厂映射数据流逆透视转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ef861cdf394716a70d85e43ce9c60f46af2cc2e4
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040198"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>映射数据流中的逆透视转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过将单个记录中多个列的值扩展为单个列中具有相同值的多个记录，可以在 ADF 映射数据流中使用逆透视。

![屏幕截图显示从菜单中选择的逆透视。](media/data-flow/unpivot1.png "Unpivot 选项1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>分组依据

![屏幕截图显示已选中 "取消分组" 选项卡的 Unpivot 设置。](media/data-flow/unpivot5.png "Unpivot 选项2")

首先，设置要对其进行逆透视聚合运算的列。 使用列列表旁边的 + 号设置一个或多个列用于取消组合。

## <a name="unpivot-key"></a>逆透视键

![屏幕截图显示已选中 "逆透视键" 选项卡的 Unpivot 设置。](media/data-flow/unpivot6.png "Unpivot 选项3")

Unpivot 键是 ADF 将从列透视到行的列。 默认情况下，此字段的数据集中的每个唯一值都将与行透视。 不过，您可以根据需要输入数据集中要透视到行值的值。

## <a name="unpivoted-columns"></a>已逆透视列

![屏幕截图显示已选中 "数据预览" 选项卡的 Unpivot 设置。](media/data-flow//unpivot7.png "Unpivot 选项4")

最后，选择用于存储转换为行的逆透视列的值的列名称。

 (可选) 可以删除具有 Null 值的行。

例如，SumCost 是上面共享的示例中所选的列名称。

![图像：使用水果列作为 unipivot 键，在 unipivot 转换前后显示 PO、供应商和水果列。](media/data-flow/unpivot3.png)

将列排列设置为 "Normal" 会将所有新的逆透视列从单个值组合在一起。 将 "列排列" 设置为 "横向" 会将从现有列生成的新的逆透视列组合在一起。

![屏幕截图显示转换的结果。](media/data-flow//unpivot7.png "Unpivot 选项5")

最终的已逆透视数据结果集显示已逆透视到单独行值的列总值。

## <a name="next-steps"></a>后续步骤

使用 [透视转换](data-flow-pivot.md) 将行透视到列。
