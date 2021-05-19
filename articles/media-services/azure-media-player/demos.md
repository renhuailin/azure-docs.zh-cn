---
title: Azure Media Player 演示
description: 本页包含指向 Azure Media Player 演示的链接的列表。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 584748b23f526e6f03b543b8298927e3f202f743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "82139298"
---
# <a name="azure-media-player-demos"></a>Azure Media Player 演示

下面是指向 Azure Media Player 演示的链接的列表。 从 GitHub 可以下载所有 [Azure Media Player 示例](https://github.com/Azure-Samples/azure-media-player-samples)。

## <a name="demo-listing"></a>演示列表

| 示例名称 | 使用 JavaScript 以编程方式 | 使用 HTML5 视频元素以静态方式 | 说明 |
| ------------|----------------------------|-------------------------------------|--------------|
| 基本 |
| 设置源 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |播放未受保护的内容。|
| 功能 |
| VOD 广告插入 - VAST | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | 不适用 | 在 VOD 资产中插入视频播放前、中、后的 VAST 广告。 |
| 播放速度 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| 不适用 | 使观看者能够控制观看视频的速度。 |
| AMP 刷新外观 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | 启用新的 AMP 外观。 **注意：** 仅 AMP 版本 2.1.0+ 支持 AMP 刷新 |
| 隐藏式字幕和字幕 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | 使用 WebVTT 字幕播放。
| 直播 CEA 708 隐藏式字幕 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | 不适用 | 使用直播 CEA 708 入站隐藏式字幕（左对齐）播放。 |
| 包含渐进式回退的流式处理 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | 使用渐进式回退进行自适应播放的基本设置（如果平台不支持流式处理）。 |
| 渐进式视频 MP4 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | 播放渐进式音频 MP4。 |
| 渐进式音频 MP3 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | 播放渐进式音频 MP3。 |
| DD+ | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | 不适用 | 使用 DD+ 音频播放内容。 |
| 选项 |
| 启发式配置文件 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | 更改启发式配置文件 |
| 本地化 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
设置本地化 |
| 音频轨道菜单 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
用于显示音频轨道菜单在默认外观上的样式的选项。 |
| 热键 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | 此示例演示如何配置在播放器中启用哪些热键 |
| 事件、日志记录和诊断 |
| 注册事件 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | 不适用 | 使用事件侦听器播放。 |
| 日志记录 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | 打开控制台详细日志记录。 |
| 诊断 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | 不适用 | 获取诊断数据。 此示例仅适用于某些技术。 |
| AES |
| AES 无令牌 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | 播放无令牌的 AES 内容。 |
| AES 令牌 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | 播放有令牌的 AES 内容。 |
| AES HLS 代理模拟 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | 播放有令牌的 AES 内容，显示 HLS 的代理，以便令牌可用于 iOS 设备。 |
| AES 令牌强制闪存 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | 播放有令牌的 AES 内容，并强制使用 flashSS 技术。 |
| DRM |
| 使用 PlayReady、Widevine 和 FairPlay 的多种 DRM | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | 播放无令牌、具有 PlayReady、Widevine 和 FairPlay 标头的 DRM 内容。 |
| PlayReady 无令牌 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | 播放无令牌的 PlayReady 内容。 |
| PlayReady 无令牌强制使用 Silverlight | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | 播放无令牌的 PlayReady 内容，强制使用 silverlightSS 技术。 |
| PlayReady 令牌 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | 播放有令牌的 PlayReady 内容。 |
| PlayReady 令牌强制 Silverlight | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | 播放有令牌的 PlayReady 内容，强制使用 silverlightSS 技术。 |
| 协议和技术 |
| 更改 techOrder | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | 更改技术订单 |
| 仅在 UrlRewriter 中强制使用 MPEG-DASH | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | 仅使用 DASH 协议播放未受保护内容。 |
| 在 UrlRewriter 中排除 MPEG-DASH | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | 仅使用 Smooth 和 HLS 协议播放未受保护内容。 |
| 多个交付策略 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [静态](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | 使用具有 Azure 媒体服务中的多个交付策略的内容设置源 |
| 以编程方式选择 |
| 选择文本轨道 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | 不适用 | 从跟踪列表中选择 WebVTT 轨道。 |
| 选择比特率 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | 不适用 | 从比特率列表中选择比特率。 此示例仅适用于某些技术。 |
| 选择音频流 | [动态](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | 不适用 | 从可用音频流列表中选择音频流。 此示例仅适用于某些技术。 |

## <a name="next-steps"></a>后续步骤

<!---Some context for the following links goes here--->
- [Azure Media Player 快速入门](azure-media-player-quickstart.md)