---
title: 从索引器 v1 和 v2 迁移到 Azure 媒体服务视频索引器 | Microsoft Docs
description: 本主题介绍如何从 Azure Media Indexer v1 和 v2 迁移到 Azure 媒体服务视频索引器。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2021
ms.author: inhenkel
ms.openlocfilehash: 3a2871bd42e6b4da9ec20dc918c5c0ab79ed1a64
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721550"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-analyzer-for-media"></a>从 Media Indexer 和 Media Indexer 2 迁移到视频分析器媒体版 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> 建议客户从索引器 v1 和索引器 v2 迁移到使用[媒体服务 v3 AudioAnalyzerPreset 基本模式](../latest/analyze-video-audio-files-concept.md)。 [Azure Media Indexer](media-services-index-content.md) 媒体处理器和 [Azure Media Indexer 2 预览版](./legacy-components.md)媒体处理器即将停用。 有关停用日期，请参阅此[旧组件](legacy-components.md)主题。

Azure 视频分析器媒体版基于 Azure 媒体分析、Azure 认知搜索、认知服务（例如人脸 API、Microsoft 翻译工具、计算机视觉 API 和自定义语音识别服务）构建。 借助该服务，可以使用视频分析器媒体版视频和音频模型从视频中提取见解。 要了解视频分析器媒体版的适用场景、它提供的功能，以及如何开始使用，请参阅[视频分析器媒体版视频和音频模型](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md)。 

可以通过使用 [Azure 媒体服务 v3 分析器预设](../latest/analyze-video-audio-files-concept.md)或直接使用[视频分析器媒体版 API](https://api-portal.videoindexer.ai/) 从视频和音频文件中提取见解。 目前，视频分析器媒体版 API 和媒体服务 v3 API 提供的功能之间存在重叠。

> [!NOTE]
> 要了解视频分析器媒体版与媒体服务分析器预设的区别，请查看[比较文档](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)。

本文介绍从 Azure Media Indexer 和 Azure Media Indexer 2 迁移到视频分析器媒体版的步骤。  

## <a name="migration-options"></a>迁移选项

|如果需要  |然后在受影响的域控制器上，运行 |
|---|---|
|一个以隐藏式字幕文件格式为任何格式的媒体文件提供语音转文本听录的解决方案：VTT、SRT 或 TTML<br/>以及其他音频见解，例如：关键字、主题推理、声音事件、说话人分割聚类、实体提取和翻译| 更新应用程序，以通过视频分析器媒体版 v2 REST API 或 Azure 媒体服务 v3 音频分析器预设使用视频分析器媒体版功能。|
|语音转文本功能| 直接使用认知服务语音 API。|  

## <a name="getting-started-with-video-analyzer-for-media"></a>开始使用视频分析器媒体版

以下部分指向相关链接：[如何开始使用视频分析器媒体版？](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md#how-can-i-get-started-with-video-analyzer-for-media) 

## <a name="getting-started-with-media-services-v3-apis"></a>媒体服务 v3 API 入门

借助 Azure 媒体服务 v3 API，可以通过 [Azure 媒体服务 v3 分析器预设](../latest/analyze-video-audio-files-concept.md)从视频和音频文件中提取见解。

凭借 AudioAnalyzerPreset 能够从音频或视频文件中提取多个音频见解。 输出包括音频脚本的 VTT 或 TTML 文件，以及 JSON 文件（包含所有附加音频见解）。 音频见解包括关键字、说话人索引和语音情绪分析。 AudioAnalyzerPreset 还支持特定语言的语言检测。 有关详细信息，请参阅[转换](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)。

### <a name="get-started"></a>入门

若要开始操作，请参阅：

* [教程](../latest/analyze-videos-tutorial.md)
* AudioAnalyzerPreset 示例：[Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) 或 [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset 示例：[Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) 或 [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>认知服务语音服务入门

[Azure 认知服务](../../cognitive-services/index.yml)提供语音转文本服务，可将音频流实时听录为应用程序、工具或设备可以使用或显示的文本。 你可以使用语音转文本[自定义自己的声音模型、语言模型或发音模型](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)。 有关详细信息，请参阅 [Azure 认知服务语音转文本](../../cognitive-services/speech-service/speech-to-text.md)。 

> [!NOTE] 
> 语音转文本服务不采用视频文件格式，而只采用[特定的音频格式](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats)。 

有关文本转语音服务及其用法的详细信息，请参阅[什么是语音转文本？](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>与弃用的服务之间的已知差异

你会发现，与已停用的 Azure Media Indexer 1 和 Azure Media Indexer 2 处理器相比，视频分析器媒体版、Azure 媒体服务 v3 音频分析器预设和认知服务语音服务更加可靠，且可生成更好的输出质量。  

一些已知的差异包括：

* 认知服务语音服务不支持关键字提取。 不过，视频分析器媒体版和媒体服务 v3 音频分析器预设都提供一组 JSON 文件格式的更可靠关键字。

## <a name="support"></a>支持

可以通过导航到[新建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来开具支持票证

## <a name="next-steps"></a>后续步骤

* [旧组件](legacy-components.md)
* [定价页面](https://azure.microsoft.com/pricing/details/media-services/#encoding)
