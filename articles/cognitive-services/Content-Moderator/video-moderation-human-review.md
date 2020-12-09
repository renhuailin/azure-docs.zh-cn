---
title: 使用评审工具来审查视频 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 使用计算机辅助视频审核和审阅工具来审核是否有不当内容
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 05ece4d8ea24fb9da106ec3daa2cc1a63706dab9
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853492"
---
# <a name="video-moderation-with-the-review-tool"></a>使用评审工具来审查视频

使用内容审查器的计算机辅助[视频审核](video-moderation-api.md)和[审阅工具](Review-Tool-User-Guide/human-in-the-loop.md)，审核视频和脚本是否有成人（明示）和挑逗性（暗示）内容，以获得最佳业务效果。

## <a name="view-videos-under-review"></a>观看正在评审的视频

在仪表板上，选择视频内容类型中的任意评审队列。 这将启动评审并打开视频内容审查页面。

> [!div class="mx-imgBorder"]
> ![在内容审查器 (Preview) 上，滑块将突出显示并设置为4个评论。 "全部模糊" 和 "黑白" 和 "白色" 开关均已设置。](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Review count

使用右上角的滑块设置要在页面上显示的评审数量。

### <a name="view-type"></a>视图类型

你可以采用磁贴或详细视图形式查看不同的内容项。 “详细信息”视图可用来查看关键帧以及有关选定视频的其他信息。 

> [!NOTE]
> 视频审查服务仅识别和输出可能完整（良好）的帧，而不是定期输出帧。 可以借助此功能高效地生成帧，以便对帧级成人内容和不雅内容进行分析。

“平铺”视图会将每个视频显示为单个磁贴。 选择视频帧上方的展开按钮可以放大该视频并隐藏其他视频。

### <a name="content-obscuring-effects"></a>内容遮掩效果

使用“全部模糊”和“黑白”开关来设置这些内容遮掩效果。 默认情况下，它们处于启用状态。 在“平铺”视图中，你可以单独切换每个视频的效果。

## <a name="check-video-details"></a>检查视频详细信息

在“详细信息”视图中，右窗格会显示多个选项卡，其中显示有关视频的详细信息。

* 选择“注释”选项卡，向视频添加自定义注释。
* 选择“脚本”选项卡以查看视频脚本&mdash;服务会自动提取视频中任何语音的脚本。 当你选择某个部分的文本时，视频播放器会跳转到视频的该部分。
* 选择“元数据”选项卡可查看视频文件元数据。
* 选择“历史记录”选项卡可查看评审历史记录，例如创建时间和修改情况。

> [!div class="mx-imgBorder"]
> ![将突出显示右窗格，并选择 "注释" 选项卡。 有一个标记为 "添加注释" 的测试区域。](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>应用审查标记

视频评审的主要任务是在视频中或部分视频中应用或删除审查标记。

### <a name="bulk-tagging"></a>批量标记

使用“批量标记”工具栏，可以一次向多个选定的视频添加标记。 选择一个或多个视频，然后选择要应用的标记，并单击“提交”。 

> [!div class="mx-imgBorder"]
> !["+" 按钮在 "批量标记" 窗格中突出显示。](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>关键帧标记

你还可以向特定的关键帧添加审查标记。 从关键帧磁贴窗格中选择帧，然后选择“关键帧标记 +”以应用所需的标记。

> [!NOTE]
> 如果服务无法提取关键帧，则关键帧磁贴窗格会显示“没有可用的帧”，并且选择关键帧的选项会灰显。在这种情况下，你只能将标记应用于整个视频（使用“视频标记 +”按钮）。

> [!div class="mx-imgBorder"]
> ![磁贴窗格、视频播放器、关键帧标记窗格和视频标记窗格都显示出来。 将突出显示 "关键帧标记 +" 和 "视频标记 +" 按钮。](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>暂停评审

通过视频窗格底部的“暂停”按钮，你可以暂停评审，以便以后检索并完成它。 对于需要咨询当前没有时间的其他团队成员或经理的评审，你可以执行此操作。 

你可以通过单击屏幕顶部的“暂停”按钮来观看暂停的视频。 “暂停”窗格显示在右侧。 从这里，你可以选择多个暂停的评审，并将其释放回队列，或设置其过期时间。 在经过预先配置的时长后，暂停的评审会释放回队列。 选择“保存”将从当前选定的过期时间开始倒计时。

> [!div class="mx-imgBorder"]
> ![在 "视频" 窗格上，突出显示 "保留" 按钮。 在窗格底部，将突出显示 "保留时间" 组合框以及 "发布" 和 "保存" 按钮。](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>提交评审

应用标记后，选择视频窗格底部的“提交”按钮。 如果你标记了多个视频，则可以在单个评审中提交它们，也可以在各个独立的评审中提交它们。

## <a name="limbo-state"></a>Limbo 状态

提交评审后，视频会转为“Limbo”状态，可以通过选择屏幕顶部的“Limbo”按钮来查看此状态。 视频在预先配置的一段时间（可以在底部菜单中进行更改）内保持 Limbo 状态，直到再次评审或手动提交它们。

视频过期（脱离 Limbo 状态）后，其评审会被标记为完成。

## <a name="next-steps"></a>后续步骤

- 开始学习[视频审查快速入门](video-moderation-api.md)。
- 了解如何根据审查输出为人工审阅者生成[视频审阅](video-reviews-quickstart-dotnet.md)。
- 向视频审阅添加[视频脚本审阅](video-transcript-reviews-quickstart-dotnet.md)。
