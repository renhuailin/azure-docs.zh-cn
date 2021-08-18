---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）自动识别和听录多语言内容
titleSuffix: Azure Video Analyzer for Media
description: 本主题演示如何使用 Azure 视频分析器媒体版（以前称为视频索引器）自动识别和听录多语言内容。
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: a0466a8123a3732e78cd35826290b3e29e3c8c50
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119217"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>自动识别和转录多语言内容

Azure 视频分析器媒体版（以前称为视频索引器）支持自动识别语言和听录多语言内容。 此过程涉及自动识别音频中不同片段中的口语，发送每个要听录的媒体文件片段，以及将听录合并为一份统一的听录内容。 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>在使用门户编制索引时选择多语言识别

可以在上传视频和为视频编制索引时选择“多语言检测”。 也可以在为视频重新编制索引时选择“多语言检测”。 以下步骤介绍如何重新编制索引：

1. 浏览到[视频分析器媒体版](https://vi.microsoft.com/)网站并登录。
1. 转到“库”页，将鼠标悬停在要重新编制索引的视频的名称上。 
1. 在右下角单击“重新编制视频索引”按钮。 
1. 从“重新编制视频索引”对话框的“视频源语言”下拉框中选择“多语言检测”  。

    * 在将某个视频作为多语言编制索引时，见解页会包括该选项，并且会出现一个附加的见解类型，使用户能够查看使用了哪种“口语”来听录哪个片段。
    * 多语言听录完全支持翻译为所有语言的功能。
    * 所有其他见解都将使用检测到的主语言来显示，主语言是指在音频中出现最多的语言。
    * 播放器上的隐藏式字幕也会以多语言形式提供。

![门户体验](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>在使用 API 编制索引时选择多语言识别

在使用 API 对视频编制索引或[重新编制索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)时，请在 `sourceLanguage` 参数中选择“`multi-language detection`”选项。

### <a name="model-output"></a>模型输出

该模型将在一个列表中检索在视频中检测到的所有语言

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

此外，听录部分的每个实例都将包含在听录该实例时所使用的语言

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>指导原则和限制

* 受支持的语言组：英语、法语、德语、西班牙语。
* 对多语言内容的支持最多使用三种受支持的语言。
* 如果音频包含上述支持列表以外的语言，则会出现意外结果。
* 为每种语言检测的片段长度最小为 15 秒。
* 语言检测偏移平均为 3 秒。
* 语音应是连续的。 频繁更换语言可能会影响模型性能。
* 非母语发言人的语音可能会影响模型性能（例如，如果发言人在使用其母语时切换为另一种语言，则会发生这种情况）。
* 该模型旨在识别具有合理声音效果的自然对话语音（而不是语音命令、唱歌，等等）。
* 对于多语言视频，目前无法进行项目创建和编辑。
* 在使用多语言检测时，自定义语言模型不可用。
* 不支持添加关键字。
* 在导出隐藏式字幕文件时，不会显示语言指示。
* 更新听录 API 不支持多语言文件。

## <a name="next-steps"></a>后续步骤

[视频分析器媒体版概述](video-indexer-overview.md)
