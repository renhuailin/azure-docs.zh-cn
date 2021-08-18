---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）自动识别口述语言 - Azure
titleSuffix: Azure Video Analyzer for Media
description: 本文介绍如何使用 Azure 视频分析器媒体版（以前称为视频索引器）识别模型来自动识别视频中的口述语言。
services: azure-video-analyzer
author: juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 8caada82b0c4b0c99e2d18b8586cc0b2c9d575f4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119530"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>通过语言识别模型自动识别口述语言

Azure 视频分析器媒体版（以前称为视频索引器）支持自动语言识别 (LID)，这是一个从音频中自动识别口述语言内容，并发送媒体文件，以便将其以主导识别语言进行听录的过程。 

目前 LID 支持：英语、西班牙语、法语、德语、意大利语、中文（普通话）、日语、俄语和葡萄牙语（巴西）。 

请确保查看下面的[准则和限制](#guidelines-and-limitations)部分。

## <a name="choosing-auto-language-identification-on-indexing"></a>在编制索引时选择自动语言识别

使用 API 为视频编制索引或[重新编制索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)时，请在 `sourceLanguage` 参数中选择 `auto detect` 选项。

使用门户时，请在[视频分析器媒体版](https://www.videoindexer.ai/)主页上转到你的帐户视频，并将鼠标悬停在要重新索引的视频的名称上。 在右下角单击重新索引按钮。 在“重新索引视频”对话框中，从“视频源语言”下拉框中选择“自动检测”。

![自动检测](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>模型输出

视频分析器媒体版会根据最可能的语言（如果该语言的置信度 `> 0.6`）听录视频。 如果该语言无法通过置信度识别，则视频索引器会假定所讲语言为英语。 

模型主导语言在见解 JSON 中以 `sourceLanguage` 属性（在“root/videos/insights”下）的形式提供。 相应的置信度分数也会在 `sourceLanguageConfidence` 属性下提供。

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>指导原则和限制

* 自动语言识别 (LID) 支持以下语言： 

    英语、西班牙语、法语、德语、意大利语、中文（普通话）、日语、俄语和葡萄牙语（巴西）。
* 尽管视频分析器媒体版支持阿拉伯语（现代标准和黎凡特）、印地语和韩语，但 LID 不支持这些语言。
* 如果音频包含上述支持列表以外的语言，则会出现意外结果。
* 如果视频分析器媒体版无法以足够高的置信度 (`>0.6`) 识别语言，则回退语言为英语。
* 当前不支持使用混合语言音频的文件。 如果音频包含混合语言，则会出现意外结果。 
* 低质音频可能会影响模型结果。
* 该模型要求音频中至少有一分钟的语音。
* 该模型旨在识别自发的对话语音（而不是语音命令、唱歌等）。

## <a name="next-steps"></a>后续步骤

* [概述](video-indexer-overview.md)
* [自动识别和转录多语言内容](multi-language-identification-transcription.md)
