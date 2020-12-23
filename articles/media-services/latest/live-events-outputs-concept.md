---
title: Azure 媒体服务 v3 中的实时事件和实时输出概念
titleSuffix: Azure Media Services
description: 本主题概述 Azure 媒体服务 v3 中的直播活动和实时输出。
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
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: a74dcb3cae74605e747a63f8fbb102404d8cc80e
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741818"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>媒体服务中的实时事件和实时输出

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒体服务可将实时事件传送到 Azure 云中的客户。 若要在媒体服务 v3 中设置实时传送视频流事件，需了解本文中所述的概念。

> [!TIP]
> 对于从媒体服务 v2 Api 迁移的客户， **live 事件** 实体将替换 v2 中的 **通道** **，并替换** **program**。

## <a name="live-events"></a>直播活动

[直播活动](/rest/api/media/liveevents)负责引入和处理实时视频源。 当你创建活动事件时，将创建一个主输入终结点和辅助输入终结点，你可以使用该终结点从远程编码器发送实时信号。 远程实时编码器使用 [RTMP](https://www.adobe.com/devnet/rtmp.html) 或[平滑流式处理](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)（分段 MP4）输入协议将贡献源发送到该输入终结点。 对于 RTMP 引入协议，内容可以通过明文 (`rtmp://`) 或网络安全加密 (`rtmps://`) 的方式发送。 对于平滑流式处理引入协议，支持的 URL 方案为 `http://` 或 `https://`。  

## <a name="live-event-types"></a>实时事件类型

可以将 [实时事件](/rest/api/media/liveevents) 设置为 *传递* (本地实时编码器将多比特率流发送) 或 *实时编码* ， (本地实时编码器发送单比特率流) 。 这些类型是在创建期间使用 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype) 设置的：

* **LiveEventEncodingType.None**：本地实时编码器发送多比特率流。 引入流通过实时事件而不进行任何进一步处理。 也称为“直通模式”。
* **LiveEventEncodingType**：本地实时编码器将单比特率流发送到实时事件，媒体服务将创建多比特率流。 如果贡献源的分辨率为 720p 或更高，则 **Default720p** 预设将编码一组 6 分辨率/比特率对。
* **LiveEventEncodingType. Premium1080p**：本地实时编码器将单比特率流发送到实时事件，媒体服务将创建多比特率流。 Default1080p 预设指定分辨率/比特率对的输出集。

### <a name="pass-through"></a>直通

![使用媒体服务的传递实时事件示例关系图](./media/live-streaming/pass-through.svg)

使用传递 **实时事件** 时，你依赖于本地实时编码器生成多比特率视频流，并使用 RTMP 或零散的协议) 将该内容作为贡献源发送到实时事件 (。 实时事件随后会经历传入视频流，而不会进行任何进一步处理。 此类直通实时事件针对长时间运行的实时事件或24x365 线性实时流进行了优化。 创建此类型的实时事件时，请指定 None (LiveEventEncodingType) 。

发送的贡献源的最高分辨率可为 4K，帧速率可为 60 帧/秒，采用 H.264/AVC 或 H.265/HEVC 视频编解码器，以及 AAC（AAC-LC、HE-AACv1 或 HE-AACv2）音频编解码器。 有关详细信息，请参阅 [实时事件类型比较](live-event-types-comparison.md)。

> [!NOTE]
> 当你要在很长一段时间内进行多个活动，并且已在本地编码器上进行投入时，使用直通方法是进行实时传送视频流的最经济方式。 请参阅 [定价](https://azure.microsoft.com/pricing/details/media-services/) 详细信息。
>

请参阅 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126) 中的 .NET 代码示例。

### <a name="live-encoding"></a>实时编码  

![使用媒体服务的实时编码示例图](./media/live-streaming/live-encoding.svg)

在媒体服务中使用实时编码时，可以配置本地实时编码器，以便将单比特率视频作为发布事件的发布源发送到实时事件 (使用 RTMP 或 Fragmented-Mp4 协议) 。 然后，设置一个实时事件，使其将传入单比特率流编码为 [多比特率视频流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，并使输出可用于通过诸如 MPEG-短线、HLS 和平滑流式处理等协议来播放设备。

使用实时编码时，可发送的贡献源最高分辨率只能是 1080p，帧速率是 30 帧/秒，采用 H.264/AVC 视频编解码器，以及 AAC（AAC-LC、HE-AACv1 或 HE-AACv2）音频编解码器。 请注意，传递实时事件可支持最大为 4K 60 帧/秒的分辨率。 有关详细信息，请参阅 [实时事件类型比较](live-event-types-comparison.md)。

实时编码器的输出中包含的分辨率和比特率由预设确定。 如果使用 **Standard** 实时编码器 (LiveEventEncodingType.Standard)，*Default720p* 预设将指定一组 6 分辨率/比特率对，其最高分辨率为 720p，最高比特率为 3.5 Mbps，最低分辨率为 192p，最低比特率为 200 kbps。 否则，如果使用 **Premium1080p** 实时编码器 (LiveEventEncodingType.Premium1080p)，*Default1080p* 预设将指定一组 6 分辨率/比特率对，其最高分辨率为 1080p，最高比特率为 3.5 Mbps，最低分辨率为 180p，最低比特率为 200 kbps。 有关信息，请参阅[系统预设](live-event-types-comparison.md#system-presets)。

> [!NOTE]
> 如果需要自定义实时编码预设，请通过 Azure 门户开具支持票证。 指定所需的分辨率和比特率的表。 确认只有一个层的分辨率为 720p（如果请求 Standard 实时编码器的预设）或 1080p（如果请求 Premium1080p 实时编码器的预设），且最多有 6 个层。

## <a name="creating-live-events"></a>创建实时事件

### <a name="options"></a>选项

创建实时事件时，可以指定以下选项：

* 可以为实时事件指定名称和说明。
* 云编码包括传递 (没有云编码) 、标准 (最高为 720p) 或高级 (最多可达 1080p) 。 对于标准和高级编码，可以选择编码视频的 stretch 模式。
  * 无：严格考虑编码预设中指定的输出分辨率，而不考虑像素纵横比或显示输入视频的纵横比。
  * 自动调整大小：覆盖输出分辨率，并将其更改为与输入的显示纵横比（不含边距）相匹配。 例如，如果输入为1920x1080，编码预设要求1280x1280，则将重写预设中的值，输出将为1280x720，这将保持输入纵横比为16:9。
  * 自动调整：通过 "黑边" 或 "支柱" 框) 来填充输出 (，同时确保输出中的活动视频区域与输入具有相同的纵横比。 例如，如果输入为1920x1080，并且编码预设要求使用1280x1280，则输出将为1280x1280，其中包含16:9 的内部矩形，在右下方为280像素宽的支柱框区域。
* 目前 (传输协议，) 支持 RTMP 和平滑流式处理协议。 当实时事件或其关联的实时输出正在运行时，不能更改协议选项。 如果需要不同的协议，请为每个流式处理协议创建一个单独的实时事件。
* 输入 ID，它是实时事件输入流的全局唯一标识符。
* 静态 hostname 前缀，其中包括 "无" (，在这种情况下，将使用随机128位十六进制字符串) ，使用 "实时事件名称" 或 "使用自定义名称"。  选择使用客户名称时，此值为自定义主机名前缀。
* 可以通过设置输入关键帧间隔（HLS 输出中每个媒体段的持续时间 (以秒为单位) ），来减少实时广播与播放之间的端到端延迟。 该值应为介于0.5 到20秒范围内的非零整数。  如果未设置 *任何* 输入或输出关键帧间隔，则该值默认为2秒。 仅在传递事件中允许使用关键帧间隔。
* 创建事件时，可以将其设置为自动启动。 如果 "自动启动" 设置为 "true"，则实时事件将在创建后启动。 实时事件开始运行后，就会开始计费。 必须显式对直播活动资源调用停止操作才能停止进一步计费。 或者，可以在准备好开始流式传输后，启动事件。

> [!NOTE]
> 对于标准和高级编码，最大帧速率为 30 fps。

## <a name="standby-mode"></a>备用模式

创建实时事件时，可以将其设置为备用模式。 当事件处于待机模式时，您可以编辑说明、静态主机名前缀并限制输入和预览访问设置。  备用模式仍为计费模式，但价格不同于启动实时流。

有关详细信息，请参阅 [实时事件状态和计费](live-event-states-billing.md)。

* 对引入和预览的 IP 限制。 你可以定义允许将视频引入到此实时事件的 IP 地址。 允许的 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.252.0)”）。
<br/><br/>
如果未指定 IP 地址并且没有规则定义，则不会允许任何 IP 地址。 若要允许任何 IP 地址，请创建一个规则并设置 0.0.0.0/0。<br/>IP 地址必须采用以下格式之一：具有四个数字或 CIDR 地址范围的 IpV4 地址。
<br/><br/>
如果要在自己的防火墙上启用某些 IP，或者要将直播活动的输入约束到 Azure IP 地址，请从 [Azure 数据中心 IP 地址范围](https://www.microsoft.com/download/details.aspx?id=41653)下载 JSON 文件。 有关此文件的详细信息，请选择页面上的“详细信息”部分。 

* 创建事件时，可以选择打开 "实时转录"。 默认情况下，将禁用实时脚本。 有关实时脚本的详细信息，请阅读 [实时](live-transcription.md)脚本。

### <a name="naming-rules"></a>命名规则

* 最大直播活动名称为 32 个字符。
* 该名称应遵循此[正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)模式：`^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`。

另请参阅[流式处理终结点命名约定](streaming-endpoint-concept.md#naming-convention)。

> [!TIP]
> 为了保证直播活动名称的唯一性，可以生成 GUID，并删除所有连字符和大括号（如果有）。 该字符串在所有直播活动中都是唯一的，并且其长度保证为 32。

## <a name="live-event-ingest-urls"></a>实时事件引入 Url

创建实时事件后，可以获取将提供给实时本地编码器的引入 Url。 实时编码器使用这些 URL 来输入实时流。 有关详细信息，请参阅[建议的本地实时编码器](recommended-on-premises-live-encoders.md)。

>[!NOTE]
> 从 2020-05-01 API 版本，虚 Url 称为静态主机名

可以使用非虚 URL 或虚 URL。

> [!NOTE]
> 要使引入 URL 具有预测性，请设置“vanity”模式。

* 非虚 URL

    在媒体服务 v3 中，非虚 URL 是默认模式。 可能很快就会获得实时事件，但只有在启动实时事件时才会知道摄取 URL。 如果停止/启动实时事件，则 URL 将更改。 非虚 URL 适用于这样的情况：最终用户希望使用应用进行流式处理，而应用希望尽快获取实时事件，并且可以使用动态引入 URL。

    如果客户端应用在创建实时事件之前不需要预先生成摄取 URL，则允许 Media Services 自动生成实时事件的访问令牌。

* 虚 URL

    虚模式是使用硬件广播编码器且不希望在启动实时事件时重新配置其编码器的大型媒体广播程序的首选模式。 这些广播公司需要一个不随时间变化的预测性引入 URL。

    > [!NOTE]
    > 在 Azure 门户中，虚 URL 的名称为 "*Static hostname prefix*"。

    若要在 API 中指定此模式，请在创建时将 `useStaticHostName` 设为 `true`（默认值为 `false`）。 当 `useStaticHostname` 设置为 true 时，将 `hostnamePrefix` 指定分配给实时事件预览和摄取终结点的主机名的第一部分。 最终主机名将是此前缀、媒体服务帐户名称和 Azure 媒体服务数据中心的简短代码的组合。

    若要避免 URL 中出现随机标记，还需要在创建时将自己的访问令牌传递 (`LiveEventInput.accessToken`) 。  访问令牌必须是有效的 GUID 字符串（带或不带连字符）。 一旦设置模式，就无法将其更新。

    访问令牌在数据中心内必须是唯一的。 如果应用需要使用虚 URL，我们建议始终为访问令牌创建新的 GUID 实例（而不要重复使用任何现有的 GUID）。

    使用以下 API 启用虚 URL，并将访问令牌设置为有效的 GUID（例如 `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`）。  

    |语言|启用虚 URL|设置访问令牌|
    |---|---|---|
    |REST|[properties.vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent.md?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>实时引入 URL 命名规则

* 下面的随机  字符串是一个 128 位的十六进制数字（由 32 个 0-9 a-f 字符组成）。
* *访问令牌*：使用虚模式时设置的有效 GUID 字符串。 例如，`"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`。
* *流名称*：指示特定连接的流名称。 流名称值通常由使用的实时编码器添加。 可将实时编码器配置为使用任何名称来描述连接，例如：“video1_audio1”、“video2_audio1”、“stream”。

#### <a name="non-vanity-url"></a>非虚 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>顺畅流式处理

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>虚 URL

在以下路径中， `<live-event-name>` 表示为事件指定的名称或在创建实时事件时使用的自定义名称。

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>顺畅流式处理

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>实时事件预览 URL

实时事件开始接收 "发布内容" 源后，你可以使用其预览终结点预览并验证你在进一步发布之前是否正在接收实时流。 在检查预览流是否良好后，可以使用 live 事件，通过一个或多个) 流式处理终结点创建一个或多个 (来提供实时流。 若要实现此目的，请在实时事件上创建新的 [实时输出](/rest/api/media/liveoutputs) 。

> [!IMPORTANT]
> 确保视频流向预览 URL，然后再继续操作！

## <a name="live-event-long-running-operations"></a>实时事件长时间运行的操作

有关详细信息，请参阅[长时间运行的操作](media-services-apis-overview.md#long-running-operations)。

## <a name="live-outputs"></a>实时输出

将流流入实时事件后，可以通过创建 [资产](/rest/api/media/assets)、 [实时输出](/rest/api/media/liveoutputs)和 [流式处理定位符](/rest/api/media/streaminglocators)来开始流式处理事件。 实时输出将存档流，并通过 [流式处理终结点](/rest/api/media/streamingendpoints)将其提供给查看者。  

有关实时输出的详细信息，请参阅 [使用云 DVR](live-event-cloud-dvr.md)。

## <a name="frequently-asked-questions"></a>常见问题

请参阅[常见问题解答](frequently-asked-questions.md#live-streaming)一文。

## <a name="ask-questions-and-get-updates"></a>提出问题并获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

[实时传送视频流教程](stream-live-tutorial-with-api.md)
