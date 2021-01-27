---
标题： Azure 媒体服务 v3 中的实时事件和实时输出概念： Azure 媒体服务说明：本主题提供 Azure 媒体服务 v3 中的实时事件和实时输出的概述。
服务：媒体服务 documentationcenter： ' ' author： IngridAtMicrosoft manager： femila editor： ' '

ms. service： media services ms. 工作负荷： media ms.tgt_pltfrm： na ms.devlang： ne ms。主题：概念性 ms. 日期：10/23/2020 毫秒。作者： inhenkel

---
# <a name="live-events-and-live-outputs-in-media-services"></a>媒体服务中的实时事件和实时输出

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒体服务可将实时事件传送到 Azure 云中的客户。 若要在媒体服务 v3 中设置实时传送视频流事件，需了解本文中所述的概念。

> [!TIP]
> 对于从媒体服务 v2 API 迁移的客户，“实时事件”实体取代了 v2 中的“频道”，“实时输出”取代了“节目”。   

## <a name="live-events"></a>直播活动

[直播活动](/rest/api/media/liveevents)负责引入和处理实时视频源。 创建实时事件时，会创建一个主辅输入终结点，可以使用它从远程编码器发送实时信号。 远程实时编码器使用 [RTMP](https://www.adobe.com/devnet/rtmp.html) 或[平滑流式处理](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)（分段 MP4）输入协议将贡献源发送到该输入终结点。 对于 RTMP 引入协议，内容可以通过明文 (`rtmp://`) 或网络安全加密 (`rtmps://`) 的方式发送。 对于平滑流式处理引入协议，支持的 URL 方案为 `http://` 或 `https://`。  

## <a name="live-event-types"></a>实时事件类型

[实时事件](/rest/api/media/liveevents)可以设置为“直通”（本地实时编码器发送多比特率流）或“实时编码”（本地实时编码器发送单比特率流）。 这些类型是在创建期间使用 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype) 设置的：

* **LiveEventEncodingType.None**：本地实时编码器发送多比特率流。 引入的流直接通过实时事件，而不会经过任何进一步的处理。 也称为“直通模式”。
* **LiveEventEncodingType.Standard**：本地实时编码器将单比特率流发送到实时事件，媒体服务创建多比特率流。 如果贡献源的分辨率为 720p 或更高，则 **Default720p** 预设将编码一组 6 分辨率/比特率对。
* **LiveEventEncodingType.Premium1080p**：本地实时编码器将单比特率流发送到实时事件，媒体服务创建多比特率流。 Default1080p 预设指定分辨率/比特率对的输出集。

### <a name="pass-through"></a>直通

![使用媒体服务的直通实时事件示例图](./media/live-streaming/pass-through.svg)

使用直通 **实时事件**，可以依赖本地实时编码器生成多比特率视频流，并将其作为贡献源发送到实时事件（使用 RTMP 或分段 MP4 协议）。 然后，实时事件会接受传入视频流，不对其进行进一步的处理。 此类直通实时事件已针对长时间运行的实时事件或 24x365 线性实时传送视频流进行优化。 创建此类实时事件时，请指定 None (LiveEventEncodingType.None)。

发送的贡献源的最高分辨率可为 4K，帧速率可为 60 帧/秒，采用 H.264/AVC 或 H.265/HEVC 视频编解码器，以及 AAC（AAC-LC、HE-AACv1 或 HE-AACv2）音频编解码器。 有关详细信息，请参阅[实时事件类型的比较](live-event-types-comparison.md)。

> [!NOTE]
> 当你要在很长一段时间内进行多个活动，并且已在本地编码器上进行投入时，使用直通方法是进行实时传送视频流的最经济方式。 请参阅[定价](https://azure.microsoft.com/pricing/details/media-services/)详细信息。
>

请参阅 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126) 中的 .NET 代码示例。

### <a name="live-encoding"></a>实时编码  

![使用媒体服务的实时编码示例图](./media/live-streaming/live-encoding.svg)

将实时编码与媒体服务配合使用时，需配置本地实时编码器，以便将单比特率视频作为贡献源发送到实时事件（使用 RTMP 或分段 MP4 协议）。 然后设置实时事件，以将该传入的单比特率流编码为[多比特率视频流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，并使输出可通过 MPEG-DASH、HLS 和平滑流式处理等协议传送到播放设备。

使用实时编码时，可发送的贡献源最高分辨率只能是 1080p，帧速率是 30 帧/秒，采用 H.264/AVC 视频编解码器，以及 AAC（AAC-LC、HE-AACv1 或 HE-AACv2）音频编解码器。 请注意，直通实时事件支持的最大分辨率为 4K（60 帧/秒）。 有关详细信息，请参阅[实时事件类型的比较](live-event-types-comparison.md)。

实时编码器的输出中包含的分辨率和比特率由预设确定。 如果使用 **Standard** 实时编码器 (LiveEventEncodingType.Standard)，*Default720p* 预设将指定一组 6 分辨率/比特率对，其最高分辨率为 720p，最高比特率为 3.5 Mbps，最低分辨率为 192p，最低比特率为 200 kbps。 否则，如果使用 **Premium1080p** 实时编码器 (LiveEventEncodingType.Premium1080p)，*Default1080p* 预设将指定一组 6 分辨率/比特率对，其最高分辨率为 1080p，最高比特率为 3.5 Mbps，最低分辨率为 180p，最低比特率为 200 kbps。 有关信息，请参阅[系统预设](live-event-types-comparison.md#system-presets)。

> [!NOTE]
> 如果需要自定义实时编码预设，请通过 Azure 门户开具支持票证。 指定所需的分辨率和比特率的表。 确认只有一个层的分辨率为 720p（如果请求 Standard 实时编码器的预设）或 1080p（如果请求 Premium1080p 实时编码器的预设），且最多有 6 个层。

## <a name="creating-live-events"></a>创建实时事件

### <a name="options"></a>选项

创建实时事件时，可以指定以下选项：

* 可以为实时事件指定名称和说明。
* 云编码包括直通（无云编码）、标准（最高 720p）或高级（最高 1080p）。 对于标准和高级编码，你可以选择已编码视频的拉伸模式。
  * 无：严格遵循编码预设中指定的输出分辨率，而不考虑输入视频的像素纵横比或显示纵横比。
  * AutoSize：替代输出分辨率，并对其进行更改以匹配输入的显示纵横比，无需填充。 例如，如果输入为 1920x1080，而编码预设要求提供 1280x1280，则会重写预设中的值，输出将是 1280x720，这样可以保持 16:9 的输入纵横比。
  * AutoFit：根据输出分辨率填充输出（使用上下黑边或左右黑边），同时确保输出中活动视频区域的纵横比与输入相同。 例如，如果输入为 1920x1080，并且编码预设要求提供 1280x1280，则输出将是 1280x1280，其中包含纵横比为 16:9 的 1280x720 内部矩形，并在左侧和右侧包含宽度为 280 像素的左右黑边区域。
* 流式处理协议（目前支持 RTMP 和平滑流式处理协议）。 运行实时事件或其关联的实时输出时，无法更改协议选项。 如果需要不同的协议，请为每个流式处理协议创建单独的实时事件。
* 输入 ID，它是实时事件输入流的全局独一无二的标识符。
* 包含 None 的静态主机名前缀（在本例中，将使用随机的 128 位十六进制字符串），使用实时事件名称，或使用自定义名称。  选择使用自定义名称时，此值为自定义主机名前缀。
* 你可以通过设置 HLS 输出中每个媒体段的输入关键帧间隔（以秒为单位的持续时间）来减少实时广播与播放之间的端到端延迟。 该值应当为 0.5 秒到 20 秒范围内的非零整数。  如果输入或输出关键帧间隔均未设置，则该值默认为 2 秒。 只有直通事件中允许设置关键帧间隔。
* 创建事件时，可以将其设置为自动启动。 如果将 autostart 设置为 true，则实时事件会在创建后启动。 只要实时事件开始运行，就会开始计费。 必须显式对直播活动资源调用停止操作才能停止进一步计费。 或者，可以在准备好开始流式传输后，启动事件。

> [!NOTE]
> 对于标准和高级编码，最大帧速率为 30 fps。

## <a name="standby-mode"></a>备用模式

创建实时事件时，可以将其设置为备用模式。 当事件处于备用模式时，你可以编辑“说明”、“静态主机名前缀”并限制输入和预览访问设置。  备用模式仍为可计费模式，但价格不同于启动实时传送流时的价格。

有关详细信息，请参阅[实时事件状态和计费](live-event-states-billing.md)。

* 对引入和预览的 IP 限制。 可定义允许向该实时事件引入视频的 IP 地址。 允许的 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.252.0)”）。
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

## <a name="live-event-ingest-urls"></a>实时事件引入 URL

创建实时事件后，可以获得要提供给实时本地编码器的引入 URL。 实时编码器使用这些 URL 来输入实时流。 有关详细信息，请参阅[建议的本地实时编码器](recommended-on-premises-live-encoders.md)。

>[!NOTE]
> 从 2020-05-01 API 版本开始，虚 URL 称为静态主机名

可以使用非虚 URL 或虚 URL。

> [!NOTE]
> 要使引入 URL 具有预测性，请设置“vanity”模式。

* 非虚 URL

    在媒体服务 v3 中，非虚 URL 是默认模式。 你可以快速获取实时事件，但只有在实时事件启动后，才会知道引入 URL。 如果停止/启动实时事件，此 URL 会更改。 非虚 URL 适用于这样的情况：最终用户希望使用应用进行流式处理，而应用希望尽快获取实时事件，并且可以使用动态引入 URL。

    如果客户端应用在创建实时事件之前不需要预生成引入 URL，请让媒体服务自动生成实时事件的访问令牌。

* 虚 URL

    对于使用硬件广播编码器且不希望在启动实时事件时重新配置其编码器的大型媒体广播者来说，虚模式是首选。 这些广播公司需要一个不随时间变化的预测性引入 URL。

    > [!NOTE]
    > 在 Azure 门户中，虚 URL 称为“静态主机名前缀”。

    若要在 API 中指定此模式，请在创建时将 `useStaticHostName` 设为 `true`（默认值为 `false`）。 如果 `useStaticHostname` 设置为 true，则 `hostnamePrefix` 指定分配给实时事件预览和引入终结点的主机名的第一部分。 最终主机名将是此前缀、媒体服务帐户名称和 Azure 媒体服务数据中心的短代码的组合。

    为了避免 URL 中出现随机令牌，在创建时还需要传递你自己的访问令牌 (`LiveEventInput.accessToken`)。  访问令牌必须是有效的 GUID 字符串（带或不带连字符）。 一旦设置模式，就无法将其更新。

    访问令牌在数据中心内必须是唯一的。 如果应用需要使用虚 URL，我们建议始终为访问令牌创建新的 GUID 实例（而不要重复使用任何现有的 GUID）。

    使用以下 API 启用虚 URL，并将访问令牌设置为有效的 GUID（例如 `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`）。  

    |语言|启用虚 URL|设置访问令牌|
    |---|---|---|
    |REST|[properties.vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

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

在以下路径中，`<live-event-name>` 表示为事件指定的名称或在创建实时事件时使用的自定义名称。

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>顺畅流式处理

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>实时事件预览 URL

一旦实时事件开始接收贡献源，你就可以使用其预览终结点进行预览，并在进一步发布之前验证是否可以收到实时传送流。 确认预览流正常后，可以使用实时事件，以便通过一个或多个（预先创建的）流式处理终结点传送实时传送流。 为此，请针对实时事件创建新的[实时输出](/rest/api/media/liveoutputs)。

> [!IMPORTANT]
> 确保视频流向预览 URL，然后再继续操作！

## <a name="live-event-long-running-operations"></a>实时事件的长时间运行的操作

有关详细信息，请参阅[长时间运行的操作](media-services-apis-overview.md#long-running-operations)。

## <a name="live-outputs"></a>实时输出

将流传输到实时事件后，可以通过创建[资产](/rest/api/media/assets)、[实时输出](/rest/api/media/liveoutputs)和[流定位符](/rest/api/media/streaminglocators)来启动流事件。 实时输出会存档流，并使观看者可通过[流式处理终结点](/rest/api/media/streamingendpoints)使用该流。  

有关实时输出的详细信息，请参阅[使用云 DVR](live-event-cloud-dvr.md)。

## <a name="frequently-asked-questions"></a>常见问题

请参阅[常见问题解答](frequently-asked-questions.md#live-streaming)一文。

## <a name="ask-questions-and-get-updates"></a>提出问题并获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

[实时传送视频流教程](stream-live-tutorial-with-api.md)
