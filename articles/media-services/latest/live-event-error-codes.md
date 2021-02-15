---
title: Azure 媒体服务实时事件错误代码
description: 本文列出了直播活动错误代码。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 02/12/2020
ms.author: inhenkel
ms.openlocfilehash: b3be465c488bdd3c5dbd62f757733939d1bee393
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393507"
---
# <a name="media-services-live-event-error-codes"></a>媒体服务直播活动错误代码

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

下表列出了 [实时事件](live-events-outputs-concept.md) 错误代码。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

当你订阅实时事件的 [事件网格](../../event-grid/index.yml) 事件时，可能会看到 [LiveEventConnectionRejected](media-services-event-schemas.md\#liveeventconnectionrejected) 事件出现以下错误之一。
> [!div class="mx-tdCol2BreakAll"]
>| 错误 | 信息 |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|说明 | 引入 URL 不正确 |
>|建议的解决方案| APPID 是 RTMP 摄取 URL 中的 GUID 令牌。 请确保它与来自 API 的引入 URL 相匹配。 |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| 说明 |编码器 IP 不在配置的 IP 允许列表中 |
>| 建议的解决方案| 请确保编码器的 IP 位于 IP 允许列表中。 使用联机工具（如 *whoismyip* 或 *CIDR 计算器* ）设置正确的值。  请确保编码器可以在实际实时事件发生之前到达服务器。 |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| 说明|RTMP 编码器未发送 `setDataFrame` 命令。 |
>| 建议的解决方案|大多数商业编码器发送流元数据。 对于推送单比特率引入的编码器，这可能不会出现问题。 当缺少流元数据时，LiveEvent 能够计算传入比特率。  对于 PassThru 通道或双重推送方案的多比特率引入，可以尝试在引入 URL 中追加 "videodatarate" 和 "audiodatarate" 的查询字符串。 大致值可能有效。 单元位于 Kbit 中。 例如  `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| 说明|不支持指定的编解码器。|
>| 建议的解决方案| LiveEvent 收到了不受支持的编解码器。 例如，RTMP 引入，LiveEvent 收到非 AVC 视频编解码器。  检查编码器预设。 |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| 说明 |在实际媒体数据送达之前未收到媒体说明信息。 |
>| 建议的解决方案|LiveEvent 不会从编码器接收 (标头或 FLV 标记) 的流说明。 这违反了协议。 联系编码器供应商。 |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| 说明|音频或视频类型的质量计数超过了允许的最大限制。 |
>| 建议的解决方案|当实时事件模式为实时编码时，编码器应推送单比特率的视频和音频。  请注意，允许使用同一比特率的冗余推送。 检查编码器预设或输出设置，确保它输出单比特率流。 |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| 说明|直播活动或频道服务中的总传入比特率超过了允许的最大限制。 |
>| 建议的解决方案|编码器超过了最大传入比特率。 此限制将聚合来自贡献编码器的所有传入数据。 检查编码器预设值或输出设置以降低比特率。 |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| 说明|视频或音频 FLVTag 的时间戳在 RTMP 编码器中无效。 |
>| 建议的解决方案|已否决。 |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| 说明|帧速率为传入编码器的引入流超过允许的最大 30 fps，用于对实时事件/频道进行编码。 |
>| 建议的解决方案|检查编码器预设值以降低帧速率，使其低于 36 fps。 |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| 说明|传入的编码器引入流超过了以下允许的分辨率：1920x1088（直播活动/频道编码）和 4096x2160（直接传递直播活动/频道编码）。 |
>| 建议的解决方案|检查编码器预设以降低视频分辨率，使其不超过限制。 |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| 说明|直播活动同时接收了大量音频数据或大量没有任何关键帧的视频数据。 我们断开了编码器的连接，让它有机会使用正确的数据重试。 |
>| 建议的解决方案|确保编码器为每个关键帧间隔 (GOP) 发送关键帧。  启用 "恒定比特率 (CBR) " 或 "对齐关键帧" 等设置。 有时，重置参与编码器可能会有所帮助。 如果它不起作用，请联系编码器供应商。 |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

你可能会从 [LiveEventEncoderDisconnected](media-services-event-schemas.md\#liveeventencoderdisconnected) 事件中看到以下错误之一。

> [!div class="mx-tdCol2BreakAll"]
>| 错误 | 信息 |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| 说明|在空闲时间达到允许的时间限制后，RTMP 会话超时。 |
>|建议的解决方案|这种情况通常发生在编码器停止接收输入源，以便会话闲置，因为没有要推送的数据。检查编码器或输入馈电状态是否处于正常运行状态。 |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|说明| 视频或音频 FLVTag 的时间戳在 RTMP 编码器中无效。 |
>| 建议的解决方案| 已否决。 |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| 说明|编码器发送数据的频率过快。 |
>| 建议的解决方案|如果编码器在短暂的一段时间内突发了大量片段，就会发生这种情况。  理论上，当编码器由于网络问题而无法推送数据，而在网络可用时产生突发数据时，会发生这种情况。 查找编码器日志或系统日志中的原因。 |
>|**未知错误代码** |
>| 说明| 这些错误代码的范围可能包括内存错误、哈希映射中包含重复条目，等等。 |

## <a name="other-error-codes"></a>其他错误代码

> [!div class="mx-tdCol2BreakAll"]
>| 错误 | 信息 |拒绝/断开连接事件|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||是|
>| 说明|这是一般错误。 ||
>|建议的解决方案| 无。||
>|**MPI_SYSTEM_MAINTENANCE** ||是|
>| 说明|由于服务更新或系统维护，编码器已断开连接。 ||
>|建议的解决方案|请确保编码器启用 "自动连接"。 这是一个编码器功能，用于恢复意外的会话断开连接。 ||
>|**MPE_BAD_URL_SYNTAX** ||是|
>| 说明|摄取 URL 格式不正确。 ||
>|建议的解决方案|请确保引入 URL 格式正确。 对于 RTMP，它应该是 `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||是|
>| 说明|编码器已断开会话连接。  ||
>|建议的解决方案|这不是错误。 这种情况下，编码器启动了断开连接，包括正常断开连接。 如果这是意外的断开连接，请检查编码器日志或系统日志。 |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||否|
>| 说明|传入的数据速率与预期的比特率不匹配。 ||
>|建议的解决方案|这是一条警告，当传入数据速率太慢或速度快时，会发生这种情况。 请检查编码器日志或系统日志。||
>|**MPE_INGEST_DISCONTINUITY** ||否|
>| 说明| 传入数据存在 discontinuty。||
>|建议的解决方案| 这是由于网络问题或系统资源问题导致编码器丢失数据的警告。 检查编码器日志或系统日志。 还监视系统资源 (CPU、内存或网络) 。 如果系统 CPU 太高，请尝试降低比特率，或者使用系统图形卡中的 H/W 编码器选项。||

## <a name="see-also"></a>另请参阅

[流式处理终结点（来源）错误代码](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>后续步骤

[教程：使用媒体服务进行实时流式传输](stream-live-tutorial-with-api.md)
