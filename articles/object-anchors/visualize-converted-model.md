---
title: 如何可视化 Object Anchors 模型
description: 介绍如何可视化转换后的模型。
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 05/28/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: a9140004d9f4d79cabb9890851ba5c5cb7b815f2
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987799"
---
# <a name="how-to-visualize-an-object-anchors-model"></a>如何可视化 Object Anchors 模型

转换后的模型不需要可视化也能使用。 不过，如果需要进行可视化，可采用一种简单的方法来查看其网格。

遵循我们的 [Unity 应用快速入门](quickstarts/get-started-unity-hololens.md)中的步骤进行操作，但有一处细微改动。 在生成场景时，请打开“VisualizeScene”并将其添加到场景生成列中，而不是打开“AOASampleScene”。  然后在“Build Settings”中，确保仅选中了“VisualizeScene”：不应包含任何其他场景。

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-build-settings-visualize.png" alt-text="生成设置可视化":::

关闭“Build Settings”对话框，而不是选择“Build”按钮。

在“Hierarchy”面板中，选择“Visualizer”游戏对象。 

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-hierarchy.png" alt-text="层次结构":::

在“Inspector”面板中，找到“Mesh Loader (Script)”部分下的“Model path”属性，然后键入 Object Anchors 模型文件的路径，包括文件扩展名。  

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-inspector.png" alt-text="检查器":::

选择 Unity Editor 顶部的 Play 按钮，然后确保选择了 Scene 视图。 

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-editor.png" alt-text="运行和场景视图":::

使用 [Unity 的场景视图导航控件](https://docs.unity3d.com/Manual/SceneViewNavigation.html)，现在可以检查 Object Anchors 模型。

:::image type="content" source="./media/object-anchors-visualize.png" alt-text="可视化模型":::
