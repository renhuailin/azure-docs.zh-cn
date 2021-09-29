---
title: Azure 视频分析器媒体版（以前称为视频索引器）的概念 - Azure
titleSuffix: Azure Video Analyzer for Media (formerly Video Indexer)
description: 本文简要概述 Azure 视频分析器媒体版（以前称为视频索引器）的术语和概念。
ms.topic: conceptual
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 6b68ee08d6569bd22709e5b9ee25cbbf745c0470
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630160"
---
# <a name="video-analyzer-for-media-concepts"></a>视频分析器媒体版的概念

本文简要概述 Azure 视频分析器媒体版（以前称为视频索引器）的术语和概念。

## <a name="audiovideocombined-insights"></a>音频/视频/组合见解

将视频上传到视频分析器媒体版时，它会通过运行不同的 AI 模型来分析视觉对象和音频。 当视频分析器媒体版分析视频时，AI 模型将提取见解。 有关详细信息，请参阅[概述](video-indexer-overview.md)。

## <a name="confidence-scores"></a>置信度分数

置信度分数表明了见解的置信度。 此分数是介于 0.0 到 1.0 之间的数字。 分数越高，答案的置信度就越大。 例如， 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>内容审核

使用文本和视觉内容审核模型可保护用户远离不当内容，并验证发布的内容是否与组织的价值观相符。 你可以自动阻止某些视频，或向用户发出有关这些内容的警报。 有关详细信息，请参阅[见解：视觉和文本内容审核](video-indexer-output-json-v2.md#visualcontentmoderation)。 

## <a name="blocks"></a>块   

可以通过块更轻松地浏览数据。 例如，可以在发言人换人或中断时间长时将块细分。  

## <a name="project-and-editor"></a>项目和编辑器

通过[视频分析器媒体版](https://www.videoindexer.ai/)网站，你能够使用视频的深入见解来查找正确的媒体内容，找到你感兴趣的部分，并使用结果创建全新的项目。 创建后，可以从视频分析器媒体版呈现和下载项目，并在自己的编辑应用程序或下游工作流中使用。

在以下情况下，你可能会发现此功能非常有用： 

* 为预告片创建电影亮点。
* 在新闻广播中使用旧的视频剪辑。
* 为社交媒体创建较短的内容。

有关详细信息，请参阅[使用编辑器创建项目](use-editor-create-project.md)。

## <a name="keyframes"></a>关键帧

视频分析器媒体版选择最能表示每个镜头的帧。 关键帧是根据美学属性（例如对比度和稳定性）从整个视频中选取的代表性帧。 有关详细信息，请参阅[场景、镜头和关键帧](scenes-shots-keyframes.md)。

## <a name="time-range-vs-adjusted-time-range"></a>时间范围和调整的时间范围   

TimeRange 是原始视频中的时间范围。 AdjustedTimeRange 是相对于当前播放列表的时间范围。 由于可以从不同视频的不同行来创建播放列表，因此可以取一个 1 小时的视频，只使用其中的 1 行，例如 10:00-10:15。 在这种情况下，你会有一个只有 1 行的播放列表，其中的时间范围是 10:00-10:15，但 adjustedTimeRange 是 00:00-00:15。 

## <a name="widgets"></a>小组件

视频分析器媒体版支持在应用中嵌入小组件。 有关详细信息，请参阅[在应用中嵌入视频分析器媒体版小组件](video-indexer-embed-widgets.md)。

## <a name="summarized-insights"></a>汇总见解  

汇总见解包含聚合视图形式的数据：人脸、主题和情感。 例如，汇总见解包含所有人脸、每个人脸出现的时间范围以及显示人脸的时间百分比，不需在数千个时间范围中逐个检查其中包含哪些人脸。  

## <a name="next-steps"></a>后续步骤

- [概述](video-indexer-overview.md)
- [Insights](video-indexer-output-json-v2.md)
