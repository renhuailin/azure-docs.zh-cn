---
title: 映射数据流中的 Union 转换
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure 数据工厂映射数据流新建分支转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 04/27/2020
ms.openlocfilehash: 2a81ffa5d73509a5bb47cda8bc2933894b3139f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638073"
---
# <a name="union-transformation-in-mapping-data-flow"></a>映射数据流中的 Union 转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Union 会将多个数据流组合成一个数据流，而这些流的 SQL Union 会作为 Union 转换的新输出。 每个输入流中的所有架构都会合并到数据流中，不需要有联接键。

通过选择每个已配置的行旁边的“+”图标，你可以在设置表中对 n 个流进行组合，包括源数据以及数据流中现有转换的流。

下面是有关 ADF 映射数据流中联合转换的简短视频演练：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![联合转换](media/data-flow/union.png "Union")

在这种情况下，可以将来自多个源（在本例中为三个不同的源文件）的不同元数据组合在一起，将它们组合成单个流：

![Union 转换概述](media/data-flow/union111.png "Union 1")

若要实现此目的，请在“Union 设置”中通过包括要添加的所有源来添加更多行。 无需使用常见的查找或联接键：

![Union 转换设置](media/data-flow/unionsettings.png "Union 设置")

如果在 Union 后设置了 Select 转换，则可以重命名重叠字段或未从无标题源命名的字段。 单击“检查”以查看本例中来自三个不同源的总共包含 132 个列的组合元数据：

![Union 转换最终结果](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>名称和位置

选择“按名称联合”时，每个列值都将从每个源放入相应的列中，并采用新的串联后的元数据架构。

如果选择“按位置联合”，则每个列值都将从每个相应的源放入原始位置，从而生成一个新的组合数据流，其中，来自每个源的数据将添加到同一个流中：

![Union 输出](media/data-flow/unionoutput.png "Union 输出")

## <a name="next-steps"></a>后续步骤

探究类似的转换，包括 [Join](data-flow-join.md) 和 [Exists](data-flow-exists.md)。
