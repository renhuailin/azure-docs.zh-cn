---
title: Azure 媒体服务 LiveEvent 类型
description: 在 Azure 媒体服务中，可以将直播活动设为“直通”  或“实时编码”  。 本文显示了比较直播活动类型的详细表格。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 09bd0cf6a43934ea7a0e973b64c434b705d9ea36
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716032"
---
# <a name="live-event-types-comparison"></a>实时事件类型比较

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒体服务中，可以将[直播活动](/rest/api/media/liveevents)设为“直通”  （本地实时编码器发送多比特率流）或“实时编码”  （本地实时编码器发送单比特率流）。 

本文对直播活动类型的功能进行了比较。

## <a name="types-comparison"></a>类型比较 

下表比较了实时事件类型的功能。 这些类型是在创建期间使用 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype) 设置的：

* LiveEventEncodingType.PassthroughBasic：本地实时编码器发送多比特率流。 基本直通限制为 5 Mbps 的峰值流入量，DVR 窗口时限为 8 小时，不支持实时听录。
* LiveEventEncodingType.PassthroughStandard：本地实时编码器发送多比特率流。 标准直通的引入限制更高，DVR 窗口时限为 25 小时，支持实时听录。
* **LiveEventEncodingType.Standard** - 本地实时编码器将单比特率流发送到实时事件，媒体服务创建多比特率流。 如果贡献源的分辨率为 720p 或更高，则 **Default720p** 预设将编码一组 6 分辨率/比特率对（本文稍后会提供详细信息）。
* **LiveEventEncodingType.Premium1080p** - 本地实时编码器将单比特率流发送到实时事件，媒体服务创建多比特率流。 Default1080p 预设指定分辨率/比特率对的输出集（本文稍后会提供详细信息）。 

| Feature                                                                           | 基本直通                                                                                                | 标准直通                                                                                             | Standard 720P 或 Premium 1080P 编码事件                                                                                                                          |
| --------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 单比特率输入在云中转码为多比特率            | 否                                                                                                                | 否                                                                                                                | 是                                                                                                                                                          |
| 贡献源的最大视频分辨率                                    | 4K（4096x2160，60 帧/秒）                                                                                   | 4K（4096x2160，60 帧/秒）                                                                                   | 1080p（1920x1088，30 帧/秒）                                                                                                                           |
| 贡献源中建议的最大层数（在引入带宽限制内） | 限制为最大聚合带宽 5 Mbps                                                                                              | 限制为最大聚合带宽 60 Mbps                                                                                                       | 1 个视频轨道和 1 个音频（任何其他轨道以无提示方式删除）轨道                                                                                                                                                    |
| 输出中的最大层数                                                          | 与输入相同                                                                                                     | 与输入相同                                                                                                     | 最多 6 个（请参阅下面的“系统预设”）                                                                                                                           |
| 贡献源的最大聚合带宽                                  | 支持高达 5 Mbps 的组合输入，单个比特率不超过 4 Mbps。 无视频帧速率限制。                                                                                                     | 支持高达 60 Mbps 的组合输入，单个比特率不超过 20 Mbps。 无视频帧速率限制。                                                                                                    | 支持单比特率输入。 单个输入带宽不能超过 20 Mbps。 视频帧速率不能超过 60 帧/秒。                                                                                                                                                         |
| 允许的最大 DVR（时移）窗口持续时间                                  | 最多 8 小时                                                                                                     | 最多 25 小时                                                                                                    | 最多 25 小时                                                                                                                                               |
| 允许的最大实时输出数                                            | 仅 1 个实时输出                                                                                                | 最多 3 个实时输出                                                                                              | 最多 3 个实时输出                                                                                                                                         |
| 贡献中单个层的最大比特率                            | 最高可达 4 Mbps                                                                                                      | 20 Mbps                                                                                                           | 20 Mbps                                                                                                                                                      |
| 支持多语言音轨                                        | 是                                                                                                               | 是                                                                                                               | 否                                                                                                                                                           |
| 支持的输入视频编解码器                                                      | H.264/AVC（RTMP 和平滑流式处理引入）或 H.265/HEVC（仅平滑流式处理引入）                                         | H.264/AVC（RTMP 和平滑流式处理引入）或 H.265/HEVC（仅平滑流式处理引入）                                         | H.264/AVC（RTMP 和平滑流式处理引入）                                                                                                                 |
| 支持的输出视频编解码器                                                     | 与输入相同                                                                                                     | 与输入相同                                                                                                     | H.264/AVC                                                                                                                                                    |
| 支持的视频位深、输入和输出                                      | 最多 10 位，包括 HDR 10/HLG                                                                                 | 最多 10 位，包括 HDR 10/HLG                                                                                 | 8 位                                                                                                                                                        |
| 支持的输入音频编解码器                                                      | AAC-LC、HE-AAC v1、HE-AAC v2                                                                                      | AAC-LC、HE-AAC v1、HE-AAC v2                                                                                      | AAC-LC、HE-AAC v1、HE-AAC v2                                                                                                                                 |
| 支持的输出音频编解码器                                                     | 与输入相同                                                                                                     | 与输入相同                                                                                                     | AAC-LC                                                                                                                                                       |
| 输出视频的最大视频分辨率                                          | 与输入相同                                                                                                     | 与输入相同                                                                                                     | Standard - 720p，Premium1080p - 1080p                                                                                                                        |
| 输入视频的最大帧速率                                                 | 60 帧/秒                                                                                                  | 60 帧/秒                                                                                                  | Standard 或 Premium 1080p - 60 帧/秒 - 转码输出将减少到 23.98、24、25、29.97 或 30 fps，仅取决于源帧速率。 |
| 输入协议                                                                   | RTMP、分段 MP4（平滑流式处理）                                                                           | RTMP、分段 MP4（平滑流式处理）                                                                           | RTMP、分段 MP4（平滑流式处理）                                                                                                                      |
| 价格                                                                             | 请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡 | 请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡 | 请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡                                            |
| 最长运行时间                                                                  | 24 小时 x 365 天，实时线性                                                                                    | 24 小时 x 365 天，实时线性                                                                                    | 24 小时 x 365 天，实时线性（预览）                                                                                                                     |
| 传递嵌入式 CEA 608/708 字幕数据的能力                        | 是                                                                                                               | 是                                                                                                               | 是                                                                                                                                                          |
| 实时听录支持                                            | 否。 基本直通不支持实时听录。                                                 | 是                                                                                                               | 是                                                                                                                                                          |
| 支持通过带内 SCTE35 消息发出广告指示 | 是 | 是 | 是                                                                                                                                                                                                   |
| 支持非一致性输入 GOP                    | 是 | 是 | 是持续时间                                               |
| 输入源丢失时，会自动关闭直播活动    | 否  | 否  | 12 小时后，如果没有运行的 LiveOutput                                                                                                                                                     |

## <a name="system-presets"></a>系统预设

实时编码器的输出中包含的分辨率和比特率由 [presetName](/rest/api/media/liveevents/create#liveeventencoding) 确定。 如果使用 **Standard** 实时编码器 (LiveEventEncodingType.Standard)，则 *Default720p* 预设将指定如下所述的一组 6 分辨率/比特率对。 否则，如果使用 **Premium1080p** 实时编码器 (LiveEventEncodingType.Premium1080p)，则 *Default1080p* 预设将指定分辨率/比特率对的输出集。

> [!NOTE]
> 如果为 Standard 实时编码设置了 Default1080p 预设，则无法将该预设应用到实时事件 - 会出现错误。 如果尝试将 Default720p 预设应用到 Premium1080p 实时编码器，也会出现错误。

### <a name="output-video-streams-for-default720p"></a>Default720p 的输出视频流

如果贡献源的分辨率为 720p 或更高，则 **Default720p** 预设会将源编码到以下 6 个层中。 在下表中，比特率以 kbps 为单位，MaxFPS 表示允许的最大帧速率（帧/秒），Profile 表示使用的 H.264 配置文件。

如果输入的源帧速率 > 30 fps，则帧速率将降低，以匹配输入帧速率的一半。  例如，60 fps 将减少到 30fps。  50 fps 将减少到 25 fps 等。


| Bitrate | 宽度 | 高度 | MaxFPS | 配置文件 |
| ------- | ----- | ------ | ------ | ------- |
| 3500    | 1280  | 720    | 30     | 高    |
| 2200    | 960   | 540    | 30     | 高    |
| 1350    | 704   | 396    | 30     | 高    |
| 850     | 512   | 288    | 30     | 高    |
| 550     | 384   | 216    | 30     | 高    |
| 200     | 340   | 192    | 30     | 高    |

> [!NOTE]
> 如果需要自定义实时编码预设，请通过 Azure 门户开具支持票证。 你应当指定所需的视频分辨率和比特率的表。 不支持自定义音频编码比特率。 请确认只有一个 720p 的图层，最多有 6 个图层。 此外，请务必指定你要请求预设。

### <a name="output-video-streams-for-default1080p"></a>Default1080p 的输出视频流

如果贡献源的分辨率为 1080p，则 **Default1080p** 预设会将源编码到以下 6 个层中。

如果输入的源帧速率 > 30 fps，则帧速率将降低，以匹配输入帧速率的一半。  例如，60 fps 将减少到 30fps。  50 fps 将减少到 25 fps 等。

| Bitrate | 宽度 | 高度 | MaxFPS | 配置文件 |
| ------- | ----- | ------ | ------ | ------- |
| 5500    | 1920  | 1080   | 30     | 高    |
| 3000    | 1280  | 720    | 30     | 高    |
| 1600    | 960   | 540    | 30     | 高    |
| 800     | 640   | 360    | 30     | 高    |
| 400     | 480   | 270    | 30     | 高    |
| 200     | 320   | 180    | 30     | 高    |

> [!NOTE]
> 如果需要自定义实时编码预设，请通过 Azure 门户开具支持票证。 你应当指定所需的分辨率和比特率的表。 请确认只有一个层的分辨率为 1080p，且最多有 6 个层。 此外，请指定你要请求 Premium1080p 实时编码器的预设。 比特率和分辨率的特定值今后可能会调整。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Default720p 和 Default1080p 的输出音频流

对于 *Default720p* 和 *Default1080p* 预设，音频将编码到 128 kbps 的立体声 AAC-LC。 采样率与贡献源中的音频轨迹采样率相同。

## <a name="implicit-properties-of-the-live-encoder"></a>实时编码器的隐式属性

上一部分介绍了可以通过预设显式控制的实时编码器属性 - 例如层数、分辨率和比特率。 本部分将阐明隐式属性。

### <a name="group-of-pictures-gop-duration"></a>帧组 (GOP) 持续时间

实时编码器遵从贡献源的 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 结构 - 这意味着，输出层具有相同的 GOP 持续时间。 因此，我们建议配置本地编码器，生成具有固定 GOP 持续时间（通常为 2 秒）的贡献源。 这可以确保服务传出的 HLS 和 MPEG DASH 流也具有固定的 GOP 持续时间。 大多数设备可能都会容忍 GOP 持续时间存在较小的变化。

### <a name="frame-rate-limits"></a>帧速率限制

实时编码器还遵从贡献源中单个视频帧的持续时间 - 这意味着，输出层的帧具有相同的持续时间。 因此，我们建议配置本地编码器，生成具有固定帧速率（最大 30 帧/秒）的贡献源。 这可以确保服务传出的 HLS 和 MPEG DASH 流也具有固定的帧速率持续时间。 大多数设备可能都会容忍帧速率存在较小的变化，但不保证实时编码器生成可正常播放的输出。 本地实时编码器不应丢帧（例如 在电池电量不足的情况下），或以任何方式改变帧速率。

如果输入的源帧速率 > 30 fps，则帧速率将降低，以匹配输入帧速率的一半。  例如，60 fps 将减少到 30fps。  50 fps 将减少到 25 fps 等。

### <a name="resolution-of-contribution-feed-and-output-layers"></a>贡献源和输出层的分辨率

实时编码器的配置可避免向上转换贡献源。 因此，输出层的最大分辨率不会超过贡献源的最大分辨率。

例如，如果将 720p 的贡献源发送到为 Default1080p 实时编码配置的实时事件，则输出只包含 5 层，最前面的层的分辨率为 720p，比特率为 3 Mbps，最后面的层的分辨率为 1080p，比特率为 200 kbps。 或者，如果将 360p 的贡献源发送到为 Standard 实时编码配置的实时事件，则输出将包含 3 层（分辨率分别为 288p、216p 和 192p）。 在退化的情况下，如果将 160x90 像素（举例而言）的贡献源发送到 Standard 实时编码器，则输出将包含一个 160x90 分辨率的层，其比特率与贡献源的比特率相同。

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>贡献源和输出层的比特率

无论贡献源的比特率如何，实时编码器的配置都会遵从预设中的比特率设置。 因此，输出层的比特率可能会超过贡献源的比特率。 例如，如果传入一个比特率为 1 Mbps、分辨率为 720p 的贡献源，则输出层将与[上表](live-event-types-comparison-reference.md#output-video-streams-for-default720p)中的描述相同。


## <a name="next-steps"></a>后续步骤

[实时传送视频流概述](stream-live-streaming-concept.md)
