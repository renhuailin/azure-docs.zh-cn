---
title: 大规模使用 Azure 视频分析器媒体版（以前称为视频索引器）时的注意事项 - Azure
titleSuffix: Azure Media Services
description: 本主题说明大规模使用 Azure 视频分析器媒体版（以前称为视频索引器）时的注意事项。
services: media-services
author: Juliako
manager: femila
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: bfad40e55deae4ebad930907221517ae3ef4e5f4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385179"
---
# <a name="things-to-consider-when-using-video-analyzer-for-media-at-scale"></a>大规模使用视频分析器媒体版时的注意事项

使用 Azure 视频分析器媒体版（以前称为视频索引器）为视频编制索引并且视频存档不断增长时，请考虑使用缩放。 

本文将回答以下问题：

* 是否需要考虑任何技术约束？
* 是否有智能且高效的方法来执行此操作？
* 是否可以防止在此过程中花费太多资金？

本文提供了如何大规模使用视频分析器媒体版的六个最佳做法。

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>考虑通过字节数组使用 URL 上传视频

视频分析器媒体版确实可以让你选择是从 URL 上传视频，还是直接通过将文件作为字节数组发送来上传视频，但后者有一些约束。 有关详细信息，请参阅[上传注意事项和限制](upload-index-videos.md#uploading-considerations-and-limitations)

首先，文件大小有限制。 使用 URL 时，字节数组文件的大小限制为 2 GB，而上传大小限制为 30 GB。

其次，考虑一些可能会影响性能并因此影响缩放能力的问题：

* 使用多部分发送文件意味着高度依赖网络， 
* 服务可靠性， 
* 连接性， 
* 上传速度， 
* 万维网中某个位置丢失了数据包。

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="大规模使用视频分析器媒体版时的第一个注意事项":::

使用 URL 上传视频时，只需提供媒体文件位置的路径，其余由视频索引器负责（查看[上传视频](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API 中的 `videoUrl` 字段）。

> [!TIP]
> 使用上传视频 API 的 `videoUrl` 可选参数。

要查看如何使用 URL 上传视频的示例，请查看[此示例](upload-index-videos.md#code-sample)。 或者，可以使用 [AzCopy](../../storage/common/storage-use-azcopy-v10.md) 快速可靠地将内容获取到存储帐户，从该存储帐户可以使用 [SAS URL](../../storage/common/storage-sas-overview.md) 将内容提交到视频分析器媒体版。

## <a name="increase-media-reserved-units-if-needed"></a>根据需要，增加媒体保留单位

通常，在刚开始使用视频分析器媒体版的概念证明阶段，你不需要大量的计算能力。 当你开始有需要编制索引的更大的视频存档，并且希望该过程的发展速度适应你的用例时，就需要扩展视频分析器媒体版的使用情况。 因此，如果当前计算能力不够，则应考虑增加所使用的计算资源数。

在 Azure 媒体服务中，当你想要增加计算能力和并行度时，需要注意媒体[保留单位](../../media-services/latest/concept-media-reserved-units.md) (RU)。 RU 是确定媒体处理任务的参数的计算单位。 RU 的数量会影响每个帐户中可并发处理的媒体任务的数量，其类型决定了处理速度，而一个视频的索引如何很复杂，则可能需要多个 RU。 如果你的 RU 处于繁忙状态，则新任务将保留在队列中，直到另一个资源可用。

为了有效地操作并避免资源在某些时候处于空闲状态，视频索引器提供了一个自动缩放系统，该系统在需要较少处理时减少 RU，在高峰时间增加 RU（直到充分利用所有 RU）。 通过在帐户设置中[打开自动缩放](manage-account-connected-to-azure.md#autoscale-reserved-units)，或使用 [Update-Paid-Account-Azure-Media-Services API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)，可启用此功能。

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="大规模使用视频分析器媒体版时的第二个注意事项":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="AMS 保留单位":::

为了最大限度地减少索引持续时间和低吞吐量，建议你从 S3 类型的 10 个 RU 开始。 稍后，如果纵向扩展以支持更多内容或更高的并发性，并且需要更多的资源来执行此操作，则可以[使用支持系统与我们联系](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)（仅限付费帐户）请求分配更多 RU。

## <a name="respect-throttling"></a>遵守带宽限制

视频分析器媒体版旨在大规模处理索引，如果你想充分利用它，还应了解系统的功能并相应地设计集成。 当你发送一批视频的上传请求时，一定不想看到有些影片没有上传，并收到 HTTP 429 响应代码（太多请求）。 发生这种情况的原因是，你发送的请求数超过了[我们支持的每分钟影片限制](upload-index-videos.md#uploading-considerations-and-limitations)。 视频分析器媒体版在 HTTP 响应中添加 `retry-after` 头，该头指定何时应尝试下一次重试。 在尝试下一个请求之前，请确保你遵守此限制。

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="精心设计集成，遵守带宽限制":::

## <a name="use-callback-url"></a>使用回叫 URL

建议不要从发送上传请求的那一刻起就一直轮询请求的状态，你可以添加一个[回调 URL](upload-index-videos.md#callbackurl)，等待视频分析器媒体版为你提供所需信息。 一旦上传请求中存在任何状态更改，就会收到你指定的 URL 的 POST 通知。

可以添加一个回叫 URL 作为[上传视频 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) 的参数之一。 在 [GitHub 存储库](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/)上查看代码示例。 

对于回叫 URL，还可以使用 Azure Functions，这是一个可通过 HTTP 触发并实现后流的无服务器事件驱动平台。

### <a name="callback-url-definition"></a>回叫 URL 定义

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>为你使用正确的索引参数

在进行有关大规模使用视频分析器媒体版的决策时，请思考如何使用正确的参数来充分利用它以满足你的需求。 通过定义不同的参数来思考你的用例，可以节省资金并加快视频的索引过程。

在上传视频和编制视频索引之前，请阅读这篇简短的[文档](upload-index-videos.md)，查看 [indexingPreset](upload-index-videos.md#indexingpreset) 和 [streamingPreset](upload-index-videos.md#streamingpreset)，以便更好地了解你的选项。

例如，如果你不打算观看视频，请不要将“预设”设置为“流式处理”，如果你只需要音频见解，请不要为视频见解编制索引。

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>以最佳分辨率，而不是最高分辨率编制索引

你可能会问，为视频编制索引需要怎样的视频质量？ 

在许多情况下，HD (720P) 视频和 4K 视频之间几乎没有任何区别。 最终，将获得几乎相同的见解和相同的可信度。 上传影片的质量越高，文件大小就越大，这就会导致上传视频所需的计算能力更高，时间更长。

例如，对于人脸检测功能，较高的分辨率在许多虽小但在情景中很重要的人脸的情况下有帮助。 但是，这会导致运行时出现二次增加，并增加假正的风险。

因此，建议你验证是否获取了正确的用例结果，并首先在本地对其进行测试。 以 720P 和 4K 上传同一视频，并比较获得的见解。

## <a name="next-steps"></a>后续步骤

[了解 API 生成的 Azure 视频分析器媒体版输出](video-indexer-output-json-v2.md)