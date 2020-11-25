---
title: 在缩放时使用视频索引器时要考虑的事项-Azure
titleSuffix: Azure Media Services
description: 本主题说明在大规模使用视频索引器时应考虑的事项。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: 7d5108d2c155c7e21f2f94f532bd1aa0a96c5b3f
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "96020498"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>按比例使用视频索引器时要考虑的事项

使用 Azure 媒体服务视频索引器为视频编制索引并增加视频存档时，请考虑缩放。 

本文回答以下问题：

* 是否需要考虑任何技术限制？
* 是否有智能和高效的方法来执行此操作？
* 是否可以在此过程中阻止支出额外资金？

本文提供了有关如何大规模使用视频索引器的六个最佳实践。

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>上传视频时，请考虑使用 URL over 字节数组

视频索引器可让你选择是从 URL 上传视频，还是直接通过将文件作为字节数组发送，后者会附带一些约束。 有关详细信息，请参阅 [上传注意事项和限制) ](upload-index-videos.md#uploading-considerations-and-limitations)

首先，它具有文件大小限制。 使用 URL 时，字节数组文件的大小限制为 2 GB，与 30 GB 的上载大小限制相同。

其次，只考虑一些可能会影响性能并因此能够扩展的问题：

* 使用多部分发送文件意味着网络上的高相关性， 
* 服务可靠性， 
* 连接 
* 上传速度， 
* 万维网内某个位置丢失了数据包。

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="按比例使用视频索引器的第一注意事项":::

使用 URL 上传视频时，只需提供媒体文件位置的路径，视频索引器将负责 rest (查看 `videoUrl` [上传视频](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) API) 中的字段。

> [!TIP]
> 使用 `videoUrl` 上传视频 API 的可选参数。

若要查看如何使用 URL 上传视频的示例，请查看 [此示例](upload-index-videos.md#code-sample)。 或者，你可以使用 [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) 以一种快速可靠的方式将内容获取到存储帐户，你可以使用 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)将内容提交到视频索引器。

## <a name="increase-media-reserved-units-if-needed"></a>如果需要，增加媒体保留单位

通常在概念证明阶段，当你只是开始使用视频索引器时，不需要大量的计算能力。 当你开始对需要进行索引的视频进行更大的存档时，如果你希望该过程处于适合你的用例的步调，则需要扩展视频索引器的使用情况。 因此，如果当前的计算能力只是足够的，则应考虑增加所使用的计算资源数。

在 Azure 媒体服务中，当你想要增加计算能力和并行度时，需要注意媒体 [保留单位](../latest/concept-media-reserved-units.md) (ru) 。 Ru 是确定媒体处理任务的参数的计算单位。 Ru 的数量会影响每个帐户中可并发处理的媒体任务的数量，它们的类型决定了处理速度，而一个视频的索引很复杂，则可能需要多个 RU。 如果你的 ru 处于繁忙状态，则新任务将保留在队列中，直到另一个资源可用。

为了有效地操作和避免资源处于空闲状态，视频索引器提供了一个自动缩放系统，该系统在需要较少的处理的情况下旋转 ru，并在您的高峰时间内旋转 ru， (完全使用所有的 ru) 。 可以启用此功能，方法是在帐户设置中 [打开自动缩放](manage-account-connected-to-azure.md#autoscale-reserved-units) ，或使用 [更新-付费-AZURE 媒体服务 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update)。

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="按比例使用视频索引器的第二个注意事项":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="AMS 保留单位":::

为了最大限度地减少索引持续时间和低吞吐量，我们建议你从 S3 类型的10个 ru 开始。 稍后，如果纵向扩展以支持更多内容或更高的并发性，并且需要更多的资源来执行此操作，则可以使用付费帐户上 [的支持系统 (与我们联系](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) ，只需) 来请求更多的 ru 分配。

## <a name="respect-throttling"></a>考虑限制

视频索引器是为了大规模地处理索引而构建的，当你想要充分利用它时，还应知道系统的功能并相应地设计集成。 您不希望为一批视频发送上载请求，只是为了发现某些电影没有上传，而您收到 HTTP 429 响应代码 (太多的请求) 。 发生这种情况的原因是，你发送的请求数超过了 [我们支持的每分钟电影限制](upload-index-videos.md#uploading-considerations-and-limitations)。 视频索引器将 `retry-after` 标头添加到 HTTP 响应中，标头指定何时应尝试下一次重试。 在尝试下一个请求之前，请确保你尊重它。

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="设计集成，遵循限制":::

## <a name="use-callback-url"></a>使用回叫 URL

我们建议您不要在发送上载请求的第二个请求的情况下，从发送请求的第二个请求开始，而是添加一个 [回调 URL](upload-index-videos.md#callbackurl)，并等待视频索引器更新您的状态。 上载请求中的任何状态发生更改后，就会收到你指定的 URL 的 POST 通知。

可以添加一个回调 URL 作为 [上传视频 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload)的参数之一。 查看 [GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/)存储库中的代码示例。 

对于回调 URL，你还可以使用 Azure Functions，这是一个可通过 HTTP 触发并实现以下流的无服务器事件驱动平台。

### <a name="callback-url-definition"></a>回调 URL 定义

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>使用正确的索引参数

在做出大规模使用视频索引器的决策时，请查看如何使用正确的参数满足你的需求。 通过定义不同的参数来考虑用例，可以节省资金并使视频的索引过程更快。

上传和索引 [之前，请](upload-index-videos.md)先查看 [indexingPreset](upload-index-videos.md#indexingpreset) 和 [streamingPreset](upload-index-videos.md#streamingpreset) ，以便更好地了解你的选项。

例如，如果你不打算观看视频，请不要将 "预设" 设置为 "流式处理"，如果你只需要音频见解，请不要为视频见解编制索引。

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>最佳分辨率的索引，不是最高分辨率

你可能会问，为视频编制索引需要哪些视频质量？ 

在许多情况下，HD (720P) 视频和4K 视频之间几乎没有任何区别。 最终，您将获得与相同置信度几乎相同的见解。 上传电影的质量越高，文件大小越大，这就会产生更高的计算能力和上传视频所需的时间。

例如，对于人脸检测功能，较高的分辨率有助于实现许多小但根据上下文重要的场景。 但这会导致运行时出现二次增加，并增加误报的风险。

因此，建议你验证是否获取了正确的用例结果，并首先在本地对其进行测试。 在720P 和4K 中上传同一视频，并比较获得的见解。

## <a name="next-steps"></a>后续步骤

[检查 API 生成的 Azure 视频索引器输出](video-indexer-output-json-v2.md)
