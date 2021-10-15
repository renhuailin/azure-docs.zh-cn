---
title: Azure 媒体服务直播活动错误代码
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
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 24428aeb07548556a96c292b6dc1be0f2014a479
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389829"
---
# <a name="media-services-live-event-error-codes"></a>媒体服务直播活动错误代码

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

下表列出了[直播活动](live-event-outputs-concept.md)错误代码。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

订阅直播活动的[事件网格](../../event-grid/index.yml)事件时，可以从 [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) 事件中看到以下错误之一。
> [!div class="mx-tdCol2BreakAll"]
>| 错误 | 信息 |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|说明 | 引入 URL 不正确 |
>|建议的解决方案| APPID 是 RTMP 引入 URL 中的 GUID 令牌。 请确保它与 API 中的引入 URL 相匹配。 |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| 说明 |编码器 IP 在配置的 IP 允许列表中不存在 |
>| 建议的解决方案| 请确保编码器的 IP 位于 IP 允许列表中。 使用联机工具（如“whoismyip”或“CIDR 计算器”）设置正确的值。  请确保编码器可以在实际直播活动开始之前连接到服务器。 |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| 说明|RTMP 编码器未发送 `setDataFrame` 命令。 |
>| 建议的解决方案|多数商业编码器发送流元数据。 对于推送单比特率引入的编码器，这可能不是问题。 缺少流元数据时，LiveEvent 能够计算传入比特率。  对于 PassThru 通道或双重推送方案的多比特率引入，可以尝试在引入 URL 中追加含有“videodatarate”和“audiodatarate”的查询字符串。 近似值可能有效。 单位为 Kbit。 例如  `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| 说明|不支持指定的编解码器。|
>| 建议的解决方案| LiveEvent 收到不支持的编解码器。 例如，RTMP 引入时，LiveEvent 收到非 AVC 视频编解码器。  检查编码器预设。 |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| 说明 |在实际媒体数据送达之前未收到媒体说明信息。 |
>| 建议的解决方案|LiveEvent 不会从编码器接收流说明（标头或 FLV 标记）。 这违反了协议。 联系编码器供应商。 |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| 说明| 音频或视频类型的质量级别计数超过了允许的最大限制。 超出限制的质量级别被服务忽略。|
>| 建议的解决方案|当直播活动处于“实时编码”模式时，编码器应推送单比特率的视频和音频。  请注意，系统允许使用同一比特率进行冗余推送。 检查编码器预设或输出设置，确保它输出的是单比特率流。 |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| 说明|直播活动或频道服务中的总传入比特率超过了允许的最大限制。 |
>| 建议的解决方案|编码器超过了最大传入比特率。 此限制将聚合从参与编码器传入的所有数据。 检查编码器预设或输出设置，从而降低比特率。 |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| 说明|视频或音频 FLVTag 的时间戳在 RTMP 编码器中无效。 |
>| 建议的解决方案|已弃用。 |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| 说明|传入的编码器引入流的帧速率超过了直播活动/频道编码的最大允许值，即 30 fps。 |
>| 建议的解决方案|检查编码器预设，使帧速率降低至 36 fps 以下。 |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| 说明|传入的编码器引入流超过了以下允许的分辨率：1920x1088（直播活动/频道编码）和 4096x2160（基础和标准直接传递直播活动/频道编码）。 |
>| 建议的解决方案|检查编码器预设，以降低视频分辨率，使其不超过限值。 |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| 说明|直播活动同时接收了大量音频数据或大量没有任何关键帧的视频数据。 已断开编码器连接，允许它使用正确数据重试。 |
>| 建议的解决方案|确保编码器为每个关键帧间隔 (GOP) 发送关键帧。  启用“恒定比特率 (CBR)”或“对齐关键帧”等设置。 有时，可尝试重置参与编码器。 如果不起作用，请联系编码器供应商。 |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

你可能会从 [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) 事件中看到以下错误之一。

> [!div class="mx-tdCol2BreakAll"]
>| 错误 | 信息 |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| 说明|在空闲时间达到允许的时间限制后，RTMP 会话超时。 |
>|建议的解决方案|当编码器停止接收输入源，导致无数据可推送，会话被闲置，此时通常会出现该错误。请注意检查编码器或输入源状态是否正常。 |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|说明| RTMP 编码器中视频或音频 FLVTag 的时间戳无效。 |
>| 建议的解决方案| 已弃用。 |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| 说明|编码器发送数据的频率过快。 |
>| 建议的解决方案|如果编码器在短时间内突发了大量片段，就会发生这种情况。  理论上，若编码器因网络问题暂时无法推送数据，而且在网络连通时产生了突发数据，就会发生这种情况。 从编码器日志或系统日志查找原因。 |
>|**未知错误代码** |
>| 说明| 这些错误代码的范围可能包括内存错误、哈希映射中包含重复条目，等等。 如果编码器在短时间内发送了大量片段，就会发生这种情况。  若编码器因网络问题暂时无法推送数据，并在网络连通时同时发送所有延迟片段，也可能会发生这种情况。 |
>|建议的解决方案| 检查编码器日志。|

## <a name="other-error-codes"></a>其他错误代码

> [!div class="mx-tdCol2BreakAll"]
>| 错误 | 信息 |已拒绝/已断开连接活动|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||是|
>| 说明|这属于常规错误。 ||
>|建议的解决方案| 无。||
>|**MPI_SYSTEM_MAINTENANCE** ||是|
>| 说明|由于服务更新或系统维护，编码器已断开连接。 ||
>|建议的解决方案|请确保编码器启用了“自动连接”。 它允许编码器重新连接到未进行维护的冗余直播活动终结点。 ||
>|**MPE_BAD_URL_SYNTAX** ||是|
>| 说明|引入 URL 格式不正确。 ||
>|建议的解决方案|请确保引入 URL 格式正确。 对于 RTMP，它应该是 `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||是|
>| 说明|编码器已断开会话连接。  ||
>|建议的解决方案|这不是错误。 编码器发起断开连接，包括不正常断开。 若此次是意外断开连接，请检查编码器日志。 |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||否|
>| 说明|传入数据速率与预期比特率不匹配。 ||
>|建议的解决方案|这是一条警告消息，数据传入速率太慢或太快时都会发生这种情况。 请检查编码器日志或系统日志。||
>|**MPE_INGEST_DISCONTINUITY** ||否|
>| 说明| 传入数据不连续。||
>|建议的解决方案| 这是一条警告消息，警示用户该编码器因网络问题或系统资源问题丢失了数据。 请检查编码器日志或系统日志。 并监视系统资源（CPU、内存或网络）。 如果系统 CPU 占用过高，请尝试降低比特率，或使用系统显卡中的 H/W 编码器选项。||

## <a name="see-also"></a>另请参阅

[流式处理终结点（来源）错误代码](stream-streaming-endpoint-error-codes-reference.md)

## <a name="next-steps"></a>后续步骤

[教程：使用媒体服务进行实时流式传输](stream-live-tutorial-with-api.md)
