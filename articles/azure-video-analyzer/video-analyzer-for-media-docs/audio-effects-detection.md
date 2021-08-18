---
title: 音频效果检测
titleSuffix: Azure Video Analyzer
description: 音频效果检测是 Azure 视频分析器媒体版 AI 功能之一。 它可以检测各种声学事件，并将它们分类为不同的声学类别（例如，枪声、尖叫和人群反应等）。
author: Juliako
manager: femila
ms.service: azure-video-analyzer
ms.subservice: azure-video-analyzer-media
ms.topic: article
ms.date: 05/12/2021
ms.author: juliako
ms.openlocfilehash: d576408cc0d65cc908a24b27ae2a6a50ed7aa9ee
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121550"
---
#  <a name="audio-effects-detection-preview"></a>音频效果检测（预览版）

音频效果检测是 Azure 视频分析器媒体版 AI 功能之一。 它可以检测各种声学事件，并将它们分类为不同的声学类别（例如，枪声、尖叫和人群反应等）。
 
可以在许多领域中使用音频事件检测。 两个示例包括：

* 公共安全和司法领域使用音频效果检测。 音频效果检测可以检测并分类枪声、爆炸声和玻璃碎裂声。 因此，可以在智慧城市系统或包含照相机和麦克风的其他公共环境中实现该功能。 提供快速准确的暴力事件检测。 
* 在媒体和娱乐领域，具有大量视频存档的公司可以通过使用非语音效果来增强其视频听录，为存在听力障碍的人士提供更多上下文，从而轻松改善其辅助功能方案。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/audio-effects.jpg" alt-text="音频效果图像":::
<br/>视频分析器媒体版音频效果检测输出的示例

## <a name="supported-audio-categories"></a>支持的音频类别  

音频效果检测可以检测并分类 8 种不同的类别。 在下一个表中，可以找到不同的类别，这些类别分为不同的 VI 预设，而 VI 预设又分为“标准”和“高级”。  有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/media-services/)。

|索引编制类型 |标准索引编制| 高级索引编制|
|---|---|---|
|预设名称 |“仅音频” <br/>“视频 + 音频” |“高级音频”<br/> “高级视频 + 音频”|
|显示在“见解”窗格中|| V|
|人群反应 |V| V|
| 静音| V| V|
| 枪声 ||V |
| 玻璃碎裂 ||V|
| 警报响铃|| V |
| 汽笛鸣响|| V |
| 笑声|| V |
| 狗吠声|| V|

## <a name="result-formats"></a>结果格式

音频效果在见解 JSON 中检索，其中包括每个类别的类别 ID、类型、名称和一组实例及其特定时间范围和置信度分数。

`name` 参数以用于为 JSON 编制索引的语言提供，而类型将始终保持不变。

```json
audioEffects: [{
        id: 0,
        type: "Gunshot",
        name: "Gunshot",
        instances: [{
                confidence: 0.649,
                adjustedStart: "0:00:13.9",
                adjustedEnd: "0:00:14.7",
                start: "0:00:13.9",
                end: "0:00:14.7"
            }, {
                confidence: 0.7706,
                adjustedStart: "0:01:54.3",
                adjustedEnd: "0:01:55",
                start: "0:01:54.3",
                end: "0:01:55"
            }
        ]
    }, {
        id: 1,
        type: "CrowdReactions",
        name: "Crowd Reactions",
        instances: [{
                confidence: 0.6816,
                adjustedStart: "0:00:47.9",
                adjustedEnd: "0:00:52.5",
                start: "0:00:47.9",
                end: "0:00:52.5"
            },
            {
                confidence: 0.7314,
                adjustedStart: "0:04:57.67",
                adjustedEnd: "0:05:01.57",
                start: "0:04:57.67",
                end: "0:05:01.57"
            }
        ]
    }
],
```

## <a name="how-to-index-audio-effects"></a>如何为音频效果编制索引

若要将索引过程设置为包括音频效果检测，用户应选择“视频 + 音频索引”菜单下的高级预设之一，如下图所示。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/index-audio-effect.png" alt-text="音频效果索引编制图像":::

## <a name="closed-caption"></a>隐藏式字幕

检索隐藏式字幕文件中的音频效果时，将在方括号中进行检索，其结构如下：

|类型| 示例|
|---|---|
|SRT |00:00:00,000  00:00:03,671<br/>[Gunshot]|
|VTT |00:00:00.000  00:00:03.671<br/>[Gunshot]|
|TTML|置信度：0.9047 <br/> <p begin="00:00:00.000" end="00:00:03.671">[Gunshot]</p>|
|TXT |[Gunshot]|
|CSV |0.9047,00:00:00.000,00:00:03.671, [Gunshot]|

将使用以下逻辑来检索隐藏式字幕文件中的音频效果：

* `Silence` 事件类型不会添加到隐藏式字幕
* 显示事件的最长持续时间为 5 秒
* 显示事件的最短计时器持续时间为 700 毫秒

## <a name="adding-audio-effects-in-closed-caption-files"></a>在隐藏式字幕文件中添加音频效果

可以通过[获取视频字幕 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Captions) 向 Azure 视频分析器支持的隐藏式字幕文件添加音频效果，方法是在 `includeAudioEffects` 参数中选择 true；也可以通过 video.ai 门户进行添加，方法是选择“下载” -> “隐藏式字幕” -> “包含音频效果”。  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/close-caption.jpg" alt-text="隐藏式字幕中的音频效果":::

> [!NOTE]
> 从隐藏式字幕文件中使用[更新脚本](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)或者从隐藏式字幕文件中使用[更新自定义语言模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model)时，系统会忽略这些文件中包含的音频效果。

## <a name="limitations-and-assumptions"></a>限制和假设

* 该模型仅适用于非语音段。
* 该模型目前一次只能处理单个类别。 例如，目前不支持背景中的哭泣声和讲话或枪声 + 爆炸声。
* 该模型目前不支持背景中有吵闹音乐的情况。
* 最小段长度为 2 秒。

## <a name="next-steps"></a>后续步骤

查看[概述](video-indexer-overview.md)
