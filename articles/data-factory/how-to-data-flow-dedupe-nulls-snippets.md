---
title: 使用数据流代码段重复数据重复行并查找 null 值
description: 了解如何通过使用数据流中的代码片段轻松地重复数据的行和查找 null
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: cdb522cc4be83eadd2c60c91c7fee33e7ccc039b
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632441"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>使用数据流代码段重复数据重复行并查找 null 值

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过使用映射数据流中的代码片段，您可以轻松执行一些常见任务，例如重复数据删除和 null 筛选。 本文介绍如何使用数据流脚本代码段轻松地将这些函数添加到管道。
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>创建管道

1. 选择 " **新建管道** "。

1. 添加数据流活动。

1. 选择 " **源设置** " 选项卡，添加源转换，然后将其连接到某个数据集。

    ![用于添加源类型的 "源设置" 窗格的屏幕截图。](media/data-flow/snippet-adf-2.png)

    重复数据的和 null 检查代码段使用利用数据流架构偏移的一般模式。 代码段适用于来自数据集的任何架构，或与没有预定义架构的数据集配合使用。

1. 在 [ (DFS) 的数据流脚本 ](./data-flow-script.md#distinct-row-using-all-columns)的 "不同行使用所有列" 部分中，复制 DistinctRows 的代码片段。

1. [中转到 "数据流脚本" 文档页，复制不同行的代码片段。](./data-flow-script.md#distinct-row-using-all-columns)

    ![源代码段的屏幕截图。](media/data-flow/snippet-adf-3.png)

1. 在脚本中，在的定义后 `source1` ，按 Enter，然后粘贴代码段。

1. 执行以下操作之一：

   * 通过在粘贴的代码前面键入 **source1** ，将粘贴的代码段连接到您在关系图中前面创建的源转换。

   * 或者，您可以通过从关系图中的 "新转换" 节点中选择传入流，在设计器中连接新的转换。

     !["有条件拆分设置" 窗格的屏幕截图。](media/data-flow/snippet-adf-4.png)

   现在，数据流将使用聚合转换从源删除源中的重复行，该转换通过对所有列值使用一般哈希对所有行进行分组。
    
1. 添加用于将数据拆分为一个流的代码段，其中包含具有 null 值的行和没有 null 值的另一个流。 为此，请执行以下操作：

1. [返回到代码片段库，此时复制 NULL 检查的代码。](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. 在数据流设计器中，再次选择 " **脚本** "，然后在底部粘贴此新的转换代码。 此操作通过在粘贴的代码段前面放置该转换的名称，将该脚本连接到之前的转换。

   数据流图表现在应如下所示：

    ![数据流关系图的屏幕截图。](media/data-flow/snippet-adf-1.png)

  现在，你已创建了一个包含泛型 deduping 和 null 检查的工作数据流，方法是从数据流脚本库中提取现有代码段并将其添加到现有设计中。

## <a name="next-steps"></a>后续步骤

* 使用映射数据流 [转换](concepts-data-flow-overview.md)生成数据流逻辑的其余部分。