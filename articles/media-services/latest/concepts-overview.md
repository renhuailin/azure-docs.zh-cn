---
title: 媒体服务的术语和概念
description: 了解 Azure 媒体服务的术语和概念。
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 876e4c33f2f2b848b8155ef0be1650d75e1e9f49
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324671"
---
# <a name="media-services-terminology-and-concepts"></a>媒体服务的术语和概念

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本主题简要概述 Azure 媒体服务的术语和概念。 本文还会提供深入介绍媒体服务 v3 概念和功能的文章的链接。

在开始开发之前，应该复习这些主题中所述的基本概念。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>媒体服务 v3 术语

|术语|说明|
|---|---|
|实时事件|**实时事件** 表示用于引入、转码（可选）以及打包视频、音频和实时元数据的管道。<br/><br/>对于从媒体服务 v2 API 迁移的客户，**实时事件** 取代了 v2 中的 **频道** 实体。 有关详细信息，请参阅[从 v2 迁移到 v3](migrate-v-2-v-3-migration-introduction.md)。|
|流式处理终结点/打包/源|流式处理终结点表示动态（即时）打包和源服务，该服务可直接将实时和按需内容发送到客户端播放器应用程序。 它使用常见流式处理媒体协议之一（HLS 或 DASH）。 此外，流式处理终结点还为行业领先的数字版权管理系统 (DRMs) 提供动态（实时）加密。<br/><br/>在媒体流行业，此服务通常称为 **打包器** 或 **来源**。  本行业对此功能使用的其他常见术语包括 JITP（实时打包器）或 JITE（实时加密）。

## <a name="media-services-v3-concepts"></a>媒体服务 v3 概念

|概念|说明|链接|
|---|---|---|
|资产和上传内容|若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建一个媒体服务帐户，并将数字文件上传到 **资产** 中。|[云上传和存储](storage-account-concept.md)<br/><br/>[资产的概念](assets-concept.md)|
|对内容进行编码|将优质数字媒体文件上传到资产中后，可将其编码为可在各种浏览器和设备上播放的格式。 <br/><br/>若要使用媒体服务 v3 进行编码，需要创建 **转换** 和 **作业**。|[转换和作业](transform-jobs-concept.md)<br/><br/>[使用媒体服务进行编码](encode-concept.md)|
|分析内容（视频分析器媒体版）|在媒体服务 v3 中，可以使用媒体服务 v3 预设从视频和音频文件中提取见解。 若要使用媒体服务 v3 预设来分析内容，需要创建 **转换** 和 **作业**。<br/><br/>如果需要更详细的见解，请直接使用[视频分析器媒体版](../../azure-video-analyzer/video-analyzer-for-media-docs/index.yml)。|[分析视频和音频文件](analyze-video-audio-files-concept.md)|
|打包和交付|将内容编码后，可以利用 **动态打包**。 在媒体服务中，**流式处理终结点** 是用于将媒体内容传送到客户端播放器的动态打包服务。 若要使输出资产中的视频可供客户端进行播放，必须创建 **流定位符**，然后生成流 URL。 <br/><br/>创建流式处理定位符时，除了资产名称之外，还需要指定流式处理策略。 使用 **流策略** 可为 **流定位符** 定义流式处理协议和加密选项（如果有）。 无论流式传输的是直播内容还是点播内容，都要使用动态打包。 <br/><br/>可以使用媒体服务 **动态清单** 来仅流式传输视频的特定再现内容或子剪辑。 此外，如果你有预编码的内容，或已由第三方编码器进行编码的内容，则可以使用 AMS 源服务流式处理内容。 有关使用预编码的源文件的示例，请参阅示例：[流式处理现有 Mp4](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4) |[动态打包](encode-dynamic-packaging-concept.md)<br/><br/>[流式处理终结点](stream-streaming-endpoint-concept.md)<br/><br/>[流式处理定位符](stream-streaming-locators-concept.md)<br/><br/>[流式处理策略](stream-streaming-policy-concept.md)<br/><br/>[动态清单](filters-dynamic-manifest-concept.md)<br/><br/>[筛选器](filters-concept.md)|
|内容保护|借助媒体服务，可以传送使用高级加密标准 (AES-128) 或/和三个主要 DRM 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 <br/><br/>若要针对流指定加密选项，请创建 **内容密钥策略** 并将其与 **流定位符** 相关联。 使用 **内容密钥策略**，可以配置如何将内容密钥传送到终端客户端。<br/><br/> 在需要相同选项的情况下尝试重复使用策略。| [内容密钥策略](drm-content-key-policy-concept.md)<br/><br/>[内容保护](drm-content-protection-concept.md)|
|实时传送视频流|使用媒体服务可将直播活动传送到 Azure 云中的客户。 **直播活动** 负责引入和处理实时视频源。 创建 **实时事件** 时，会创建一个输入终结点，可以使用它来从远程编码器发送实时信号。 将流传输到 **实时事件** 后，可以通过创建 **资产**、**实时输出** 和 **流定位符** 来启动流事件。 **实时输出** 会将流存档到 **资产** 中，使观看者可通过 **流式处理终结点** 使用该流。 直播活动可以设置为“直通”（本地实时编码器发送多比特率流）或“实时编码”（本地实时编码器发送单比特率流）。 |[实时传送视频流概述](stream-live-streaming-concept.md)<br/><br/>[直播活动和实时输出](live-event-outputs-concept.md)|
|通过事件网格进行监视|要查看作业的进度，请使用“事件网格”。 媒体服务也会发出实时事件类型。 使用事件网格，应用可以侦听和响应来自几乎所有 Azure 服务和自定义源的事件。 |[处理事件网格事件](monitoring/reacting-to-media-services-events.md)<br/><br/>[架构](monitoring/media-services-event-schemas.md)|
|使用 Azure Monitor 进行监视|可以使用 Azure Monitor 监视指标和诊断日志，以便了解应用的执行情况。|[指标和诊断日志](monitoring/monitor-media-services-data-reference.md)<br/><br/>[诊断日志架构](monitoring/monitor-media-services-data-reference.md)|
|播放器客户端|可以使用任何支持 HLS 或 DASH 流式处理协议的播放器框架。 市场提供了许多开放源代码和商用播放器（Shaka、Hls.js、Video.js、Theo Player、Bitmovin Player 等），以及适用于 HLS 和 DASH 的内置本机浏览器和 OS 级别流式处理支持。  还可在各种浏览器上使用 Azure Media Player 播放媒体服务流式处理的媒体内容。 Azure Media Player 使用行业标准（如 HTML5、媒体源扩展 (MSE) 和加密媒体扩展插件 (EME)）来提供自适应流式处理体验。 |[Azure Media Player 概述](player-use-azure-media-player-how-to.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [对远程文件和流视频进行编码 - REST](stream-files-tutorial-with-rest.md)
* [对上传的文件和流视频进行编码 - .NET](stream-files-tutorial-with-api.md)
* [实时流 - .NET](stream-live-tutorial-with-api.md)
* [分析视频 - .NET](analyze-videos-tutorial.md)
* [AES-128 动态加密 - .NET](drm-playready-license-template-concept.md)
* [通过多重 DRM 进行动态加密 - .NET](drm-protect-with-drm-tutorial.md)
