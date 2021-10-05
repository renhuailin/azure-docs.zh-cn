---
title: 如何使用内容感知编码预设
description: 本文讨论如何在 Microsoft Azure 媒体服务 v3 中使用使用内容感知型编码预设。
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 09/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 09d3f199cc17cc1457413f673afe3c6be0646f58
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668614"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>如何使用内容感知编码预设

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>简介
[内容感知型编码预设](encode-content-aware-concept.md)有助于为通过[自适应比特率流式处理](encode-autogen-bitrate-ladder.md)进行传输（在这种情况下，视频需要以多个比特率进行编码）做好更充分的内容准备。该预设包含自定义逻辑，能够使编码器提供最适合给定分辨率的比特率值而无需大量计算分析。 预设使用这些结果来确定最佳层数、适当的比特率和分辨率设置，以便于自适应流式处理进行传输。

与自适应流式处理预设相比，内容感知型编码在处理低复杂性和中等复杂性视频时更有效，在这种情况下，输出文件的比特率较低，但其提供的质量仍可带来良好的观看体验。

### <a name="using-the-content-aware-encoding-presets"></a>使用内容感知编码预设

若要在代码中使用内容感知预设进行编码，请使用 [BuiltInStandardEncoderPreset](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset) 对象并将 [PresetName](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset.presetname) 属性设置为以下内置预设名称之一。

- ContentAwareEncoding - 此预设支持 H.264。
- H265ContentAwareEncoding - 此预设支持 HEVC (H.265)

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

## <a name="configure-output-settings"></a>配置输出设置

此外，在确定用于为自适应比特率流式处理梯度编码的最佳设置时，开发人员还可以控制内容感知编码预设使用的输出范围。

使用 PresetConfigurations 类，开发人员可以将一组约束和选项传入内容感知编码预设，以控制编码器生成的结果文件。 如果你想要将所有编码限制为特定的最大分辨率，以控制编码作业的体验或成本，则属性特别有用。  如果能够控制受众在移动网络或带宽受限的全球区域中可支持的最大和最小比特率，则也很有帮助。

使用 PresetConfigurations 类可以调整内容感知编码预设的以下设置。 


| 属性                   | 说明                                                                                                                                                                         |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| complexity              | Speed、Balanced 或 Quality。 可用于配置编码器设置，以控制速度和质量之间的平衡。 示例：将 Complexity 设置为 Speed 可以加快编码速度，但会降低压缩效率 |
| interleaveOutput          |    控制 MP4 输出文件的格式设置。  可将这些文件与音频和视频交错，使其更容易单独共享；或者，它们可以是独立的音频和视频文件，以便可以更轻松地在后期绑定额外的语言音轨或者在以后修改音频。                                                                                                                                                                                 |
| keyFrameIntervalInSeconds |        设置在对文件进行编码时用于每个帧组 (GOP) 的关键帧距离。  典型的新式自适应流式处理协议使用 2 秒。  根据传送内容时所处的网络情况，有时使用更长的间隔会有帮助。 请咨询 CDN 提供商，或者在自己的网络上进行一些试验。                                                                                                                                                                             |
| maxBitrateBps             |               约束自适应流式处理梯度的最高比特率。  此约束对于控制存储成本和网络传送成本很有用。  此外，它还可以将提供的比特率保持在客户端支持的范围内。                                                                                                                                                                       |
| maxHeight                 |       允许在自适应梯度中编码的最高分辨率。  它还可用于控制成本或控制受众体验。 可以将所有编码限制为“标准清晰度”，或者根据需要将它们限制为最大 720P。                                                                                                                                                                              |
| maxLayers                 |        此属性控制自适应流式处理梯度中的层数。 它还可以按清晰度控制以 MP4 格式输出到存储容器中的文件数量。  降低此值也会降低成本，因为生成的输出再现内容更少，编码总分钟数也会减少。 将此属性与 maxHeight 和 maxBitrateBps 结合使用，可以在编码期间控制成本，并使费用更具可预测性。                                                                                                                                                                             |
| minBitrateBps             |                           此属性控制编码器要选择的最低比特率。  使用此属性可以优化低比特率和低分辨率再现内容的质量。                                                                                                                                                         |
| minHeight                 |     此设置控制自适应比特率梯度要生成的最小分辨率。 它有助于避免客户端选择对于要编码的内容类型而言可能过于模糊的极低分辨率的再现内容。 它在你提供的体验质量方面有很大的帮助。                                                                                                                                                                                |
|

### <a name="example-code-for-configuring-content-aware-preset"></a>用于配置内容感知预设的示例代码

以下代码片段摘自[使用内容感知、H.246 和约束进行编码](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained)示例。

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/Encoding_H264_ContentAware_Constrained/Program.cs#PresetConfigurations)]

## <a name="samples"></a>示例

以下示例演示了可使用的各种编解码器和约束、如何实例化预设，以及如何提交和监视编码作业。

### <a name="net"></a>.NET

| 示例 | 说明|
|---------|------------------|
| [使用内容感知和 H.246 进行编码](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | 演示不带任何约束的 H.264 内容感知编码的最基本用法 |
| [使用内容感知、H.246 和约束进行编码](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | 演示如何使用 PresetConfigurations 类来约束预设的输出行为|
| [使用内容感知和 HEVC (H.265) 进行编码](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | 演示具有内容感知编码但没有约束的 HEVC 编解码器的基本用法。  PresetConfigurations 类也受 HEVC 的支持，可以添加到此示例中|

> [!NOTE]
> 使用 `ContentAwareEncoding` 预设的编码作业仅根据输出分钟数计费。 AMS 使用二次编码，除[定价页](https://azure.microsoft.com/pricing/details/media-services/#overview)上列出的费用以外，没有任何与使用任何预设相关的额外费用。
  
## <a name="next-steps"></a>后续步骤

* [教程：使用媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - REST](stream-files-tutorial-with-rest.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - CLI](stream-files-cli-quickstart.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET](stream-files-dotnet-quickstart.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - Node.js](stream-files-nodejs-quickstart.md)
