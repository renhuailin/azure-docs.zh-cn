---
title: 媒体服务 v3 示例
description: 本文包含可用于媒体服务 v3 的所有示例的列表，这些示例按方法和 SDK 进行组织。  示例包括 .NET、Node.JS、Python 和 Java 以及带 Postman 的 REST。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 6dd82d635199e4aa47a07e09b3c4649dbc4ef7cd
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "122643111"
---
# <a name="media-services-v3-samples"></a>媒体服务 v3 示例

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文包含可用于媒体服务的所有示例的列表，这些示例按方法和 SDK 进行组织。  示例包括 .NET、Node.JS (Typescript)、Python 和 Java 以及带 Postman 的 REST。

## <a name="samples-by-sdk"></a>按 SDK 归类的示例

你可以在每个选项卡上找到所要的示例的说明和链接。

## <a name="net"></a>[.NET](#tab/net/)

| 示例 | 说明 |
|-------------|-------------|
| [Account/CreateAccount](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Account/CreateAccount)|该示例演示了如何创建媒体服务帐户和设置主存储帐户，以及高级配置设置，包括密钥传递 IP 允许列表、托管标识、存储身份验证和创建自己的加密密钥。|
| [VideoEncoding/Encoding_PredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_PredefinedPreset)|该示例演示了如何使用内置预设和 HTTP URL 输入来提交作业，发布输出资产以进行流式处理，以及下载结果进行验证。|
| [VideoEncoding/Encoding_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264)|该示例演示了如何使用自定义 H.264 编码预设和 HTTP URL 输入来提交作业，发布输出资产以进行流式处理，以及下载结果进行验证。|
| [VideoEncoding/Encoding_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)|该示例演示了如何使用自定义 HEVC 编码预设和 HTTP URL 输入来提交作业，发布输出资产以进行流式处理，以及下载结果进行验证。|
| [VideoEncoding/Encoding_StitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets)|该示例演示了如何使用 JobInputSequence 提交作业，以将两个或更多个可按开始或结束时间剪辑的资产拼接在一起。 生成的编码文件是单个视频，其所有资产拼接在一起。  示例还将发布输出资产以进行流式传输，以及下载结果进行验证。|
| [VideoEncoding/Encoding_SpriteThumbnail](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_SpriteThumbnail)|该示例演示了如何使用带缩略图子画面的自定义预设和 HTTP URL 输入来提交作业，如何发布输出资产以进行流式处理，以及如何下载结果进行验证。|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|该示例首先演示了如何创建包含最多 25 小时完整存档的 LiveEvent，以及在资产上创建具有 5 分钟 DVR 窗口的筛选器，然后演示了如何使用该筛选器创建用于流式传输的定位符。|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|该示例演示了如何创建视频分析器转换，如何将视频文件上传到输入资产，如何使用转换提交作业，以及如何下载结果进行验证。|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|该示例演示了如何创建音频分析器转换，将媒体文件上传到输入资产，使用转换提交作业，以及下载结果进行验证。|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|该示例演示了如何使用内置的 AdaptiveStreaming 预设创建转换，如何提交作业，如何使用密钥创建 ContentKeyPolicy，如何将 ContentKeyPolicy 与 StreamingLocator 相关联，如何获取令牌，以及如何打印用于在 Azure Media Player 中进行播放的 URL。 当播放器请求某个流时，媒体服务会使用指定的密钥通过 AES-128 动态加密你的内容，Azure Media Player 则使用令牌进行解密。|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|该示例演示了如何使用内置的 AdaptiveStreaming 预设创建转换，如何提交作业，如何使用密钥创建具有 Widevine 配置的 ContentKeyPolicy，如何将 ContentKeyPolicy 与 StreamingLocator 相关联，如何获取令牌，以及如何打印用于在 Widevine Player 中进行播放的 URL。 当用户请求受 Widevine 保护的内容时，播放器应用程序会从媒体服务许可证服务请求许可证。 如果播放器应用程序获得授权，媒体服务许可证服务会向该播放器颁发许可证。 Widevine 许可证包含客户端播放器用来对内容进行解密和流式传输的解密密钥。|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|该示例演示了如何使用内置的 AdaptiveStreaming 预设创建转换，如何提交作业，如何使用密钥创建具有 PlayReady 配置的 ContentKeyPolicy，如何将 ContentKeyPolicy 与 StreamingLocator 相关联，如何获取令牌，以及如何打印用于在 Azure Media Player 中进行播放的 URL。 当用户请求受 PlayReady 保护的内容时，播放器应用程序会从媒体服务许可证服务请求许可证。 如果播放器应用程序获得授权，媒体服务许可证服务会向该播放器颁发许可证。 PlayReady 许可证包含客户端播放器用来对内容进行解密和流式传输的解密密钥。|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|该示例演示了如何使用 PlayReady 和 Widevine DRM 来动态加密内容，并在不从许可证服务请求许可证的情况下播放内容。 它展示了如何使用内置的 AdaptiveStreaming 预设创建转换，如何提交作业，创建具有开放式限制和 PlayReady/Widevine 永久性配置的 ContentKeyPolicy，将 ContentKeyPolicy 与 StreamingLocator 相关联，以及输出用于播放的 URL。|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|该示例演示了如何使用内置的 AdaptiveStreaming 预设创建转换，如何提交作业，如何创建资产筛选器和帐户筛选器，如何将筛选器关联到流式传输定位符，以及如何打印用于播放的 URL。|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|该示例演示了如何使用内置的 AdaptiveStreaming 预设创建转换，如何提交作业，如何发布输出资产以进行 HLS 和 DASH 流式传输。|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | 该示例提供了针对使用按需编码或分析的生产系统的指南和最佳做法。 读者应该从[媒体服务和 VOD 的高可用性](architecture-high-availability-encoding-concept.md)这一配套文章开始。 为 [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md) 示例提供了一个单独的解决方案文件。 |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|示例|说明|
|---|---|
|[Hello World - 列出资产](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/HelloWorld-ListAssets/list-assets.ts)|有关连接和列出资产的基本示例 |
|[实时传送视频流](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live/index.ts)| 基本实时传送视频流示例。 警告：在使用实时传送视频流时，请确保检查是否已清理所有资源，并且不再在门户中计费|
|[上传和流式传输 HLS 和 DASH](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample/index.ts)| 上传本地文件或从源 URL 编码的基本示例。 示例演示如何使用存储 SDK 下载内容，并演示如何流式传输到播放机 |
|[使用 Playready 和 Widevine DRM 上传和流式传输 HLS 和 DASH](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample/index.ts)| 演示如何使用 Widevine 和 PlayReady DRM 进行编码和流式传输 |
|[上传并使用 AI 为视频和音频编制索引](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample/index.ts)| 使用视频和音频分析器预设生成视频或音频文件中的元数据和见解的示例 |

## <a name="python"></a>[Python](#tab/python)

|示例|说明|
|---|---|
|[使用 Python 进行基本编码](https://github.com/Azure-Samples/media-services-v3-python)| 上传本地文件或从源 URL 编码的基本示例。 示例演示如何使用存储 SDK 下载内容，并演示如何流式传输到播放机 |
|[使用事件和函数进行人脸编修](https://github.com/Azure-Samples/media-services-v3-python/tree/main/VideoAnalytics/FaceRedactorEventBased)| 这是一个基于事件的方法的示例，它在视频到达 Azure 存储帐户后立即触发 Azure 媒体服务人脸编修作业。 它为解决方案利用 Azure 媒体服务、Azure 函数、事件网格和 Azure 存储。 有关解决方案的完整说明，请参阅 [README.md](https://github.com/Azure-Samples/media-services-v3-python/blob/main/VideoAnalytics/FaceRedactorEventBased/README.md)|


## <a name="java"></a>[Java](#tab/java)

|示例|说明|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|如何分析媒体文件中的音频。 |
|内容保护|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|如何使用 AES-128 动态加密你的内容。|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|如何使用 PlayReady DRM 动态加密你的内容。|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|如何使用 Widevine DRM 动态加密你的内容。|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|如何使用 FairPlay DRM 动态加密你的内容，并在不从许可证服务请求许可证的情况下播放内容。|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|如何使用 PlayReady 和 Widevine DRM 来动态加密内容，并在不从许可证服务请求许可证的情况下播放内容。|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|如何使用资产和帐户筛选器筛选内容。|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|如何动态将 VOD 内容打包为 HLS/DASH 以进行流式传输。|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|如何创建一个 LiveEvent，使其具有长达 25 小时的完整存档和一个在资产上的 DVR 时长为 5 分钟的筛选器；如何创建用于流式传输的定位符并使用筛选器。|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|如何创建一个 LiveEvent，使其具有长达 25 小时的完整存档和一个在资产上的 DVR 时长为 5 分钟的筛选器；如何创建用于流式传输的定位符并使用筛选器。|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|如何使用 StandardEncoderPreset 设置创建自定义编码转换。|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|如何使用内置预设和 HTTP URL 输入来提交作业，发布输出资产以进行流式处理，以及下载结果进行验证。|

---

## <a name="rest-postman-collection"></a>REST Postman 集合

[REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) 示例包括一个 Postman 环境和集合，供你导入到 Postman 客户端中。 建议使用 Postman 集合示例，以熟悉 API 结构及其如何与 Azure 资源管理 (ARM) 以及客户端 SDK 中的调用结构一起工作。 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]
