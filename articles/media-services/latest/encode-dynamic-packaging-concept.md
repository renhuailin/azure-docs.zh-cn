---
title: Azure 媒体服务 v3 中的动态打包
description: 本文概述了 Azure 媒体服务中的动态打包。
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: acc993f5690fbc250659830b52099f8d3d966421
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322166"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>媒体服务 v3 中的动态打包

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒体服务提供内置源服务器和打包功能，以 HLS 和 MPEG DASH 流式处理协议格式交付内容。 在 AMS 中，[流式处理终结点](stream-streaming-endpoint-concept.md)充当“源”服务器，向支持使用这些常用格式的自适应比特率流式处理的客户端播放器发送格式化 HLS 和 DASH 内容。 流式处理终结点还支持许多功能（例如具有或没有内容保护的即时动态打包），以访问所有主要设备（如 iOS 和 Android 设备）。 

当今市场上大多数浏览器和移动设备都支持并理解 HLS 或 DASH 流式处理协议。 例如，iOS 要求以 HTTP Live Streaming (HLS) 格式传送流，Android 设备在特定型号上支持 HLS 以及 MPEG DASH（或者通过使用适用于 Android 设备的应用程序级别播放器 [Exoplayer](https://exoplayer.dev/)）。

在媒体服务中，[流式处理终结点](stream-streaming-endpoint-concept.md)（源）表示动态（即时）打包和源服务，该服务可直接将你的实时和按需内容发送到客户端播放器应用。 它使用下一部分中所述的一种常见流式处理媒体协议。 动态打包是所有流式处理终结点的标准功能。

即时打包的好处如下：

* 可以使用标准 MP4 文件格式存储所有文件
* 无需在 blob 存储中存储静态打包的 HLS 和 DASH 格式的多个副本，从而减少存储的视频内容量并降低总体存储成本
* 可以在协议和规范随时间而发展时，立即利用新的协议更新和规范更改，无需在目录中重新打包静态内容
* 可以使用存储中的相同 MP4 文件，通过我们交付内容，而无需加密和 DRM
* 可以使用简单的资产级别或全局筛选器动态筛选或更改清单，以删除特定曲目、分辨率、语言，或者提供来自相同 MP4 文件的较短精彩内容剪辑，而无需重新编码或重新呈现内容。 

## <a name="to-prepare-your-source-files-for-delivery"></a>准备源文件供传输

若要利用动态打包，需将夹层（源）文件[编码](encode-concept.md)为一组单个或多个自适应比特率 MP4（ISO 基本媒体 14496-12）文件。 你需要具备包含媒体服务动态打包所需的编码 MP4 和流式处理配置文件的[资产](assets-concept.md)。 通过此组 MP4 文件，可以使用动态打包通过下述流媒体协议传送视频。

通常，你会使用 Azure 媒体服务标准编码器通过内容感知型编码预设或自适应比特率预设来生成此内容。  两者都会生成一组可供流式处理和动态打包的 MP4 文件。  或者，可以选择在外部服务、本地或是自己的 VM 或无服务器函数应用中进行编码。 外部编码的内容可以上传到资产进行流式处理，前提是它满足自适应比特率流式处理格式的编码要求。 .NET SDK 示例中提供了上传预编码 MP4 进行流式传输的示例项目 - 请参阅[对现有 Mp4 文件进行流式处理](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4)。


Azure 媒体服务动态打包仅支持 MP4 容器格式的视频和音频文件。 如果使用的是备用编解码器（如 Dolby），则必须将音频文件编码为 MP4 容器。  

> [!TIP]
> 获取 MP4 和流式处理配置文件的一种方式是[使用媒体服务对夹层文件进行编码](#encode-to-adaptive-bitrate-mp4s)。  建议使用[内容感知型编码预设](encode-content-aware-concept.md)为内容生成最佳自适应流式处理层和设置。 请参阅代码示例以了解使用 [VideoEncoding 文件夹中的 .NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding) 进行编码 

若要使编码资产中的视频可供客户端播放，必须使用[流式处理定位符](stream-streaming-locators-concept.md)发布资产，然后生成相应的 HLS 和 DASH 流式处理 URL。 通过更改 URL 格式查询中使用的协议，服务会交付相应的流式处理清单（HLS、MPEG DASH）。

因此，只需以单一存储格式 (MP4) 存储文件并为其付费，媒体服务便会基于客户端播放器的请求生成并提供相应的 HLS 或 DASH 清单。

如果计划使用媒体服务动态加密来保护内容，请参阅[流式处理协议和加密类型](drm-content-protection-concept.md#streaming-protocols-and-encryption-types)。

## <a name="deliver-hls"></a>交付 HLS
### <a name="hls-dynamic-packaging"></a>HLS 动态打包

流式处理客户端可以指定以下 HLS 格式。 建议使用 CMAF 格式，以便与最新播放器和 iOS 设备兼容。  对于旧设备，只需通过更改格式查询字符串，也可使用 v4 和 v3 格式。

|协议| 格式字符串| 示例|
|---|---|---|
|HLS CMAF（建议）| format=m3u8-cmaf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|HLS V4 |  format=m3u8-aapl | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 | format=m3u8-aapl-v3 | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|


> [!NOTE]
> Apple 以前的准则建议低带宽网络的回退提供仅限音频的流。  目前，媒体服务编码器自动生成仅限音频的曲目。现在，Apple 准则表明不应包含仅限音频的曲目，特别是对于 Apple TV 发行版。  为了防止播放机默认设置为仅限音频的曲目，我们建议在 URL 中使用“audio-only=false”标记（这将在 HLS 中删除仅音频呈现形式），或使用 HLS-V3。 例如，`http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`。


### <a name="hls-packing-ratio-for-vod"></a>VOD 的 HLS 打包比例

若要控制较旧 HLS 格式的 VOD 内容打包比例，可以在 .ism 文件中设置 fragmentsPerHLSSegment 元数据标记，以控制从较旧 v3 和 v4 HLS 格式清单交付的 TS 段的默认 3:1 打包比例。 此设置更改要求直接修改存储中的 .ism 文件以调整打包比例。

fragmentsPerHLSSegment 设置为 1 的示例 .ism 服务器清单。 
``` xml
   <?xml version="1.0" encoding="utf-8" standalone="yes"?>
   <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
         <meta name="formats" content="mp4" />
         <meta name="fragmentsPerHLSSegment" content="1"/>
      </head>
      <body>
         <switch>
         ...
         </switch>
      </body>
   </smil>
```

## <a name="deliver-dash"></a>交付 DASH
### <a name="dash-dynamic-packaging"></a>DASH 动态打包

流式处理客户端可以指定以下 MPEG-DASH 格式：

|协议| 格式字符串| 示例|
|---|---|---|
|MPEG-DASH CMAF（建议）| format=mpd-time-cmaf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|MPEG-DASH CSF（旧版）| format=mpd-time-csf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |

## <a name="deliver-smooth-streaming-manifests"></a>交付平滑流式处理清单
### <a name="smooth-streaming-dynamic-packaging"></a>平滑流式处理动态打包

流式处理客户端可以指定以下平滑流式处理格式：

|协议|说明/示例| 
|---|---|
|平滑流| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|平滑流式处理 2.0（旧清单）|默认情况下，平滑流式处理清单格式包含重复标记（r 标记）。 但是，一些播放器不支持 `r-tag`。 使用这些播放器的客户端可以使用禁用 r 标记的格式：<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> 平滑流式处理要求流中同时存在音频和视频。

## <a name="on-demand-streaming-workflow"></a>按需流式处理工作流

以下步骤显示了常见的媒体服务流式处理工作流，其中动态打包与 Azure 媒体服务中的标准编码器一起使用。

1. [上传一个输入文件](job-input-from-http-how-to.md)，例如 MP4、QuickTime/MOV 或其他受支持的文件格式。 此文件也称为夹层文件或源文件。 有关受支持格式的列表，请参阅[标准编码器支持的格式](encode-media-encoder-standard-formats-reference.md)。
1. 将夹层文件[编码](#encode-to-adaptive-bitrate-mp4s)为 H.264/AAC MP4 自适应比特率集。

    如果已经有编码文件，只是想要复制并流式传输文件，请使用：[CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) 和 [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio) API。 将创建一个带有流式处理清单（.ism 文件）的新 MP4 文件。

    此外，可以仅在预编码文件上生成 .ism 和 .ismc 文件，只要使用自适应比特率流式处理的正确设置进行编码（这通常是 2 秒 GOP、最小和最大值为 2 秒的关键帧距离以及恒定比特率 (CBR) 模式编码）。  

    请参阅[对现有 Mp4 .NET SDK 示例进行流式处理](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4)，以详细了解如何生成 .ism（服务器清单）和 .ismc（客户端清单）以便从现有预编码 MP4 文件进行流式处理。

1. 发布包含自适应比特率 MP4 集的输出资产。 通过创建[流式处理定位符](stream-streaming-locators-concept.md)进行发布。
1. 生成针对不同格式（HLS、MPEG-DASH 和平滑流式处理）的 URL。 流式处理终结点将负责为所有这些不同格式提供正确的清单和请求。
    
下图显示了使用动态打包进行按需流式处理的工作流。

![使用动态打包进行按需流式处理的工作流关系图](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

上图中显示了下载路径，这只是为了向你展示可通过流式处理终结点（源）（在流式处理定位符上指定可下载的[流式处理策略](stream-streaming-policy-concept.md)）直接下载 MP4 文件。<br/>动态包生成工具不会更改文件。 如果希望绕过流式处理终结点（来源）功能，则可以选择使用 Azure blob 存储 API 直接访问 MP4 以便进行渐进式下载。 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>编码为自适应比特率 MP4

以下文章介绍[如何使用媒体服务对视频进行编码](encode-concept.md)的示例：

* [使用内容感知型编码](encode-content-aware-concept.md)。
* [使用内置预设从 HTTPS URL 进行编码](job-input-from-http-how-to.md)。
* [使用内置预设对本地文件进行编码](job-input-from-local-file-how-to.md)。
* [构建自定义预设，以确定特定方案或设备要求](transform-custom-presets-how-to.md)。
* [使用 .NET 通过标准编码器进行编码的代码示例](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding)

请参阅支持的标准编码器输入[格式和编解码器](encode-media-encoder-standard-formats-reference.md)的列表。

## <a name="live-streaming-workflow"></a>实时传送视频流工作流

直播活动可以设置为“直通”（本地实时编码器发送多比特率流）或“实时编码”（本地实时编码器发送单比特率流）。 

以下是使用动态打包进行实时传送视频流的常用工作流：

1. 创建[直播活动](live-event-outputs-concept.md)。
1. 获取引入 URL 并配置本地编码器以使用 URL 发送贡献源。
1. 获取预览 URL 并使用它验证是否接收到来自编码器的输入。
1. 创建新资产。
1. 创建实时输出并使用创建的资产名称。<br />实时输出会将流存档到资产中。
1. 使用内置的流式处理策略类型创建流式处理定位符。<br />如果想要加密内容，请查看[内容保护概述](drm-content-protection-concept.md)。
1. 列出流式处理定位符的路径，以获取要使用的 URL。
1. 获取要从中进行流式传输的流式处理终结点的主机名。
1. 生成针对不同格式（HLS、MPEG-DASH 和平滑流式处理）的 URL。 流式处理终结点负责提供正确的清单并请求不同的格式。

此关系图显示使用动态打包进行实时传送视频流的工作流：

![使用动态打包进行直通编码的工作流关系图](./media/live-streaming/pass-through.svg)

有关在媒体服务 v3 中实时传送视频流的信息，请参阅[实时传送视频流概述](stream-live-streaming-concept.md)。

## <a name="video-codecs-supported-by-dynamic-packaging"></a>动态打包支持的视频编解码器

动态打包支持采用 MP4 容器文件格式，并包含使用 [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC)（MPEG-4 AVC 或 AVC1）或是 [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding)（HEVC、hev1 或 hvc1）进行编码的视频的视频文件。

> [!NOTE]
> 已使用动态打包测试了高达 4K 的分辨率和高达 60 帧/秒的帧速率。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>动态打包支持的音频编解码器

动态打包还支持以 MP4 文件容器格式存储、包含使用以下编解码器之一编码的音频流的音频文件：

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)（AAC-LC、HE-AAC v1 或 HE-AAC v2）。 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)（增强型 AC-3 或 E-AC3）。  编码的音频必须以 MP4 容器格式存储，才能使用动态打包。
* Dolby Atmos

   流式处理 Dolby Atmos 内容支持 MPEG-DASH 协议等标准，包括采用公共流式处理格式 (CSF) 或公共媒体应用程序格式 (CMAF) 分段的 MP4，以及通过具有 CMAF 的 HTTP Live Streaming (HLS)。
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DASH-CSF、DASH-CMAF、HLS-M2TS 和 HLS-CMAF 打包格式支持的 DTS 编解码器包括：  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution 和 DTS-HD Master Audio  (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (no core) (dtsl)

动态打包支持使用 DASH 或 HLS（版本 4 或更高版本）的多音轨，用于流式传输包含使用多个编解码器和语言的多音轨的资产。

对于以上所有音频编解码器，编码的音频必须以 MP4 容器格式存储，才能使用动态打包。 该服务不支持 blob 存储上的原始基本流文件格式（例如，不支持以下格式：.dts、.ac3）。 

音频打包仅支持扩展名为 .mp4 或 .mp4a 的文件。 

### <a name="limitations"></a>限制

#### <a name="ios-limitation-on-aac-51-audio"></a>AAC 5.1 音频上的 iOS 限制

Apple iOS 设备不支持 5.1 AAC 音频编解码器。 必须使用 Dolby Digital 或 Dolby Digital Plus 编解码器对多通道音频进行编码。

有关详细信息，请参阅[适用于 Apple 设备的 HLS 创作规范](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices)。

> [!NOTE]
> 媒体服务不支持 Dolby Digital、Dolby Digital Plus 或 Dolby Atmos 多通道音频格式的 Dolby Digital Plus 编码。

#### <a name="dolby-digital-audio"></a>Dolby Digital 音频

媒体服务动态打包目前不支持包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音频（因为这被视为 Dolby 遗留的编解码器）的文件。

## <a name="manifests"></a>清单

在媒体服务动态打包中，HLS、MPEG-DASH 和平滑流式处理的流式处理客户端清单是基于 URL 中的格式查询动态生成的。  

清单文件包含流元数据，例如轨迹类型（音频、视频或文本）、轨迹名称、开始和结束时间、比特率（质量）、轨迹语言、演播窗口（持续时间固定的滑动窗口）和视频编解码器 (FourCC)。 此文件还会通过提供有关下一个可播放视频片段及其位置的信息，来指示播放器检索下一个片段。 片段（或段）实际上是视频内容的“区块”。

### <a name="examples"></a>示例

#### <a name="hls"></a>HLS

以下示例是 HLS 清单文件，也称为 HLS 主播放列表： 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

以下示例是 MPEG-DASH 清单文件，也称为 MPEG-DASH 媒体演示说明 (MPD)：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>平滑流

下面是平滑流式处理清单文件的示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>命名清单中的曲目

如果在 .ism 文件中指定了音轨名称，则媒体服务会在 `AdaptationSet` 中添加 `Label` 元素，以指定特定音轨的纹理信息。输出 DASH 清单的示例：

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

播放机可以使用 `Label` 元素在其 UI 上显示。

### <a name="signaling-audio-description-tracks"></a>发出音频描述轨道的信号

可以向视频中添加旁白轨道，以帮助视力受损的客户通过倾听旁白来跟随视频录制。 需要将音轨批注为清单中的音频描述。 为此，请将“accessibility”和“role”参数添加到 .ism 文件中。 你有责任正确设置这些参数，以将音频轨道作为音频描述发出信号。 例如，将 `<param name="accessibility" value="description" />` 和 `<param name="role" value="alternate"` 添加到特定音频轨道的 .ism 文件中。 

有关详细信息，请参阅[如何发出描述性音轨信号](signal-descriptive-audio-howto.md)示例。

#### <a name="smooth-streaming-manifest"></a>平滑流式处理清单

如果正在播放平滑流式处理流，则清单将在该音频轨道的 `Accessibility` 和 `Role` 属性中携带值。例如，`Role="alternate" Accessibility="description"` 将添加到 `StreamIndex` 元素中，以指示它是音频描述。

#### <a name="dash-manifest"></a>DASH 清单

对于 DASH 清单，将添加以下两个元素以发出音频描述信号：

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS 播放列表

对于 HLS v7 及更高版本 `(format=m3u8-cmaf)`，当音频描述跟踪收到信号时，其播放列表将携带 `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"`。



#### <a name="example"></a>示例

有关详细信息，请参阅[如何发出音频描述轨道信号](signal-descriptive-audio-howto.md)。

## <a name="dynamic-manifest-filtering"></a>动态清单筛选

若要控制发送到播放器的曲目数目、格式、比特率和呈现时间范围，可以将动态筛选与媒体服务动态包生成工具配合使用。 有关详细信息，请参阅[预筛选清单与动态包生成工具配合使用](filters-dynamic-manifest-concept.md)。

## <a name="dynamic-encryption-for-drm"></a>DRM 的动态加密

可以使用动态加密借助 AES-128 或三种主要数字版权管理 (DRM) 系统中的任何一种对实时或按需内容进行动态加密：内容。 媒体服务还提供用于向已授权客户端传送 AES 密钥和 DRM 许可证的服务。 有关详细信息，请参阅[动态加密](drm-content-protection-concept.md)。

> [!NOTE]
> Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="more-information"></a>详细信息

查看 [Azure 媒体服务社区](media-services-community.md)，了解提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="need-help"></a>需要帮助？

可以通过导航到[新建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来开具支持票证。

## <a name="next-steps"></a>后续步骤

[上传、编码和流式处理视频](stream-files-tutorial-with-api.md)
