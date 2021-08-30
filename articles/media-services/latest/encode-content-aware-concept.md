---
title: 内容感知编码的预设
description: 本文介绍 Microsoft Azure 媒体服务 v3 中的内容感知编码。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b87b37b98ada136597faa3ac5d990d6e08e9865
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179421"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>使用内容感知编码预设来查找给定分辨率的最佳比特率值

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

若要准备通过[自适应比特率流式处理](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)传送的内容，需以多个比特率（从高到低）编码视频。 这可以确保质量的平稳降级，因为比特率会降低，而视频的分辨率也会降低。 此类多比特率编码利用所谓的编码梯度 – 分辨率和比特率的表；请参阅媒体服务的[内置编码预设](/rest/api/media/transforms/createorupdate#encodernamedpreset)。

应该了解自己正在处理的内容，并根据单个视频的复杂性自定义/优化编码梯度。 在每种分辨率下有一个比特率，超过该比特率会感知不到任何质量提升 – 编码器将以此最佳比特率运行。 下一级优化是基于内容选择分辨率 – 例如，降到 720p 以下不会使 PowerPoint 演示文稿的视频受益。 接下来，可以进一步在编码器中优化视频中每个拍摄画面的设置。 

Microsoft 的[自适应流式处理](encode-autogen-bitrate-ladder.md)预设部分解决了源视频质量和分辨率变化的问题。 我们的客户混合使用不同的内容，其中某些内容的分辨率为 1080p，有些为 720p，还有少部分为标清或更低分辨率。 此外，并非所有源内容都是电影或电视演播室提供的高质量夹层文件。 自适应流式处理预设解决了这些问题，它可以确保比特率梯度永不超过输入夹层文件的分辨率或平均比特率。 但是，此预设不会检查除分辨率和比特率以外的源属性。

## <a name="the-content-aware-encoding-preset"></a>内容感知编码预设

内容感知编码预设扩展了“自适应比特率流式处理”机制，它可以整合自定义逻辑，使编码器能够查找给定分辨率的最佳比特率值，但不需要进行大量的计算分析。 此预设将生成一组 GOP 对齐的 MP4 文件。 在提供任何输入内容的情况下，服务将对输入内容执行初始的轻量分析，并使用结果来确定最佳层数，以及自适应流式处理适合传送的比特率和分辨率设置。 此预设特别适用于中低复杂性的视频，其中的输出文件的比特率低于自适应流式处理预设，但其质量仍会为观众提供良好的体验。 输出将包含带有交错式视频和音频的 MP4 文件

以下示例图形显示了使用 [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) 和 [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion) 等质量指标做出的比较。 源的创建方式是将电影和电视节目中的高复杂性拍摄画面的简短剪辑相连接，旨在给编码器提供压力。 按照定义，此预设产生的结果因内容而异 – 这也意味着，对于某些内容，比特率可能不会显著降低，或者质量不会显著提高。

![使用 PSNR 的速率失真 (RD) 曲线](media/encode-content-aware-concept/msrv1.png)

图 1：**使用高复杂性源的 PSNR 指标的速率失真 (RD) 曲线**

![使用 VMAF 的速率失真 (RD) 曲线](media/encode-content-aware-concept/msrv2.png)

图 2：**使用高复杂性源的 VMAF 指标的速率失真 (RD) 曲线**

下面是另一个源内容类别的结果，其中，编码器可以确定输入质量较差（由于比特率较低，因此生成了很多压缩项目）。 请注意，使用内容感知预设时，编码器确定只生成一个输出层 – 该层的比特率足够低，使大多数客户端能够播放流，而不会出现停顿。

![使用 PSNR 的 RD 曲线](media/encode-content-aware-concept/msrv3.png)

图 3：**使用低质量输入的 PSNR 的 RD 曲线（分辨率为 1080p）**

![使用 VMAF 的 RD 曲线](media/encode-content-aware-concept/msrv4.png)

图 4：**使用低质量输入的 VMAF 的 RD 曲线（分辨率为 1080p）**

## <a name="8-bit-hevc-h265-support"></a>8 位 HEVC (H.265) 支持

Azure 媒体服务的标准编码器现在支持 8 位 HEVC (H.265) 编码。 可以通过动态打包器使用“hev1”格式传送和打包 HEVC 内容。

[media-services-v3-dotnet GitHub 存储库](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)中提供了有关使用 HEVC 实现 .NET 自定义编码的新示例。 除了自定义编码，AMS 还支持其他新的内置 HEVC 编码预设，可以在 [2021 年 2 月发行说明](https://docs.microsoft.com/azure/media-services/latest/release-notes#february-2021)中查看这些预设。

## <a name="how-to-use-the-content-aware-encoding-preset"></a>如何使用内容感知编码预设 

可按如下所示创建使用此预设的转换。 

有关使用转换输出的教程，请参阅[后续步骤](#next-steps)部分。 可以在 MPEG-DASH 和 HLS 等协议中通过媒体服务流式处理终结点传送输出资产（如教程中所述）。

> [!NOTE]
> 请确保使用预设的 ContentAwareEncoding 而不使用 ContentAwareEncodingExperimental。 或者，如果想要使用 HEVC 进行编码，可使用 H265ContentAwareEncoding。

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> 使用 `ContentAwareEncoding` 预设的编码作业仅根据输出分钟数计费。 AMS 使用二次编码，除[定价页](https://azure.microsoft.com/pricing/details/media-services/#overview)上列出的费用以外，没有任何与使用任何预设相关的额外费用。
  
## <a name="next-steps"></a>后续步骤

* [教程：使用媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - REST](stream-files-tutorial-with-rest.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - CLI](stream-files-cli-quickstart.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET](stream-files-dotnet-quickstart.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - Node.js](stream-files-nodejs-quickstart.md)
