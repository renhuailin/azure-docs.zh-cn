---
title: Azure 媒体服务 v3 发行说明
description: 为了让大家随时了解最新的开发成果，本文提供了 Azure 媒体服务 v3 的最新更新。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.custom: references_regions
ms.topic: article
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 3258baa30d689513ae09ea727ac1db603f8bf5fe
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720292"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure 媒体服务 v3 发行说明

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能


## <a name="june-2021"></a>2021 年 6 月

### <a name="additional-live-event-ingest-heartbeat-properties-for-improved-diagnostics"></a>用于改进诊断的其他实时事件引入检测信号属性

其他实时事件引入检测信号属性已添加到事件网格消息。 其中包括以下新字段，用于帮助诊断实时引入过程中的问题。  如果需要监视将内容从源引入编码器推送到实时事件过程中出现的网络延迟，可以使用 ingestDriftValue。 如果此值偏移过大，则表明网络延迟过高，实时传送视频流事件无法成功。

有关更多详细信息，请参阅 [LiveEventIngestHeartbeat 架构](./monitoring/media-services-event-schemas.md#liveeventingestheartbeat)。

### <a name="private-links-support-is-now-ga"></a>专用链接支持现已正式发布

对通过[专用链接](../../private-link/index.yml)使用媒体服务的支持现已正式发布，在所有 Azure 区域（包括 Azure 政府云）都提供这一项支持。
利用 Azure 专用链接，可实现通过虚拟网络中的专用终结点访问 Azure PaaS 服务和 Azure 托管的归客户所有的/合作伙伴服务。
虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。

若要详细了解如何通过专用链接使用媒体服务，请参阅[使用专用链接创建媒体服务和存储帐户](./security-private-link-how-to.md)

### <a name="new-us-west-3-region-is-ga"></a>新的美国西部 3 区域正式发布

美国西部 3 区域现已正式发布，可供客户在创建新的媒体服务帐户时使用。

### <a name="key-delivery-supports-ip-allowlist-restrictions"></a>密钥传递支持 IP 允许列表限制

媒体服务帐户现在可以配置对密钥传递的 IP 允许列表限制。 通过 SDK 以及门户和 CLI 可在媒体服务帐户资源上获得新的允许列表设置。
这允许操作员将 DRM 许可证和 AES-128 内容密钥的传递限制在特定的 IPv4 范围内。

此功能还可用于关闭 DRM 许可证或 AES-128 密钥的所有公共 Internet 传递，并将传递限制在专用网络终结点。

有关详细信息，请参阅文章[使用 IP 允许列表限制对 DRM 许可证和 AES 密钥传递的访问](./drm-content-protection-key-delivery-ip-allow.md)。

### <a name="new-samples-for-python-and-nodejs-with-typescript"></a>Python 和 Node.js 的新示例（使用 Typescript）
Node.js 的更新示例，使用 Azure SDK 中最新的 Typescript 支持。

|示例|说明|
|---|---|
|[实时传送视频流](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live/index.ts)| 基本实时传送视频流示例。 警告：在使用实时传送视频流时，请确保检查是否已清理所有资源，并且不再在门户中计费|
|[上传和流式传输 HLS 和 DASH](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample/index.ts)| 上传本地文件或从源 URL 编码的基本示例。 示例演示如何使用存储 SDK 下载内容，并演示如何流式传输到播放机 |
|[使用 Playready 和 Widevine DRM 上传和流式传输 HLS 和 DASH](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample/index.ts)| 演示如何使用 Widevine 和 PlayReady DRM 进行编码和流式传输 |
|[上传并使用 AI 为视频和音频编制索引](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample/index.ts)| 使用视频和音频分析器预设生成视频或音频文件中的元数据和见解的示例 |


新的 Python 示例演示如何使用 Azure Functions 和事件网格来触发人脸编修预设。

|示例|说明|
|---|---|
|[使用事件和函数进行人脸编修](https://github.com/Azure-Samples/media-services-v3-python/tree/main/VideoAnalytics/FaceRedactorEventBased) | 这是一个基于事件的方法的示例，它在视频到达 Azure 存储帐户后立即触发 Azure 媒体服务人脸编修作业。 它为解决方案利用 Azure 媒体服务、Azure 函数、事件网格和 Azure 存储。 有关解决方案的完整说明，请参阅 [README.md](https://github.com/Azure-Samples/media-services-v3-python/blob/main/VideoAnalytics/FaceRedactorEventBased/README.md) |


## <a name="may-2021"></a>2021 年 5 月

### <a name="availability-zones-default-support-in-media-services"></a>媒体服务中的可用性区域默认支持

媒体服务现在支持[可用性区域](concept-availability-zones.md)，在同一 Azure 区域内提供故障隔离位置。  媒体服务帐户现在默认为区域冗余，无需其他配置或设置。 请注意，这仅适用于具有[可用性区域支持](../../availability-zones/az-region.md#azure-regions-with-availability-zones)的区域


## <a name="march-2021"></a>2021 年 3 月

### <a name="new-language-support-added-to-the-audioanalyzer-preset"></a>为 AudioAnalyzer 预设添加了新语言支持

现在，AudioAnalyzer 预设（基本和标准模式）中为视频听录和字幕提供了更多语言。

* 英语（澳大利亚），'en-AU'
* 法语（加拿大），'fr-CA'
* 现代标准阿拉伯语（巴林），'ar-BH'
* 阿拉伯语（埃及），'ar-EG'
* 阿拉伯语（伊拉克），'ar-IQ'
* 阿拉伯语（以色列），'ar-IL'
* 阿拉伯语（约旦），'ar-JO'
* 阿拉伯语（科威特），'ar-KW'
* 阿拉伯语（巴嫩），ar-LB'
* 阿拉伯语（阿曼），'ar-OM'
* 阿拉伯语（卡塔尔），'ar-QA'
* 阿拉伯语（沙特阿拉伯），'ar-SA'
* 丹麦语，‘da-DK’
* 挪威语，'nb-NO'
* 瑞典语，‘sv-SE’
* 芬兰语，‘fi-FI’
* 泰语，‘th-TH’
* 土耳其语，‘tr-TR’

请参阅[分析视频和音频文件概念文章](analyze-video-audio-files-concept.md)中的最新可用语言。

## <a name="february-2021"></a>2021 年 2 月

### <a name="hevc-encoding-support-in-standard-encoder"></a>标准编码器中的 HEVC 编码支持

标准编码器现在支持 8 位 HEVC (H.265) 编码。 可以通过动态打包器使用“hev1”格式传送和打包 HEVC 内容。  

[media-services-v3-dotnet GitHub 存储库](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)中提供了有关使用 HEVC 实现 .NET 自定义编码的新示例。
除了自定义编码以外，现在还可以使用以下新的内置 HEVC 编码预设：

- H265ContentAwareEncoding
- H265AdaptiveStreaming
- H265SingleBitrate720P
- H265SingleBitrate1080p
- H265SingleBitrate4K

过去通过 v2 API 在高级编码器中使用 HEVC 的客户应该迁移，以便在标准编码器中使用新的 HEVC 编码支持。

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Azure 媒体服务 v2 API 和 SDK 弃用公告

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>请在 2024 年 2 月 29 日之前将 Azure 媒体服务 REST API 和 SDK 更新到 v3

由于版本 3 的 Azure 媒体服务 REST API 和适用于 .NET 和 Java 的客户端 SDK 提供了比版本 2 更多的功能，因此我们正在停用版本 2 的 Azure 媒体服务 REST API 和适用于 .NET 和 Java 的客户端 SDK。

我们鼓励你尽快切换，以获得版本 3 的 Azure 媒体服务 REST API 和适用于 .NET 和 Java 的客户端 SDK 的更多优势。
版本 3 提供：
 
- 全天候实时事件支持
- ARM REST API、适用于 .NET core、Node.js、Python、Java、Go 和 Ruby 的客户端 SDK。
- 客户管理的密钥、受信任的存储集成和专用链接支持[等等](./migrate-v-2-v-3-migration-benefits.md)

#### <a name="action-required"></a>所需的操作

若要最大程度地减少工作负载的中断，请参阅[迁移指南](./migrate-v-2-v-3-migration-introduction.md)，在 2024 年 2 月 29 日之前将代码从版本 2 API 和 SDK 转换为版本 3 API 和 SDK。
**2024 年 2 月 29 日之后**，Azure 媒体服务将不再接受版本 2 REST API、ARM 帐户管理 API 版本 2015-10-01 或版本 2 .NET 客户端 SDK 中的流量。 其中包括可以调用版本 2 API 的任何第三方开源客户端 SDK。  

请参阅官方的 [Azure 更新公告](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/)。

### <a name="standard-encoder-support-for-v2-api-features"></a>对 v2 API 功能的标准编码器支持

除新增了对 HEVC (H.265) 编码的支持以外，现在 2020-05-01 版本的编码 API 中还提供了以下功能。

- 现在支持使用新的 **JobInputClip** 支持来拼结多个输入文件。
    - 我们已提供一个适用于 .NET 的示例来演示如何[将两个资产拼结到一起](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets)。
- 音频轨道选择可让客户选择并映射传入的音频轨道，并将其路由到输出以进行编码
    - 有关 **AudioTrackDescriptor** 和轨道选择的详细信息，请参阅 [REST API OpenAPI](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L385)
- 选择轨道进行编码 – 可让客户从包含多比特率轨道的 ABR 源文件或实时存档中选择轨道。 此功能对于从实时事件存档文件生成 MP4 极其有用。
    - 请参阅 [VideoTrackDescriptor](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L1562)
- 在 FaceDetector 中添加了编修（模糊处理）功能
    - 请参阅 FaceDetector 预设的[编修](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L634)和[组合](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L649)模式

### <a name="new-client-sdk-releases-for-2020-05-01-version-of-the-azure-media-services-api"></a>为 API 2020-05-01 版本的 Azure 媒体服务 API 发布了新的客户端 SDK

适用于所有可用语言的新客户端 SDK 版本已随上述功能一起提供。
请使用包管理器在代码库中更新到最新的客户端 SDK。

- [.NET SDK 包 3.0.4](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/)
- [Node.js Typescript 版本 8.1.0](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Python azure-mgmt-media 3.1.0](https://pypi.org/project/azure-mgmt-media/)
- [Java SDK 1.0.0-beta.2](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-mediaservices/1.0.0-beta.2/jar)

### <a name="new-security-features-available-in-the-2020-05-01-version-of-the-azure-media-services-api"></a>在 2020-05-01 版本的 Azure 媒体服务 API 中提供了新的安全功能

- **[客户管理的密钥](concept-use-customer-managed-keys-byok.md)** ：使用“2020-05-01”版 API 创建的帐户中存储的内容密钥和其他数据将通过帐户密钥进行加密。 客户可以提供一个密钥来加密帐户密钥。

- **[受信任的存储](concept-trusted-storage.md)** ：可以使用与媒体服务帐户关联的托管标识将媒体服务配置为访问 Azure 存储。 使用托管标识访问存储帐户时，客户可以在不防碍媒体服务方案的情况下，针对存储帐户配置限制性更高的网络 ACL。

- **[托管标识](concept-managed-identities.md)** ：客户可为媒体服务帐户启用系统分配的托管标识，以提供对密钥保管库（适用于客户管理的密钥）和存储帐户（适用于受信任的存储）的访问。

### <a name="updated-typescript-nodejs-samples-using-isomorphic-sdk-for-javascript"></a>使用同构 SDK for JavaScript 更新了 Typescript Node.js 示例

已将 Node.js 示例更新为使用最新的同构 SDK。 这些示例现在会演示 Typescript 的用法。 此外，添加了适用于 Node.js/Typescript 的新实时传送流示例。

请参阅 **[media-services-v3-node-tutorials](https://github.com/Azure-Samples/media-services-v3-node-tutorials)** GitHub 存储库中的最新示例。

### <a name="new-live-stand-by-mode-to-support-faster-startup-from-warm-state"></a>添加了新的实时待机模式以支持从暖状态更快启动

实时事件现在支持对“待机”采用成本更低的计费模式。 这样，客户便能够以更低的成本预分配实时事件，从而创建“热池”。 然后，客户可以使用待机实时事件转换到“正在运行”状态，而且转换速度比创建后从冷状态启动更快。  这可以大大缩短启动通道的时间，并可对以更低价的模式运行的计算机进行快速热池分配。
请参阅[此处](https://azure.microsoft.com/pricing/details/media-services)的最新定价详细信息。
有关待机状态和其他实时事件状态的详细信息，请参阅文章 - [实时事件状态和计费](./live-event-states-billing-concept.md)。

## <a name="december-2020"></a>2020 年 12 月

### <a name="regional-availability"></a>区域可用性

Azure 媒体服务现已在 Azure 门户中的“挪威东部”区域提供。  restV2 未在此区域推出。

## <a name="october-2020"></a>2020 年 10 月

### <a name="basic-audio-analysis"></a>基本音频分析

音频分析预设现在包含基本模式定价层。 新的基本音频分析器模式提供了一个低成本的选项，用于提取语音口述文本并设置输出隐藏式字幕和字幕的格式。 此模式执行语音转文本听录并生成 VTT 字幕文件。 此模式的输出包括一个见解 JSON 文件，该文件仅包含关键字、听录和计时信息。 此模式不包括自动语言检测和说话人分割聚类。 请参阅[支持的语言](analyze-video-audio-files-concept.md#built-in-presets)的列表。

使用索引器 v1 和索引器 v2 的客户应迁移到“基本音频分析”预设。

有关基本音频分析器模式的详细信息，请参阅[分析视频和音频文件](analyze-video-audio-files-concept.md)。  若要了解如何在 REST API 中使用基本音频分析器模式，请参阅[如何创建基本音频转换](transform-create-basic-audio-how-to.md)。

### <a name="live-events"></a>直播活动

现在允许实时事件停止时对大多数属性进行更新。 此外，允许用户为实时事件的输入和预览 URL 指定静态主机名的前缀。 VanityUrl 现在称为 `useStaticHostName`，以更好地反映属性的意向。

实时事件现在具有等待状态。  请参阅[媒体服务中的实时事件和实时输出](./live-event-outputs-concept.md)。

实时事件支持接收各种输入纵横比。 通过拉伸模式，客户可以指定输出的拉伸行为。

实时编码现在增加了在 0.5 到 20 秒之间输出固定关键帧间隔片段的功能。

### <a name="accounts"></a>帐户

> [!WARNING]
> 如果使用 2020-05-01 API 版本创建媒体服务帐户，该帐户将不适用于 RESTv2 

## <a name="august-2020"></a>2020 年 8 月

### <a name="dynamic-encryption"></a>动态加密

动态打包程序中现在提供了对旧版 PlayReady 受保护互操作文件格式 (PIFF 1.1) 加密的支持。 这为 Samsung 和 LG 的传统智能电视机提供了支持，这些支持实现了 Microsoft 发布的通用加密标准 (CENC) 的早期草稿。  PIFF 1.1 格式也是 Silverlight 客户端库以前支持的加密格式。 如今，这种加密格式的唯一用例场景是针对传统的智能电视市场，对于该市场，在某些仅支持使用 PIFF 1.1 加密的平滑流式处理的区域，仍然存在数量不少的智能电视。

若要使用新的 PIFF 1.1 加密支持，请将加密值更改为流式处理定位符的 URL 路径中的“piff”。 有关详细信息，请参阅[内容保护概述](drm-content-protection-concept.md)。
例如 `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1.1 支持作为智能电视（Samsung、LG）的后向兼容解决方案提供，实现了通用加密的早期“Silverlight”版本。 建议仅在需要时使用 PIFF 格式：用于支持在 2009-2015 年间发布的、可支持 PIFF 1.1 版本的 PlayReady 加密的旧版 Samsung 或 LG 智能电视。 

## <a name="july-2020"></a>2020 年 7 月

### <a name="live-transcriptions"></a>实时听录

实时听录现在支持 19 种语言和 8 个区域。

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>使用媒体服务和 Azure AD 保护内容

我们发布了名为[使用 Azure AD 进行端到端内容保护](./architecture-azure-ad-content-protection.md)的教程。

### <a name="high-availability"></a>高可用性

我们发布了一个高可用性的媒体服务和视频点播 (VOD) [概述](./architecture-high-availability-encoding-concept.md)和[示例](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)。

## <a name="june-2020"></a>2020 年 6 月

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>IoT Edge 预览版上的实时视频分析

IoT Edge 上的实时视频分析预览版现已公开发行。 有关详细信息，请参阅[发行说明](../live-video-analytics-edge/release-notes.md)。

IoT Edge 上的实时视频分析是媒体服务系列的扩展。 通过它，你能够在自己的边缘设备上使用所选择的 AI 模型分析实时视频，并可以选择捕获和录制该视频。 现在，你可以在边缘构建具有实时视频分析的应用，而不必担心构建和操作实时视频管道的复杂性。

## <a name="may-2020"></a>2020 年 5 月

Azure 媒体服务现已在以下区域推出正式版：“德国北部”、“德国中西部”、“瑞士北部”和“瑞士西部”。 客户可以使用 Azure 门户将媒体服务部署到这些区域。

## <a name="april-2020"></a>2020 年 4 月

### <a name="improvements-in-documentation"></a>文档改进

Azure Media Player 文档已迁移到 [Azure 文档](../azure-media-player/azure-media-player-overview.md)。

## <a name="january-2020"></a>2020 年 1 月

### <a name="improvements-in-media-processors"></a>媒体处理器中的改进

- 改进了对视频分析中隔行扫描源的支持 - 现在，在将此类内容发送到推理引擎之前，会对其正确取消隔行扫描。
- 使用“最佳”模式生成缩略图时，编码器现在会搜索超过 30 秒，以选择非单色的帧。

### <a name="azure-government-cloud-updates"></a>Azure 政府云更新

媒体服务在以下 Azure 政府区域推出了正式版：USGov 亚利桑那州和 USGov 德克萨斯州。

## <a name="december-2019"></a>2019 年 12 月

为实时和视频按需流式处理的 Origin-Assist Prefetch 标头添加了 CDN 支持；适用于与 Akamai CDN 直接签订合同的客户。 Origin-Assist CDN-Prefetch 功能涉及 Akamai CDN 与 Azure 媒体服务源之间的以下 HTTP 标头交换：

|HTTP 标头|值|发送方|接收方|目的|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1（默认值）或 0 |CDN|源|指示 CDN 已启用预提取|
|CDN-Origin-Assist-Prefetch-Path| 示例： <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|源|CDN|提供 CDN 的预提取路径|
|CDN-Origin-Assist-Prefetch-Request|1（预提取请求）或 0（常规请求）|CDN|源|指示来自 CDN 的请求是预提取|

若要查看标头交换部分的实际运行情况，可以尝试执行以下步骤：

1. 使用 Postman 或 curl 向媒体服务源发出对音频或视频段或片段的请求。 确保在请求中添加标头 CDN-Origin-Assist-Prefetch-Enabled:1。
2. 在响应中，应会看到标头 CDN-Origin-Assist-Prefetch-Path，其值为一个相对路径。

## <a name="november-2019"></a>2019 年 11 月

### <a name="live-transcription-preview"></a>实时听录预览版

实时听录现提供公共预览版，可在美国西部 2 区域使用。

实时听录旨在作为附加功能与实时事件结合使用。  直通和标准或高级编码实时事件均支持此功能。  启用此功能后，服务将使用认知服务的[语音转文本](../../cognitive-services/speech-service/speech-to-text.md)功能将传入音频中的口语转录为文本。 然后，可以在 MPEG-DASH 和 HLS 协议中将此文本连同视频和音频一起传输。 计费基于新的附加计量器，当实时事件处于“正在运行”状态时，它会产生额外的成本。  有关实时听录和计费的详细信息，请参阅[实时听录](live-event-live-transcription-how-to.md)

> [!NOTE]
> 目前，实时听录仅在美国西部 2 区域提供预览功能。 它目前只支持英语 (en-us) 口语的听录。

### <a name="content-protection"></a>内容保护

9 月份在有限区域发布的“令牌重放防护”功能现在已在所有区域提供。
媒体服务客户现在可以对同一令牌用于请求密钥或许可证的次数设置限制。 有关详细信息，请参阅[令牌重放防护](drm-content-protection-concept.md#token-replay-prevention)。

### <a name="new-recommended-live-encoder-partners"></a>新推荐的实时编码器合作伙伴

添加的支持针对用于 RTMP 实时流式处理的以下新推荐合作伙伴编码器：

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 和 Max 运动摄像头](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>文件编码增强功能

- 现在可以使用新的内容感知编码预设。 它使用内容感知编码生成一组符合 GOP 标准的 MP4。 不管输入内容是什么，该服务都会对输入内容执行初始的轻型分析。 它使用这些结果来确定最佳层数，以及适当的比特率和分辨率设置，方便通过自适应流式处理进行传递。 此预设特别适用于低复杂性和中复杂性的视频，其中的输出文件比特率较低，但其质量仍会为观众提供良好的体验。 输出将包含带有交错式视频和音频的 MP4 文件。 有关详细信息，请参阅[开放式 API 规范](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)。
- 改善了标准编码器中大小重设器的性能和多线程处理。 在特定条件下，客户应看到 5-40% 的 VOD 编码获得性能提升。 编码成多比特率的低复杂性内容会显示最高的性能提升。 
- 现在，在使用基于时间的 GOP 设置时，标准编码会在 VOD 编码期间针对可变帧速率 (VFR) 内容维持常规 GOP 节奏。  这意味着，如果提交的混合帧速率内容存在差异（例如 15-30 fps），客户现在会看到常规的 GOP 距离，此类距离根据自适应比特率流式处理 MP4 文件的输出进行计算。 这会提高通过 HLS 或 DASH 进行交付时在跟踪之间无缝切换的功能。 
-  改进了可变帧速率 (VFR) 源内容的 AV 同步

### <a name="azure-video-analyzer-for-media-video-analytics"></a>Azure 视频分析器媒体版、视频分析

- 使用 VideoAnalyzer 预设提取的关键帧现在采用视频的原始分辨率，而不是重设大小。 高分辨率关键帧提取可为你提供原始质量的图像，并允许你利用 Microsoft 计算机视觉和自定义视觉服务提供的基于图像的人工智能模型，从视频中获得更多见解。

## <a name="september-2019"></a>2019 年 9 月

###  <a name="media-services-v3"></a>媒体服务 v3  

#### <a name="live-linear-encoding-of-live-events"></a>直播活动的实时线性编码

媒体服务 v3 宣布推出实时事件的实时线性编码的全天候预览。

###  <a name="media-services-v2"></a>媒体服务 v2  

#### <a name="deprecation-of-media-processors"></a>弃用媒体处理器

我们宣布弃用 Azure Media Indexer 和 Azure Media Indexer 2 预览版 。 有关停用日期，请参阅[旧组件](../previous/legacy-components.md)文章。 Azure 视频分析器媒体版取代了这些旧版媒体处理器。

有关详细信息，请参阅[从 Azure Media Indexer 和 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](../previous/migrate-indexer-v1-v2.md)。

## <a name="august-2019"></a>2019 年 8 月

###  <a name="media-services-v3"></a>媒体服务 v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>南非区域对开放媒体服务 

媒体服务现已在南非北部和南非西部区域推出。

有关详细信息，请参阅[存在媒体服务 v3 的云和区域](azure-clouds-regions.md)。

###  <a name="media-services-v2"></a>媒体服务 v2  

#### <a name="deprecation-of-media-processors"></a>弃用媒体处理器

我们宣布弃用 Windows Azure 媒体编码器 (WAME) 和 Azure 媒体编码器 (AME) 媒体处理器，这两个处理器将停用。 有关停用日期，请参阅此[旧组件](../previous/legacy-components.md)文章。

有关详细信息，请参阅[将 WAME 迁移到 Media Encoder Standard](../previous/migrate-windows-azure-media-encoder.md) 和[将 AME 迁移到 Media Encoder Standard](../previous/migrate-azure-media-encoder.md)。
 
## <a name="july-2019"></a>2019 年 7 月

### <a name="content-protection"></a>内容保护

在流式处理受令牌限制保护的内容时，最终用户需要获取作为密钥传输请求的一部分发送的令牌。 令牌重放预防功能允许媒体服务客户设置一个限制，以限制可使用同一令牌请求密钥或许可证的次数。 有关详细信息，请参阅[令牌重放防护](drm-content-protection-concept.md#token-replay-prevention)。

截止到 7 月，该预览功能仅在美国中部和美国中西部提供。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-subclipping"></a>视频子剪辑

现在，在使用[作业](/rest/api/media/jobs)对视频进行编码时，可以对其进行剪裁或子剪辑。 

此功能适用于使用 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 预设或 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 预设生成的任何[转换](/rest/api/media/transforms)。 

请参阅示例：

* [使用 .NET 创建视频的子剪辑](transform-subclip-video-dotnet-how-to.md)
* [使用 REST 对视频进行子剪辑](transform-subclip-video-rest-how-to.md)

## <a name="may-2019"></a>2019 年 5 月

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>针对媒体服务诊断日志和指标的 Azure Monitor 支持

现在可以使用 Azure Monitor 查看媒体服务发出的遥测数据。

* 使用 Azure Monitor 诊断日志来监视媒体服务密钥传送终结点发送的请求。 
* 监视媒体服务[流式处理终结点](stream-streaming-endpoint-concept.md)发出的指标。   

有关详细信息，请参阅[监视媒体服务指标和诊断日志](monitoring/monitor-media-services-data-reference.md)。

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>动态打包中的多音频曲目支持 

现在，对包含使用多个编解码器和语言的多音频曲目的资产进行流式处理时，[动态打包](encode-dynamic-packaging-concept.md)支持 HLS 输出（版本 4 或更高版本）的多音频曲目。

### <a name="korea-regional-pair-is-open-for-media-services"></a>韩国区域对开放媒体服务 

媒体服务现已在韩国中部和韩国南部区域推出。 

有关详细信息，请参阅[存在媒体服务 v3 的云和区域](azure-clouds-regions.md)。

### <a name="performance-improvements"></a>性能改进

添加了包括媒体服务性能改进的更新。

* 更新了支持处理的最大文件大小。 请参阅[配额和限制](limits-quotas-constraints-reference.md)。
* [编码速度改进](concept-media-reserved-units.md)。

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-presets"></a>新增预设

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) 已添加到内置分析器预设中。
* 向内置编码器预设添加了 [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset)。 有关详细信息，请参阅[内容感知型编码](encode-content-aware-concept.md)。 

## <a name="march-2019"></a>2019 年 3 月

动态打包现在支持 Dolby Atmos。 有关详细信息，请参阅[动态打包支持的音频编解码器](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging)。

现在，可以指定资产或帐户筛选器的列表，这些筛选器将应用于流定位器。 有关详细信息，请参阅[将筛选器与流定位器相关联](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="february-2019"></a>2019 年 2 月

媒体服务 v3 目前在 Azure 国家云中受支持。 目前，并非所有功能在所有云中都可用。 有关详细信息，请参阅[存在 Azure 媒体服务 v3 的云和区域](azure-clouds-regions.md)。

[Microsoft.Media.JobOutputProgress](monitoring/media-services-event-schemas.md#monitoring-job-output-progress) 事件已添加到媒体服务的 Azure 事件网格架构中。

## <a name="january-2019"></a>2019 年 1 月

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard 和 MPI 文件 

使用 Media Encoder Standard 编码生成 MP4 文件时，新的 .mpi 文件会生成并添加到输出资产中。 此 MPI 文件旨在提高[动态打包](encode-dynamic-packaging-concept.md)和流式处理方案的性能。

不应修改或删除该 MPI 文件，也不应在存在（或不存在）此类文件的情况下采用服务中的任何依赖项。

## <a name="december-2018"></a>2018 年 12 月

V3 API 的正式版中的更新包括：
       
* **PresentationTimeRange** 属性不再是 **AssetFilters** 和 **AccountFilters** 所必需的。 
* 已删除 **Jobs** 和 **Transforms** 的 $top 和 $skip 查询选项，并添加了 $orderby。 在添加新排序功能的过程中，我们发现之前意外地公开了 $top 和 $skip 选项，尽管它们并未实现。
* 重新启用了枚举可扩展性。 此功能在 SDK 的预览版中已启用，但在正式版中被意外禁用。
* 已重命名两个预定义的流式处理策略。 **SecureStreaming** 现在名为 **MultiDrmCencStreaming**。 **SecureStreamingWithFairPlay** 现在名为 **Predefined_MultiDrmStreaming**。

## <a name="november-2018"></a>2018 年 11 月

CLI 2.0 模块现在可用于 [Azure 媒体服务 v3 正式版](/cli/azure/ams) - v 2.0.50。

### <a name="new-commands"></a>新命令

- [az ams account](/cli/azure/ams/account)
- [az ams account-filter](/cli/azure/ams/account-filter)
- [az ams asset](/cli/azure/ams/asset)
- [az ams asset-filter](/cli/azure/ams/asset-filter)
- [az ams content-key-policy](/cli/azure/ams/content-key-policy)
- [az ams job](/cli/azure/ams/job)
- [az ams live-event](/cli/azure/ams/live-event)
- [az ams live-output](/cli/azure/ams/live-output)
- [az ams streaming-endpoint](/cli/azure/ams/streaming-endpoint)
- [az ams streaming-locator](/cli/azure/ams/streaming-locator)
- [az ams account mru](/cli/azure/ams/account/mru) - 用于管理媒体预留单位。 有关详细信息，请参阅[缩放媒体预留单位](media-reserved-units-cli-how-to.md)。

### <a name="new-features-and-breaking-changes"></a>新功能和重大更改

#### <a name="asset-commands"></a>资产命令

- 添加了 ```--storage-account``` 和 ```--container``` 参数。
- 在 ```az ams asset get-sas-url``` 命令中添加了到期时间默认值（现在时间 + 23 小时）和权限默认值（读取）。

#### <a name="job-commands"></a>作业命令

- 添加了 ```--correlation-data``` 和 ```--label``` 参数
- ```--output-asset-names``` 已重命名为 ```--output-assets```。 现在，它接受 'assetName=label' 格式的资产列表（以空格分隔）。 没有标签的资产可以采用以下格式发送：'assetName='。

#### <a name="streaming-locator-commands"></a>创建流式处理定位符命令

- ```az ams streaming locator``` 基本命令已替换为 ```az ams streaming-locator```。
- 添加了 ```--streaming-locator-id``` 和 ```--alternative-media-id support``` 参数。
- 更新了 ```--content-keys argument``` 参数。
- ```--content-policy-name``` 已重命名为 ```--content-key-policy-name```。

#### <a name="streaming-policy-commands"></a>流式处理策略命令

- ```az ams streaming policy``` 基本命令已替换为 ```az ams streaming-policy```。
- 在 ```az ams streaming-policy create``` 中添加了加密参数支持。

#### <a name="transform-commands"></a>转换命令

- ```--preset-names``` 参数已替换为 ```--preset```。 现在只能一次设置 1 个输出/预设（若要添加更多，必须运行 ```az ams transform output add```）。 此外，还可以通过将路径传递到自定义 JSON 来设置自定义 StandardEncoderPreset。
- 可以通过传递要删除的输出索引来执行 ```az ams transform output remove```。
- 在 ```az ams transform create``` 和 ```az ams transform output add``` 命令中添加了 ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 参数。

## <a name="october-2018---ga"></a>October 2018 - GA

本部分介绍 Azure 媒体服务 (AMS) 的 10 月更新。

### <a name="rest-v3-ga-release"></a>REST v3 GA 发布

[REST v3 GA 发布](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)包含更多用于实时、帐户/资产级别清单筛选器和 DRM 支持的 API。

#### <a name="azure-resource-management"></a>Azure 资源管理 

对 Azure 资源管理的支持实现了统一的管理和操作 API（现在所有功能已整合在一起）。

从此版本开始，可以使用资源管理器模板创建直播活动。

#### <a name="improvement-of-asset-operations"></a>改善资产操作 

引入了以下改进：

- 从 HTTP(s) URL 或 Azure Blob 存储 SAS URL 中引入。
- 为资产指定自己的容器名称。 
- 使用 Azure Functions 创建自定义工作流支持更轻松的输出。

#### <a name="new-transform-object"></a>新转换对象

新转换对象简化编码模型。 通过新对象，可以轻松创建和共享编码资源管理器模板和预设。 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Azure Active Directory 身份验证和 Azure RBAC

Azure AD 身份验证和 Azure 基于角色的访问控制 (Azure RBAC) 通过 Azure AD 中的角色或用户启用安全转换、实时事件、内容密钥策略或资产。

#### <a name="client-sdks"></a>客户端 SDK  

媒体服务 v3 支持的语言：NET Core、Java、Node.js、Ruby、Typescript、Python、Go。

#### <a name="live-encoding-updates"></a>实时编码更新

引入了以下实时编码更新：

- 针对实时传送视频的新的低延迟模式（10 秒端到端）。
- 改进的 RTMP 支持（提高了稳定性并提供了更多的源编码器支持）。
- RTMPS 安全引入。

    创建直播活动时，现在会得到 4 个引入 URL。 这 4 个引入 URL 几乎是相同的，具有相同的流式处理令牌 (AppId)，仅端口号部分不同。 其中两个 URL 是 RTMPS 的主要和备份 URL。 
- 24 小时转码支持。 
- 通过 SCTE35 改进了 RTMP 中的广告信号支持。

#### <a name="improved-event-grid-support"></a>改进了事件网格支持

可以看到以下事件网格支持改进：

- 可通过逻辑应用和 Azure Functions 更轻松地开发 Azure 事件网格集成。 
- 订阅事件的编码、直播频道等。

### <a name="cmaf-support"></a>CMAF 支持

CMAF 和“cbcs”加密支持 Apple HLS (iOS 11+) 以及支持 CMAF 的 MPEG-DASH 播放器。

### <a name="video-indexer"></a>视频索引器

视频索引器 GA 版本于 8 月份发布。 有关当前支持的功能的新信息，请参阅[什么是视频索引器](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md?bc=%2fazure%2fmedia-services%2fvideo-indexer%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fmedia-services%2fvideo-indexer%2ftoc.json)。 

### <a name="plans-for-changes"></a>更改计划

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
包含所有功能（包括 Live、内容密钥策略、帐户/资产筛选器、流式处理策略）的操作的 Azure CLI 2.0 模块即将推出。 

### <a name="known-issues"></a>已知问题

只有使用资产或 AccountFilter 预览版 API 的客户才会受到以下问题的影响。

如果你使用媒体服务 v3 CLI 或 API 在 9 月 28 日到 10 月 12 日之间创建了资产或帐户筛选器，因版本冲突，需要删除所有资产和 AccountFilter，并重新创建它们。 

## <a name="may-2018---preview"></a>2018 年 5 月 - 预览版

### <a name="net-sdk"></a>.NET SDK

.NET SDK 中提供了以下功能：

* 转换和作业，用于对媒体内容来进行编码或分析 。 有关示例，请参阅[流式传输文件](stream-files-tutorial-with-api.md)和[分析](analyze-videos-tutorial.md)。
* **流式处理定位符**，用于发布内容并将其流式传输到最终用户设备
* **流式处理策略** 和 **内容密钥策略**，用于在传送内容时配置密钥传递和内容保护 (DRM)。
* **直播活动** 和 **实时输出**，用于配置实时传送视频流内容的引入和归档。
* 资产，用于在 Azure 存储中存储和发布媒体内容。 
* **流式处理终结点**，用于配置和缩放实时和点播媒体内容的动态打包、加密和流式处理。

### <a name="known-issues"></a>已知问题

* 提交作业时，可以指定使用 HTTPS URL、SAS URL 或位于 Azure Blob 存储中的文件路径引入源视频。 媒体服务 v3 目前不支持基于 HTTPS URL 的块式传输编码。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="see-also"></a>请参阅

[有关从媒体服务 v2 迁移到 v3 的迁移指南](migrate-v-2-v-3-migration-introduction.md)。

## <a name="next-steps"></a>后续步骤

- [概述](media-services-overview.md)
- [媒体服务 v2 发行说明](../previous/media-services-release-notes.md)
