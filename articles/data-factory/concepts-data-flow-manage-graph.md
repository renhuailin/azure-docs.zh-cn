---
title: 管理映射数据流图
titleSuffix: Azure Data Factory & Azure Synapse
description: 如何有效地管理和编辑映射数据流图
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: da9b3148506e437f7bf5d90fc0fbe44e0a2d27f0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812109"
---
# <a name="managing-the-mapping-data-flow-graph"></a>管理映射数据流图

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

映射数据流是使用称为数据流图的设计图面创作的。 在该图中，转换逻辑是从左到右构建的，而其他数据流则是自上而下添加的。 若要添加新的转换，请选择现有转换右下方的加号。

:::image type="content" source="media/data-flow/canvas-2.png" alt-text="画布":::

随着数据流变得越来越复杂，请使用以下机制来有效地导航和管理数据流图。 

## <a name="moving-transformations"></a>移动转换

在映射数据流时，一组连接的转换逻辑称为流。 传入流字段指示正在馈送当前转换的数据流。 每个转换都有一个或两个传入流（具体视其函数而定），并表示一个输出流。 传入流的输出架构确定当前转换可引用的列元数据。

:::image type="content" source="media/data-flow/move-nodes.png" alt-text="移动节点":::

与管道画布不同，数据流转换不使用拖放模型进行编辑。 要更改转换的传入流或“移动”转换，请从“传入流”下拉列表中选择其他值。 执行此操作时，所有下游转换将随编辑过的转换一起移动。 该图将自动更新以显示新的逻辑流。 如果将传入流更改为已具有下游转换的转换，则将创建新的分支或并行数据流。 详细了解[映射数据流中的新分支](data-flow-new-branch.md)。

## <a name="hide-graph-and-show-graph"></a>隐藏图形和显示图形

编辑转换时，可以展开配置面板以占据整个画布，从而隐藏图形。 单击位于画布右侧的朝上 V 形。

:::image type="content" source="media/data-flow/hide-graph.png" alt-text="隐藏图形":::

隐藏图形后，可以通过单击“下一个”或“上一个”在流中的转换之间移动 。 单击朝下的 V 形以显示图形。

:::image type="content" source="media/data-flow/show-graph.png" alt-text="显示图形":::

## <a name="searching-for-transformations"></a>搜索转换

若要在图形中快速查找转换，请单击缩放设置上方的“搜索”图标。

:::image type="content" source="media/data-flow/search-1.png" alt-text="屏幕截图显示“搜索”按钮。":::

你可以按转换名称或说明进行搜索以查找转换。

:::image type="content" source="media/data-flow/search-2.png" alt-text="屏幕截图显示搜索文本框。":::

## <a name="hide-reference-nodes"></a>隐藏引用节点

如果数据流具有任何联接、查找、存在或联合转换，则数据流会显示所有传入流的引用节点。 如果希望最大程度减少所占用的垂直空间，则可以尽量减少引用节点。 为此，请右键单击画布，然后选择“隐藏引用节点”。

:::image type="content" source="media/data-flow/hide-reference-nodes.png" alt-text="隐藏引用节点":::

## <a name="next-steps"></a>后续步骤

完成数据流逻辑后，打开[调试模式](concepts-data-flow-debug-mode.md)并在数据预览中对其进行测试。
