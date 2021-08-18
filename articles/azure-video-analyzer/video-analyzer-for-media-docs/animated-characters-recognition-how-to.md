---
title: 有关使用 Azure 视频分析器媒体版（以前称为视频索引器）进行动画人物检测的操作指南
titleSuffix: Azure Video Analyzer
description: 本操作指南演示如何通过 Azure 视频分析器媒体版（以前称为视频索引器）使用动画人物检测。
services: azure-video-analyzer
author: Juliako
manager: femila
ms.custom: references_regions
ms.topic: how-to
ms.subservice: azure-video-analyzer-media
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: a9807ac57130034b51c3188b56de32ade4db6844
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121574"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>通过门户和 API 使用动画人物检测（预览版） 

Azure 视频分析器媒体版（以前称为视频索引器）支持检测、分组和识别动画内容中的人物，此功能可通过 Azure 门户和 API 使用。 请查看[此概述](animated-characters-recognition.md)主题。

本文演示如何通过 Azure 门户和视频分析器媒体版 API 使用动画人物检测。

## <a name="use-the-animated-character-detection-with-portal"></a>通过门户使用动画人物检测 

在试用帐户中，自定义视觉集成由视频分析器媒体版管理，你可以开始创建并使用动画人物模型。 如果你使用试用帐户，可以跳过下面的“连接自定义视觉帐户”部分。

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>连接自定义视觉帐户（仅限付费帐户）

如果你拥有一个视频分析器媒体版付费帐户，需要先连接自定义视觉帐户。 如果你还没有自定义视觉帐户，请创建一个。 有关详细信息，请参阅[自定义视觉](../../cognitive-services/custom-vision-service/overview.md)。

> [!NOTE]
> 这两个帐户需位于同一区域。 日本区域目前不支持自定义视觉集成。

有权访问其自定义视觉帐户的付费帐户可在自定义视觉帐户中查看模型和标记的图像。 详细了解如何 [在自定义视觉中改进分类器](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)。 

请注意，只能通过视频分析器媒体版而不是自定义视觉网站来执行模型训练。 

#### <a name="connect-a-custom-vision-account-with-api"></a>通过 API 连接自定义视觉帐户 

遵循以下步骤将自定义视觉帐户连接到视频分析器媒体版或更改当前已连接到视频分析器媒体版的自定义视觉帐户：

1. 浏览到 [www.customvision.ai](https://www.customvision.ai) 并登录。
1. 复制训练资源和预测资源的密钥：

    > [!NOTE]
    > 若要提供所有密钥，你需要在自定义视觉中有两个单独的资源，一个资源用于训练，一个用于预测。
1. 提供其他信息：

    * 端点 
    * 预测资源 ID
1. 浏览并登录到[视频分析器媒体版](https://vi.microsoft.com/)。
1. 单击页面右上角的问号，然后选择“API 参考”。
1. 通过单击“产品”选项卡，确认你订阅了 API 管理。如果已连接 API，则可以继续下一步，否则请进行订阅。 
1. 在开发人员门户中，单击“完整 API 参考”并浏览到“操作” 。  
1. 选择“连接自定义视觉帐户(预览版)”，然后单击“试用” 。
1. 填写必填字段以及访问令牌，然后单击“发送”。 

    有关如何获取视频索引器访问令牌的详细信息，请转到[开发人员门户](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)并查看[相关文档](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)。  
1. 调用返回“200 正常”响应后，即表示你的帐户已连接。
1. 若要通过浏览到[视频分析器媒体版](https://vi.microsoft.com/)门户来验证连接，请执行以下操作：
1. 单击右上角的“内容模型自定义”按钮。
1. 转到“动画人物”选项卡。
1. 单击“在自定义视觉中管理模型”后，你将转到刚刚连接的自定义视觉帐户。

> [!NOTE]
> 目前仅支持通过视频分析器媒体版创建的模型。 通过自定义视觉创建的模型不可用。 此外，最佳做法是仅在视频分析器媒体版平台中编辑通过视频分析器媒体版创建的模型，因为通过自定义视觉进行的更改可能会导致意外结果。

### <a name="create-an-animated-characters-model"></a>创建动画人物模型

1. 浏览到[视频分析器媒体版](https://vi.microsoft.com/)网站并登录。
1. 若要自定义帐户中的模型，请选择页面左侧的“内容模型自定义”按钮。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="在视频分析器媒体版中自定义内容模型":::
1. 在“模型自定义”部分转到“动画人物”选项卡。
1. 单击“添加模型”。
1. 为模型命名，然后按 Enter 保存该名称。

> [!NOTE]
> 最佳做法是为每个动画序列提供一个自定义视觉模型。 

### <a name="index-a-video-with-an-animated-model"></a>使用动画模型为视频编制索引

对于初始训练，请至少上传两段视频。 每段视频的持续时间最好是超过 15 分钟，这样才有望获得识别能力较好的模型。 如果你的片段较短，我们建议在训练之前上传至少持续 30 分钟的视频内容。 这样，就可以合并属于不同场景和背景中同一人物的组，从而提高在后续编制索引的片段中检测到该人物的可能性。 若要基于多个视频（片段）训练模型，需使用同一动画模型为其编制索引。 

1. 单击“上传”按钮。
1. （从文件或 URL）选择要上传的视频。
1. 单击“高级选项”。
1. 在“人员/动画人物”下，选择“动画模型” 。
1. 如果你只有一个模型，则系统会自动选择该模型；如果你有多个模型，可以从下拉菜单中选择相关的模型。
1. 单击“上传”。
1. 为视频编制索引后，“见解”窗格中的“动画人物”部分会显示检测到的人物 。

在标记和训练模型之前，所有动画人物将命名为“未知 #X”。 训练模型后，还会识别这些人物。

### <a name="customize-the-animated-characters-models"></a>自定义动画人物模型

1. 在视频分析器媒体版中为人物命名。

    1. 在模型创建人物组后，建议在自定义视觉中检查这些组。 
    1. 若要标记视频中的动画人物，请转到“见解”选项卡，并单击窗口右上角的“编辑”按钮。 ****   ****   
    1. 在“见解”窗格中，单击任一检测到的动画人物，并将其名称从“未知 #X”更改为临时名称（或先前分配给该人物的名称）。 ****   
    1. 键入新名称后，单击新名称旁边的复选图标。 这会将新名称保存到视频分析器媒体版的模型中。 
1. 仅限付费帐户：在自定义视觉中检查组 

    > [!NOTE]
    > 有权访问其自定义视觉帐户的付费帐户可在自定义视觉帐户中查看模型和标记的图像。 详细了解如何 [在自定义视觉中改进分类器](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)。 请务必注意，只能根据本主题所述通过视频分析器媒体版执行模型训练，而不要通过自定义视觉网站训练。 

    1. 在视频分析器媒体版中转到“自定义模型”页，然后选择“动画人物”选项卡 。 
    1. 单击正在处理的模型对应的“编辑”按钮，在自定义视觉中对其进行管理。 
    1. 检查每个人物组： 

        * 如果该组包含不相关的图像，我们建议在自定义视觉网站中删除这些图像。 
        * 如果存在属于不同人物的图像，请更改这些特定图像中的标记，方法是单击该图像，然后添加正确的标记并删除错误的标记。 
        * 如果该组不正确（也就是说，它主要包含非人物图像，或包含多个人物的图像），你可以在自定义视觉网站或视频分析器媒体版见解中将其删除。 
        * 分组算法有时会将人物拆分到不同的组。 因此，建议在视频分析器媒体版见解中为属于同一人物的所有组指定相同的名称，这样，所有这些组就会立即以它们在自定义视觉网站上的相同显示方式显示。 
    1. 调整该组后，请确保标记该组时所用的初始名称反映了该组中的人物。 
1. 定型模型 

    1. 编辑完所需的所有名称后，需要训练模型。 
    1. 在模型中训练某个人物后，在使用该模型编制索引的下一段视频中就会识别到该人物。 
    1. 打开“自定义”页，单击“动画人物”选项卡，然后单击“训练”按钮以训练模型。 ****   **** 为了保持视频 
    
索引器与模型之间的连接，请不要在自定义视觉网站中训练模型（付费帐户有权访问自定义视觉网站），而只在视频分析器媒体版中训练。 训练后，使用该模型编制或重新编制索引的任何视频都会识别到已训练的人物。 

## <a name="delete-an-animated-character-and-the-model"></a>删除动画人物和模型

1. 删除动画人物。

    1. 若要在视频见解中删除某个动画人物，请转到“见解”选项卡，然后单击窗口右上角的“编辑”按钮 。
    1. 选择该动画人物，然后单击其名称下面的“删除”按钮。

    > [!NOTE]
    > 这会从此视频中删除见解，但不影响模型。
1. 删除模型。

    1. 在顶部菜单中单击“内容模型自定义”按钮，然后转到“动画人物”选项卡 。
    1. 单击要删除的模型右侧的省略号图标，然后单击“删除”按钮。
    
    * 付费帐户：模型将与视频分析器媒体版断开连接，并且你无法重新连接。
    * 试用帐户：还会从自定义视觉中删除该模型。 
    
        > [!NOTE]
        > 在试用帐户中只能使用一个模型。 删除该模型后，无法训练其他模型。

## <a name="use-the-animated-character-detection-with-api"></a>通过 API 使用动画人物检测 

1. 连接自定义视觉帐户。

    如果你拥有一个视频分析器媒体版付费帐户，需要先连接自定义视觉帐户。 <br/>
    如果你还没有自定义视觉帐户，请创建一个。 有关详细信息，请参阅[自定义视觉](../../cognitive-services/custom-vision-service/overview.md)。

    [使用 API 连接你的自定义视觉帐户](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account)。
1. 创建动画人物模型。

    使用[创建动画模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model) API。
1. 为视频编制或重新编制索引。

    使用[重新编制索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API。 
1. 自定义动画人物模型。

    使用[训练动画模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model) API。

### <a name="view-the-output"></a>查看输出

查看生成的 JSON 文件中的动画人物。

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>限制

* 目前，东亚区域不支持“动画识别”功能。
* 如果视频质量较差，可能无法正确识别在视频中看起来较小或较远的人物。
* 建议对每组动画人物（例如每个动画序列）使用一个模型。

## <a name="next-steps"></a>后续步骤

[视频分析器媒体版概述](video-indexer-overview.md)
