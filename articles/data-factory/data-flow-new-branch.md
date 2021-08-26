---
title: 映射数据流中的多个分支
titleSuffix: Azure Data Factory & Azure Synapse
description: 复制包含多个分支的映射数据流中的数据流
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 04/16/2021
ms.openlocfilehash: e84b1d1a2130c2d1c621ade1b21f92c485709100
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638962"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>在映射数据流中创建新分支

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

添加新分支，对同一数据流执行多组操作和转换。 如果要为多个接收器使用相同的源，或将自联接数据一起使用，则添加新分支会很有用。

与其他转换类似，可以从转换列表中添加新的分支。 仅在你尝试分支的转换之后存在现有转换时，“新建分支”才可作为操作使用。

![屏幕截图显示“多个输入/输出”菜单中的“新建分支”选项。](media/data-flow/new-branch2.png "添加新分支")

在下面的示例中，数据流正在读取出租车行程数据。 需要按日期和供应商汇总的输出。 可以添加一个新的分支，而不是创建从同一源读取的两个单独的数据流。 这样，两种聚合都可以作为同一数据流的一部分执行。 

![屏幕截图显示来自源的两个分支的数据流。](media/data-flow/new-branch.png "添加新分支")

> [!NOTE]
> 单击加号 (+) 将转换添加到图形时，如果存在后续转换块，将只会看到“新建分支”选项。 这是因为“新建分支”会创建对现有流的引用，并需要进一步的上游处理才能继续操作。 如果看不到“新建分支”选项，请首先添加派生列或其他转换，然后返回到上一个块，就会看到“新建分支”选项。

## <a name="next-steps"></a>后续步骤

建立分支后，建议使用[数据流转换](data-flow-transformation-overview.md)
