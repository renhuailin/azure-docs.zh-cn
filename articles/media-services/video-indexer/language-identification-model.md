---
title: 使用视频索引器自动识别口语 - Azure
titleSuffix: Azure Media Services
description: 本文介绍如何使用视频索引器语言识别模型来自动识别视频中的口语。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "81687125"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>通过语言识别模型自动识别口述语言

视频索引器支持自动语言识别 (LID)，这是一个从音频中自动识别口语内容，并发送媒体文件，以便将其以主导识别语言进行转录的过程。 

目前 LID 支持：英语、西班牙语、法语、德语、意大利语、中文（普通话）、日语、俄语和葡萄牙语（巴西）。 

请确保查看下面的[准则和限制](#guidelines-and-limitations)部分。

## <a name="choosing-auto-language-identification-on-indexing"></a>在编制索引时选择自动语言识别

使用 API 为视频编制索引或[重新编制索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)时，请在 `sourceLanguage` 参数中选择 `auto detect` 选项。

使用门户时，请在[视频索引器](https://www.videoindexer.ai/)主页上转到你的帐户视频，并将鼠标悬停在要重新索引的视频的名称上。 在右下角单击重新索引按钮。 在“重新索引视频”对话框中，从“视频源语言”下拉框中选择“自动检测”。

![自动检测](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>模型输出

视频索引器会根据最可能的语言（如果该语言的置信度 `> 0.6`）转录视频。 如果该语言无法通过置信度识别，则视频索引器会假定所讲语言为英语。 

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
* 尽管视频索引器支持阿拉伯语（现代标准和黎凡特）、印地语和韩语，但 LID 不支持这些语言。
* 如果音频包含上述支持列表以外的语言，则会出现意外结果。
* 如果视频索引器无法以足够高的置信度 (`>0.6`) 识别语言，则回退语言为英语。
* 当前不支持使用混合语言音频的文件。 如果音频包含混合语言，则会出现意外结果。 
* 低质音频可能会影响模型结果。
* 该模型要求音频中至少有一分钟的语音。
* 该模型旨在识别自发的对话语音（而不是语音命令、唱歌等）。

## <a name="next-steps"></a>后续步骤

* [概述](video-indexer-overview.md)
* [自动识别和转录多语言内容](multi-language-identification-transcription.md)
