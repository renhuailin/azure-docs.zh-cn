---
title: 视频审核和审阅工具-内容审查器
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
ms.openlocfilehash: 079d5ea0259a436100b81489043b71a81ec1a330
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327213"
---
# <a name="video-moderation-with-the-review-tool"></a>带有评审工具的视频审核

使用内容审查器的计算机辅助[视频审核](video-moderation-api.md)和[审阅工具](Review-Tool-User-Guide/human-in-the-loop.md)，审核视频和脚本是否有成人（明示）和挑逗性（暗示）内容，以获得最佳业务效果。

## <a name="view-videos-under-review"></a>查看正在查看的视频

在 "仪表板" 上，选择 "视频内容类型" 中的任意查看队列。 这会启动审阅并打开视频内容裁决页面。

> [!div class="mx-imgBorder"]
> ![在内容审查器 (Preview) 上，滑块将突出显示并设置为4个评论。 "全部模糊" 和 "黑白" 和 "白色" 开关均已设置。](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Review count

使用右上角的滑块设置要在页面上显示的评论数。

### <a name="view-type"></a>视图类型

您可以查看磁贴或详细视图中的不同内容项。 " **详细** 信息视图" 将允许你查看关键帧以及有关选定视频的其他信息。 

> [!NOTE]
> 视频审查服务仅识别和输出可能完整（良好）的帧，而不是定期输出帧。 此功能允许框架级别的成人和猥亵分析有效地生成帧。

**平** 铺视图以单个磁贴的形式显示每个视频。 选择视频帧上方的展开按钮，放大视频并隐藏其他视频。

### <a name="content-obscuring-effects"></a>内容遮掩效果

使用 " **全部模糊** " 和 " **黑白** " 开关设置这些内容隐藏效果。 默认情况下，它们处于启用状态。 在 **平铺** 视图中，可以单独切换每个视频的效果。

## <a name="check-video-details"></a>检查视频详细信息

在 **详细** 信息视图中，右窗格将显示多个选项卡，其中显示了有关视频的详细信息。

* 选择 " **注释** " 选项卡，将自定义笔记添加到视频。
* 选择 " **脚本** " 选项卡以查看视频脚本 &mdash; 。该服务会自动提取视频中任何语音的脚本。 当你选择某个文本部分时，视频播放器将跳转到此部分视频。
* 选择 " **元数据** " 选项卡以查看视频文件元数据。
* 选择 " **历史记录** " 选项卡以查看查看的历史记录，例如创建时间和修改时间。

> [!div class="mx-imgBorder"]
> ![将突出显示右窗格，并选择 "注释" 选项卡。 有一个标记为 "添加注释" 的测试区域。](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>应用裁决标记

视频审阅的主要任务是在视频或视频部分中应用或删除裁决标记。

### <a name="bulk-tagging"></a>大容量标记

利用 **批量标记** 工具栏，您可以一次向多个选定视频添加标记。 选择一个或多个视频，然后选择要应用的标记，并单击 " **提交**"。 

> [!div class="mx-imgBorder"]
> !["+" 按钮在 "批量标记" 窗格中突出显示。](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>关键帧标记

你还可以将审核标记添加到特定的关键帧。 从 "关键帧磁贴" 窗格中选择帧，然后选择 " **关键帧标记 +** " 以应用所需的标记。

> [!NOTE]
> 如果服务无法提取关键帧，则 "关键帧磁贴" 窗格将显示 " **无可用帧** "，选择关键帧的选项将显示为灰色。在这种情况下，只能使用 **视频标记 +** 按钮) 将标记作为 (整体应用到视频。

> [!div class="mx-imgBorder"]
> ![磁贴窗格、视频播放器、关键帧标记窗格和视频标记窗格都显示出来。 将突出显示 "关键帧标记 +" 和 "视频标记 +" 按钮。](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>暂停评审

使用 "视频" 窗格底部的 " **保存** " 按钮，您可以对其进行检查，以便以后检索它并完成它。 您可以执行此操作，以便查看需要从另一个团队成员或当前不可用的经理那里进行咨询。 

可以通过单击屏幕顶部的 " **保存** " 按钮来查看保持的视频。 保留窗格将显示在右侧。 在此处，你可以选择 "保持多次查看" 并将其释放回队列，或设置其过期时间。 在预先配置的时间长度后，对保留进行的审核将释放回队列。 选择 " **保存** " 从当前选定的过期时间开始计数。

> [!div class="mx-imgBorder"]
> ![在 "视频" 窗格上，突出显示 "保留" 按钮。 在窗格底部，将突出显示 "保留时间" 组合框以及 "发布" 和 "保存" 按钮。](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>提交审阅

应用标记后，选择 "视频" 窗格底部的 " **提交** " 按钮。 如果已标记了多个视频，则可以在单个审阅或单独的审阅中提交它们。

## <a name="limbo-state"></a>Limbo 状态

提交审阅后，视频会移动到 **Limbo** 状态，可以通过选择屏幕顶部的 " **Limbo** " 按钮来查看该状态。 视频在预先配置的时间段内仍处于 Limbo 状态， (可以在底部) 的菜单中更改，或再次查看或手动提交。

视频从 limbo 过期后，其审阅将被标记为 "已完成"。

## <a name="next-steps"></a>后续步骤

- 开始学习[视频审查快速入门](video-moderation-api.md)。
- 了解如何根据审查输出为人工审阅者生成[视频审阅](video-reviews-quickstart-dotnet.md)。
- 向视频审阅添加[视频脚本审阅](video-transcript-reviews-quickstart-dotnet.md)。
- 查看有关如何开发[完整视频审查解决方案](video-transcript-moderation-review-tutorial-dotnet.md)的详细教程。