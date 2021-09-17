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
ms.date: 08/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b6537547db2bb3371b6d7b4cd36ebf9ffc48530c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429815"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>如何使用内容感知型编码预设

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>简介
[内容感知型编码预设](encode-content-aware-how-to.md)有助于为[自适应比特率流式处理](encode-autogen-bitrate-ladder.md)做好更充分的内容传输准备，其中视频需要以多个比特率进行编码。该预设包含自定义逻辑，能够使编码器在不需要大量计算分析的情况下寻找最适合给定分辨率的比特率值。 预设使用这些结果来确定最佳层数、适当的比特率和分辨率设置，以便于自适应流式处理进行传输。 

与自适应流式处理预设相比，内容感知型编码在处理低复杂性和中等复杂性视频时更有效，其中输出文件的比特率较低，但其提供的质量仍可带来良好的观看体验。

## <a name="use-the-content-aware-encoding-preset"></a>使用内容感知型编码预设
可按如下所示创建使用此预设的转换。

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

有关使用转换输出的教程，请参阅[后续步骤](#next-steps)部分。 可以在 MPEG-DASH 和 HLS 等协议中通过媒体服务流式处理终结点传送输出资产（如教程中所述）。

> [!NOTE]
> 使用 `ContentAwareEncoding` 预设的编码作业仅根据输出分钟数计费。 AMS 使用二次编码，除[定价页](https://azure.microsoft.com/pricing/details/media-services/#overview)上列出的费用以外，没有任何与使用任何预设相关的额外费用。
  
## <a name="next-steps"></a>后续步骤

* [教程：使用媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - REST](stream-files-tutorial-with-rest.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - CLI](stream-files-cli-quickstart.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET](stream-files-dotnet-quickstart.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - Node.js](stream-files-nodejs-quickstart.md)
