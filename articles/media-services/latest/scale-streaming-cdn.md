---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>必填字段。 有关详细信息，请参阅 aka.ms/skyeye/meta。
title: 使用 CDN 集成流式处理内容：Azure 媒体服务 description: 了解如何使用 CDN 集成流式处理内容，以及预提取和 Origin-Assist CDN-Prefetch。
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 08/31/2020 ms.author: inhenkel
---

# <a name="stream-content-with-cdn-integration"></a>使用 CDN 集成流式处理内容

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 内容分发网络 (CDN) 为开发人员提供了一个全局解决方案，通过在世界各地按特定策略放置的物理节点缓存内容来快速分发高带宽内容。  

CDN 按照编解码器、流式处理协议、比特率、容器格式和加密/DRM 缓存从媒体服务[流式处理终结点（源）](streaming-endpoint-concept.md)流式传输的内容。 对于每种编解码器-流式处理协议-容器格式-比特率-加密组合，都会单独提供一个 CDN 缓存。

只要缓存了视频片段，就会直接从 CDN 提供受欢迎的内容。 可能会缓存实时内容，因为通常会有很多人观看完全相同的内容。 按需内容可能有点棘手，因为你可能会有一些受欢迎的内容，也有一些不受欢迎的内容。 如果你有数百万的视频资产，其中没有一个是受欢迎的（每周只有一两个观看者），但有成千上万的人观看所有不同的视频，CDN 的效果会大打折扣。

还需要考虑自适应流式处理的工作原理。 每个单独的视频片段都作为自己的实体进行缓存。 例如，假设特定的视频是第一次供人观看。 如果观看者只是跳跃式观看，在每个片段只停留几秒，则 CDN 中只会缓存与该观看者观看的内容关联的视频片段。 使用自适应流式处理，你通常会有 5 到 7 种不同的视频比特率。 如果一个人正在观看一种比特率并且另一个人在观看不同的比特率，则它们会在 CDN 中单独缓存。 即使两个人在观看相同的比特率，他们也可以通过不同的协议进行流式处理。 每个协议（HLS、MPEG-DASH、平滑流式处理）都是单独缓存的。 因此，会单独缓存每个比特率和协议，并且仅缓存已请求的视频片段。

建议为标准和高级流式处理终结点启用 CDN，但测试环境除外。 支持的吞吐量限制因流式处理终结点类型而异。
由于需要考虑各种因素，很难精确计算流式处理终结点支持的最大并发流数。 其中包括:

- 用于流式处理的最大比特率
- 播放器预缓冲和切换行为。 播放器尝试从源突发段，并使用负载速度计算自适应比特率切换。 如果流式处理终结点接近饱和，则响应时间可能会变化，播放器将开始切换到较低的质量。 由于这样减轻了流式处理终结点播放器的负载，因此它将重新调整为更高的质量，从而创建不必要的切换触发器。
总体而言，通过取流式处理终结点最大吞吐量，然后除以最大比特率（假设所有播放器都使用最高比特率）来估算最大并发流数是可靠的做法。例如，可以使用限制为 600 Mbps、最高比特率为 3Mbp 的标准流式处理终结点。 在这种情况下，将以最高比特率支持大约 200 个并发流。 另外请记得考虑音频带宽要求。 尽管可能仅以 128 kps 的带宽就能流式传输某个音频流，但将它乘以并发流数后，流式传输总量很快就会增加。

本主题介绍如何启用 [CDN 集成](#enable-azure-cdn-integration)。 其中还介绍了预提取（主动缓存）和 [Origin-Assist CDN-Prefetch](#origin-assist-cdn-prefetch) 的概念。

## <a name="considerations"></a>注意事项

- 无论你是否启用 CDN，[流式处理终结点](streaming-endpoint-concept.md) `hostname` 和流 URL 都保持不变。
- 如果需要分别使用或不使用 CDN 测试内容，请创建另一个不启用 CDN 的流式处理终结点。

## <a name="enable-azure-cdn-integration"></a>启用 Azure CDN 集成

> [!IMPORTANT]
> 无法为试用版或学生版 Azure 帐户启用 CDN。
>
> 将在所有 Azure 数据中心启用 CDN 集成，但联邦政府区域和中国区域除外。

在启用 CDN 的情况下预配流式处理终结点后，媒体服务上会等待定义的一段时间，然后执行 DNS 更新以将流式处理终结点映射到 CDN 终结点。

如果以后想要禁用/启用 CDN，流式处理终结点必须处于“已停止”状态。 启动流式处理终结点后，最长可能需要经过 4 小时，Azure CDN 集成才会启用，更改才会在所有 CDN POP 中生效。 但是，你可以启动流式处理终结点并进行流式处理，而无需中断流式处理终结点。 集成完成后，将从 CDN 传递流。 在预配期间，流式处理终结点的状态为“正在启动”，可能会观测到性能降低。

创建标准流式处理终结点时，默认会使用标准 Verizon 对其进行配置。 可以使用 REST API 来配置高级 Verizon 或标准 Akamai 提供程序。

标准流式处理终结点可在 **Verizon 提供的 Azure CDN** 上实现 Azure 媒体服务与 Azure CDN 的集成。 可以使用所有 **Azure CDN 定价层和提供程序** 配置高级流式处理终结点。

> [!NOTE]
> 有关 Azure CDN 的详细信息，请参阅 [CDN 概述](../../cdn/cdn-overview.md)。

## <a name="determine-if-a-dns-change-was-made"></a>确定是否已进行 DNS 更改

可以使用 <https://www.digwebinterface.com> 检查是否在流式处理终结点上进行了 DNS 更改（流量是否正在定向到 Azure CDN）。 如果结果中显示了 azureedge.net 域名，则表示流量目前正指向 CDN。

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

CDN 缓存是一个被动式过程。 如果 CDN 可以预测要请求的下一个对象，则 CDN 可以主动请求并缓存下一个对象。 使用此过程可以实现所有（或大多数）对象的缓存命中，从而提高性能。

预提取概念预测玩家会迫切地请求该对象，旨在将对象放在“Internet 的边缘”，因而减少了将该对象传递到播放器的时间。

为实现此目标，流式处理终结点（源）和 CDN 需要通过几种方式协同工作。

- 媒体服务源需要通过“智能”功能 (Origin-Assist) 来告知 CDN 要预提取的下一个对象。
- CDN 执行预提取和缓存（CDN-Prefetch 部分）。 CDN 也需要通过“智能”功能来告知源它执行的是预提取还是一般提取，处理 404 响应，并通过某种方式避免无限预提取循环。

### <a name="benefits"></a>好处

Origin-Assist CDN-Prefetch 功能的优势包括：

- 预提取可以改善视频播放质量，因为在播放过程中它会将预期的视频段预先放置在边缘，从而可以减少观看者遇到的延迟，并缩短视频段下载时间。 这缩短了视频启动时间，并减少了重新缓冲的次数。
- 此概念适用于一般的 CDN 源方案，而不局限于媒体。
- Akamai 已将此功能添加到 [Akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)。

> [!NOTE]
> 此功能尚不适用于与媒体服务流式处理终结点集成的 Akamai CDN。 但是，它适用于已预先签订了 Akamai 合同，并需要在 Akamai CDN 与媒体服务源之间实现自定义集成的媒体服务客户。

### <a name="how-it-works"></a>工作原理

对于直接签订了 Akamai CDN 合同的客户，提供了对 `Origin-Assist CDN-Prefetch` 头的 CDN 支持（适用于实时和按需视频流）。 该功能涉及 Akamai CDN 与媒体服务源之间的以下 HTTP 头交换：

|HTTP 标头|值|发送方|接收方|目的|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1（默认值）或 0 |CDN|源|指示 CDN 已启用预提取。|
|`CDN-Origin-Assist-Prefetch-Path`| 示例： <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|源|CDN|提供 CDN 的预提取路径。|
|`CDN-Origin-Assist-Prefetch-Request`|1（预提取请求）或 0（常规请求）|CDN|源|指示来自 CDN 的请求是预提取。|

若要查看标头交换部分的实际运行情况，可以尝试执行以下步骤：

1. 使用 Postman 或 cURL 向媒体服务源发出对音频或视频段或片段的请求。 请确保在请求中添加 `CDN-Origin-Assist-Prefetch-Enabled: 1` 头。
2. 在响应中，应会看到 `CDN-Origin-Assist-Prefetch-Path` 头，其值为某个相对路径。

### <a name="supported-streaming-protocols"></a>支持的流式处理协议

`Origin-Assist CDN-Prefetch` 功能支持以下用于实时和按需流式处理的流式处理协议：

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* 顺畅流式处理

### <a name="faqs"></a>常见问题

* 如果预提取路径 URL 无效，从而导致 CDN 预提取收到 404 响应，该怎么办？

    CDN 只会缓存 404 响应 10 秒（或配置的其他时间值）。

* 假设你有一个按需视频。 如果已启用 CDN 预提取，在客户端请求第一个视频段后，预提取是否开始循环，以预提取比特率相同的所有后续视频段？

    不是，CDN 预提取仅在客户端发起的请求/响应之后执行。 为了避免预提取循环，预提取永远不会触发 CDN 预提取。

* Origin-Assist CDN-Prefetch 功能是否始终启用？ 如何启用/禁用它？

    默认情况下此功能处于关闭状态。 客户需要通过 Akamai API 启用此功能。

* 对于实时传送视频流，如果下一段或片段尚不可用，Origin-Assist 会发生什么情况？

    在这种情况下，媒体服务源不会提供 `CDN-Origin-Assist-Prefetch-Path` 头，因此不会发生 CDN 预提取。

* `Origin-Assist CDN-Prefetch` 如何使用动态清单筛选器？

    此功能是独立于清单筛选器运行的。 当下一片段超出筛选器窗口时，也仍会通过搜索原始客户端清单来找到其 URL，并将其作为 CDN 预提取响应头返回。 因此，CDN 将获取从 DASH/HLS/平滑流清单中筛选出的片段的 URL。 但是，由于该片段不包含在播放器保存的 DASH/HLS/平滑流清单中（播放器不知道该片段存在），因此播放器永远不会向 CDN 发出 GET 请求来提取该片段。

* 是否可以预提取 DASH MPD/HLS 播放列表/平滑流清单？

    不可以，DASH MPD、HLS 主播放列表、HLS 变体播放列表或平滑流清单 URL 不会添加到预提取头中。

* 预提取 URL 是相对还是绝对的？

    尽管 Akamai CDN 允许这两种 URL，但媒体服务源仅提供预提取路径的相对 URL，因为使用绝对 URL 没有明显的好处。

* 此功能是否适用于 DRM 保护的内容？

    是的，此功能在 HTTP 级别运行，它不会解码或分析任何段/片段。 它不考虑内容是否已加密。

* 此功能是否适用于服务器端广告插入 (SSAI)？
    
    它确实适用于原始/主要内容（广告插入之前的原始视频内容），因为 SSAI 不会更改媒体服务源中的源内容的时间戳。 此功能是否适用于广告内容取决于广告源是否支持 Origin-Assist。 例如，如果广告内容也托管在 Azure 媒体服务中（相同或不同的源），则也会预提取广告内容。

* 此功能是否适用于 UHD/HEVC 内容？

    是。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* 请务必查看[流式处理终结点（源）](streaming-endpoint-concept.md)文档。
* 此[存储库](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中的示例介绍了如何使用 .NET 启动默认流式处理终结点。
