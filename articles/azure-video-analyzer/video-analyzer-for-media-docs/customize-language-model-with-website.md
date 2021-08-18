---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）网站自定义语言模型
titleSuffix: Azure Video Analyzer for Media
description: 了解如何使用 Azure 视频分析器媒体版（以前称为视频索引器）网站自定义语言模型。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: 67610ef51a11aca59b65dd60a8a70dbf10d59e50
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121469"
---
# <a name="customize-a-language-model-with-the-video-analyzer-for-media-website"></a>使用视频分析器媒体版网站自定义语言模型

Azure 视频分析器媒体版（以前称为视频索引器）可以让你通过上传自适应文本（即其词汇需要引擎来适应的领域中的文本）创建自定义语言模型来自定义语音识别。 训练模型后，可以识别自适应文本中显示的新单词。

有关自定义语言模型的详细概述和最佳做法，请参阅[使用视频分析器媒体版自定义语言模型](customize-language-model-overview.md)。

可以使用视频分析器媒体版网站在帐户中创建和编辑自定义语言模型，如本主题所述。 也可使用 API，如[使用 API 自定义语言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>创建语言模型

1. 转到[视频分析器媒体版](https://www.videoindexer.ai/)网站并登录。
1. 若要自定义帐户中的模型，请选择页面左侧的“内容模型自定义”按钮。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="在视频分析器媒体版中自定义内容模型":::
1. 选择“语言”选项卡。

    此时会看到受支持的语言的列表。
1. 在所需语言下选择“添加模型”。
1. 键入语言模型的名称，然后按 Enter。

    此步骤可创建模型并提供将文本文件上传到模型的选项。
1. 若要添加文本文件，请选择“添加文件”。 此时文件资源管理器会打开。
1. 导航到文本文件并将其选中。 可以将多个文本文件添加到语言模型。

    也可选择语言模型右侧的“...”按钮，然后选择“添加文件”，以这种方式添加文本文件。 
1. 上传完文本文件以后，请选择绿色的“训练”选项。

训练过程可能需要数分钟。 训练完以后，模型旁边会出现“已训练”字样。 可以预览、下载和删除模型中的此文件。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="模型":::

### <a name="using-a-language-model-on-a-new-video"></a>在新视频上使用语言模型

若要在新视频上使用语言模型，请执行以下操作之一：

* 选择页面顶部的“上传”按钮。

    ![“上传”按钮 - 视频分析器媒体版](./media/customize-language-model/upload.png)
* 放置音频或视频文件或者以浏览方式查找文件。

你可以选择“视频源语言”。 选择下拉列表，从其中选择已创建的语言模型。 它会显示语言模型的语言，以及在括号中提供的名称。 例如：

![选择视频源语言 - 使用视频分析器媒体版为视频重新编制索引](./media/customize-language-model/reindex.png)

在页面底部选择“上传”选项，系统就会使用语言模型为新视频编制索引。

### <a name="using-a-language-model-to-reindex"></a>使用语言模型重新编制索引

若要使用语言模型为集合中的视频重新编制索引，请执行以下步骤：

1. 登录到[视频分析器媒体版](https://www.videoindexer.ai/)主页。
1. 单击视频上的“...”按钮，然后选择“重新编制索引”。
1. 此时可选择为视频重新编制索引时使用的“视频源语言”。 选择下拉列表，从其中选择已创建的语言模型。 它会显示语言模型的语言，以及在括号中提供的名称。
1. 选择“重新编制索引”按钮，系统就会使用语言模型为视频重新编制索引。

## <a name="edit-a-language-model"></a>编辑语言模型

编辑语言模型时，可以更改其名称、向其添加文件，以及删除其中的文件。

如果在语言模型中添加或删除文件，则需选择绿色的“训练”选项，再次训练模型。

### <a name="rename-the-language-model"></a>重命名语言模型

可以更改语言模型的名称，方法是：选择语言模型右侧的省略号“...”按钮，然后选择“重命名”。 

键入新名称，然后按 Enter。

### <a name="add-files"></a>添加文件

若要添加文本文件，请选择“添加文件”。 此时文件资源管理器会打开。

导航到文本文件并将其选中。 可以将多个文本文件添加到语言模型。

也可选择语言模型右侧的省略号“...”按钮，然后选择“添加文件”，以这种方式添加文本文件。 

### <a name="delete-files"></a>删除文件

若要删除语言模型中的文件，请选择文本文件右侧的省略号“...”按钮，然后选择“删除”。  此时会弹出一个新窗口，指出删除操作无法撤消。 在新窗口中选择“删除”选项。

此操作会从语言模型中彻底删除该文件。

## <a name="delete-a-language-model"></a>删除语言模型

若要删除帐户中的语言模型，请选择语言模型右侧的省略号“...”按钮，然后选择“删除”。 

此时会弹出一个新窗口，指出删除操作无法撤消。 在新窗口中选择“删除”选项。

此操作会从帐户中彻底删除该语言模型。 使用已删除语言模型的任何视频都会保留相同的索引，直到为该视频重新编制索引为止。 如果重新为视频编制索引，可为视频分配新的语言模型。 否则，视频分析器媒体版会使用其默认模型重新为视频编制索引。

## <a name="customize-language-models-by-correcting-transcripts"></a>通过更正脚本来自定义语言模型

视频分析器媒体版支持根据用户对视频听录进行的实际更正自动自定义语言模型。

1. 若要更正脚本，请打开你想要从“帐户视频”对其进行编辑的视频。 选择“时间线”选项卡。

    ![自定义语言模型 -“时间线”选项卡 - 视频分析器媒体版](./media/customize-language-model/timeline.png)

1. 选择铅笔图标以编辑听录的脚本。

    ![自定义语言模型 - 编辑听录 - 视频分析器媒体版](./media/customize-language-model/edits.png)

    视频分析器媒体版会捕获你在视频听录中更正的所有行，并将它们自动添加到名为“来自脚本编辑”的文本文件中。 这些编辑用于重新训练为此视频编制索引时使用的特定语言模型。 
    
    还包括在[小组件](video-indexer-embed-widgets.md)的时间线中进行的编辑。
    
    如果在为此视频编制索引时未指定语言模型，则此视频的所有编辑都将存储在检测到的视频语言中名为“帐户改编”的默认语言模型中。
    
    如果对同一行进行了多次编辑，则只会使用最新版本的已更正行来更新语言模型。  
    
    > [!NOTE]
    > 仅使用文本形式的更正进行自定义。 不包括不涉及实际字词的更正（例如，标点符号或空格）。
    
1. 你将在“内容模型自定义”页的“语言”选项卡中看到更正的脚本。

   若要查看每个语言模型的“来自脚本编辑”文件，请将其选中并打开。

    ![来自脚本编辑 - 视频分析器媒体版](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>后续步骤

[使用 API 自定义语言模型](customize-language-model-with-api.md)
