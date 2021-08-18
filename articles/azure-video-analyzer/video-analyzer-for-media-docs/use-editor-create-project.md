---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）编辑器创建项目和添加视频剪辑
titleSuffix: Azure Video Analyzer for Media
description: 本主题演示如何使用 Azure 视频分析器媒体版（以前称为视频索引器）编辑器创建项目和添加视频剪辑。
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: b2a8ca1b446343ad040244f31a304ec2078d1ce2
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115312"
---
# <a name="add-video-clips-to-your-projects"></a>向项目添加视频剪辑

通过 [Azure 视频分析器媒体版（以前称为视频索引器）](https://www.videoindexer.ai/)网站，你能够使用视频的深入见解来查找正确的媒体内容，找到你感兴趣的部分，并使用结果创建全新的项目。 

创建后，可以从视频分析器媒体版呈现和下载项目，并在自己的编辑应用程序或下游工作流中使用。

在以下情况下，你可能会发现此功能非常有用： 

* 为预告片创建电影亮点。
* 在新闻广播中使用旧的视频剪辑。
* 为社交媒体创建较短的内容。

本文演示如何创建项目并将所选剪辑从视频添加到项目。 

## <a name="create-new-project-and-manage-videos"></a>创建新项目和管理视频

1. 浏览到[视频分析器媒体版](https://www.videoindexer.ai/)网站并登录。
1. 选择“项目”选项卡。如果以前创建了项目，则会在此处看到所有其他项目。
1. 单击“创建新项目”。  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="创建新项目":::
1. 通过单击铅笔图标为项目提供名称。 将显示“不带标题的项目”的文本替换为项目名称，然后单击“检查”。

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="新项目":::
    
### <a name="add-videos-to-the-project"></a>向项目添加视频

> [!NOTE]
> 当前，项目只能包含采用同一语言编制索引的视频。 </br>选择一种语言的视频后，无法在帐户中添加其他语言的视频，具有其他语言的视频会灰显/禁用。

1. 通过选择“添加视频”来添加要在此项目中使用的视频。

    你会看到帐户中的所有视频，以及一个显示“搜索文本、关键字或视觉内容”的搜索框。 可以搜索在脚本和 OCR 中具有指定人员、标签、品牌、关键字或匹配项的视频。
    
    例如，在下图中，我们仅查找在脚本中提到“custom vision”的视频（如果要筛选搜索结果，请使用筛选器）。
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="屏幕截图显示搜索提到 custom vision 的视频":::
1. 单击“添加”以将视频添加到项目。
1. 现在，你会看到选择的所有视频。 这些是你要从中为项目选择剪辑的视频。

    可以通过拖放或是选择列表菜单按钮并选择“下移”或“上移”来重新排列视频的顺序 。 从列表菜单中，还可以从此项目中删除视频。 
    
    可以通过选择“添加视频”随时向此项目添加更多视频。 还可以向项目添加同一视频的多个实例。 如果要显示一个视频中的剪辑，然后显示另一个视频中的剪辑，再然后显示第一个视频中的另一个剪辑，则可能要执行此操作。 

### <a name="select-clips-to-use-in-your-project"></a>选择要在项目中使用的剪辑

如果单击每个视频右侧的向下箭头，则会基于时间戳打开视频中的见解（视频剪辑）。 

1. 若要为特定剪辑创建查询，请使用显示“在脚本、视觉文本、人物和标签中搜索”的搜索框。
1. 选择“查看见解”以自定义要查看和不想查看的见解。 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="屏幕截图显示搜索显示“试用认知服务”的视频":::
1. 通过选择“筛选器选项”，添加筛选器以进一步指定有关你要查找的场景的详细信息。

    可以添加多个筛选器。 
1. 对结果感到满意后，通过选择要添加的段来选择要添加到此项目的剪辑。 可以通过再次单击段来取消选择此剪切。
    
    通过单击视频旁的列表菜单选项并选择“全选”，来添加视频的所有段（或是搜索之后返回的所有段）。 

选择剪辑并进行排序时，可以在页面右侧的播放器中预览视频。 

> [!IMPORTANT]
> 请记住在进行更改时，通过选择页面顶部的“保存项目”来保存项目。 

### <a name="render-and-download-the-project"></a>呈现和下载项目

> [!NOTE]
> 对于视频分析器媒体版付费帐户，呈现你的项目会产生编码成本。 视频分析器媒体版试用帐户的呈现时间限制为 5 小时。

1. 完成后，请确保已保存项目。 现在可以呈现此项目。 单击“呈现”，会显示一个弹出对话框，告知你视频分析器媒体版会呈现一个文件，然后下载链接会发送到你的电子邮件。 选择“继续”。 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="屏幕截图显示视频分析器媒体版，其中包含用于呈现和下载项目的选项":::
    
    还会看到一个通知，指出项目在页面顶部呈现。 完成呈现后，你会看到一个新通知，指出项目已成功呈现。 单击通知以下载项目。 它会以 mp4 格式下载项目。
1. 可以从“项目”选项卡访问保存的项目。 

    如果选择此项目，则会看到此项目的所有见解和时间线。 如果选择“视频编辑器”，则可以继续编辑此项目。 编辑包括添加或删除视频和剪辑，或是重命名项目。
    
## <a name="create-a-project-from-your-video"></a>从视频创建项目

可以直接从帐户中的视频创建新项目。 

1. 转到视频分析器媒体版网站的“库”选项卡。
1. 打开要用于创建项目的视频。 在见解和时间线页上，选择“视频编辑器”按钮。

    这会使你进入用于创建新项目的同一页。 与新项目不同，你会看到先前已开始编辑的视频的带时间戳见解段。

## <a name="see-also"></a>另请参阅

[视频分析器媒体版概述](video-indexer-overview.md)

