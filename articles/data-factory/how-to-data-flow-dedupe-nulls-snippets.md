---
title: 使用数据流代码片段删除重复行和查找 null
description: 了解如何通过使用数据流中的代码片段轻松删除重复行和查找 null
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 116b1e2afded3ceee0d249e326e721ca420dcf17
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638570"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>使用数据流代码片段删除重复行和查找 null

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过使用映射数据流中的代码片段，可以轻松执行重复数据删除和 null 筛选等常见任务。 本文介绍如何使用数据流脚本代码片段轻松地将这些功能添加到管道。
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>创建管道

1. 选择“新建管道”。

1. 添加数据流活动。

1. 选择“源设置”选项卡，添加源转换，然后将其连接到某个数据集。

    ![用于添加源类型的“源设置”窗格的屏幕截图。](media/data-flow/snippet-adf-2.png)

    重复数据删除和 null 检查代码片段使用利用数据流架构偏差的一般模式。 代码片段适用于数据集中的任何架构，或没有预定义架构的数据集。

1. 在[数据流脚本 (DFS)](./data-flow-script.md#distinct-row-using-all-columns) 的“使用所有列的不同行”部分中，复制 DistinctRows 的代码片段。

1. [转到“数据流脚本”文档页，复制不同行的代码片段。](./data-flow-script.md#distinct-row-using-all-columns)

    ![源代码片段的屏幕截图。](media/data-flow/snippet-adf-3.png)

1. 在脚本中，在 `source1` 的定义后，按 Enter，然后粘贴代码片段。

1. 执行以下操作之一：

   * 通过在粘贴的代码前面键入 source1，将此粘贴的代码片段连接到你之前在图中创建的源转换。

   * 或者，可以通过从图中的新转换节点选择传入流，在设计器中连接新转换。

     ![“有条件拆分设置”窗格的屏幕截图。](media/data-flow/snippet-adf-4.png)

   现在，数据流将使用聚合转换从源删除重复行，聚合转换通过对所有列值使用通用哈希来按所有行分组。
    
1. 添加一个代码片段，用于将数据拆分为一个包含带 null 的行的流，以及另一个不含 null 的流。 为此，请执行以下操作：

1. [返回到代码片段库，这一次复制 NULL 检查的代码。](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. 在数据流设计器中，再次选择“脚本”，然后在底部粘贴此新转换代码。 此操作通过将该转换的名称置于粘贴的代码片段前面，将脚本连接到之前的转换。

   数据流图现在应如下所示：

    ![数据流图的屏幕截图。](media/data-flow/snippet-adf-1.png)

  现在，你已通过从数据流脚本库中获取现有代码片段并将其添加到现有设计中，创建了一个包含通用重复数据删除和 null 检查的工作数据流。

## <a name="next-steps"></a>后续步骤

* 使用映射数据流[转换](concepts-data-flow-overview.md)来生成数据流逻辑的其余部分。