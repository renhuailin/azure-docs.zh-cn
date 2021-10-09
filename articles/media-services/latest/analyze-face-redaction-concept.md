---
title: 在 Azure 媒体服务 v3 API 中查找和编修人脸 | Microsoft Docs
description: Azure 媒体服务 v3 提供人脸检测和编修（模糊处理）预设，可让你通过一个组合式阶段，或者通过一个允许编辑的两阶段操作，来提交视频文件、检测人脸，并选择性地对人脸应用编修（模糊处理）。 本文演示如何使用 v3 API 中的人脸检测器预设来查找和编修人脸。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c434720863a7ecff4192720874547f6f8c2e8ed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621407"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>使用人脸检测器预设查找和编修（模糊处理）人脸

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒体服务 v3 API 包含一个人脸检测器预设，该预设在云中提供可缩放的人脸检测和编修（模糊处理）功能。 使用面部修订，可对视频进行修改，使所选个人的面部模糊显示。 用户可能想要在公共安全和新闻媒体场景中使用面部修订服务。 对于时长仅几分钟但包含多张人脸的镜头，进行手动人脸编修可能需要几个小时，但使用此预设仅需几个简单步骤即可完成该过程。

本文提供有关人脸检测器预设的详细信息，并介绍如何通过适用于 .NET 的 Azure 媒体服务 SDK 使用该预设。

## <a name="compliance-privacy-and-security"></a>符合性、隐私和安全性

作为一项重要提醒，在 Azure 媒体服务中使用分析时必须遵守所有适用的法律。 不得以违反他人权利的方式使用 Azure 媒体服务或任何其他 Azure 服务。 在将任何视频（包括任何生物特征数据）上传到 Azure 媒体服务进行处理和存储之前，必须拥有所有适当的权利，包括获得视频中个人的所有适当同意。 若要了解 Azure 媒体服务中的合规性、隐私和安全性，请参阅 Azure [认知服务条款](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)。 若要了解 Microsoft 的隐私义务以及对您的数据的处理，请查看 Microsoft 的[隐私声明](https://privacy.microsoft.com/PrivacyStatement)、[在线服务条款](https://www.microsoft.com/licensing/product-licensing/products)（“OST”）和[数据处理附录](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)（“DPA”）。 其他隐私信息（包括有关数据保留、删除/销毁的信息）在 OST 中和[此处](../../azure-video-analyzer/video-analyzer-for-media-docs/faq.yml)提供。 使用 Azure 媒体服务即表示你同意接受认知服务条款、OST、DPA 和隐私声明的约束

## <a name="face-redaction-modes"></a>面部修订模式

面部修订的工作方式是：检测每一帧视频中的面部，并跟踪之前和之后的面部对象，以便同一个人在其他角度也模糊显示。 自动编修过程比较复杂，不能 100% 保证始终能对每张人脸进行模糊处理。 出于此原因，能够以双阶段模式使用该预设，以通过编辑阶段改善模糊处理的质量和准确度，然后提交文件以完成最终的模糊处理两阶段。 

除了全自动化的“组合”模式外，双阶段工作流还允许通过人脸 ID 列表选择要模糊处理（或不模糊处理）的人脸。 为了按帧进行任意调整，该预设将使用 JSON 格式的元数据文件作为第二个阶段的输入。 此工作流拆分为“分析”和“修订”模式。

还可以轻松将这两个模式组合为在一个作业中运行两项任务的单个阶段；此模式称为“组合”。  本文中的示例代码将演示如何对示例源文件使用简化的单阶段“组合”模式。

### <a name="combined-mode"></a>组合模式

这会在单个阶段中生成一个经过编修的 MP4 视频文件，而无需对 JSON 文件进行任何手动编辑。 作业的资产文件夹中的输出将是单个 .mp4 文件，其中包含使用选定模糊效果模糊处理的人脸。 使用设置为 SourceResolution 的 resolution 属性可以实现最佳编修效果。

| 阶段 | 文件名 | 说明 |
| --- | --- | --- |
| 输入资产 |"ignite-sample.mp4" |WMV、MOV 或 MP4 格式的视频 |
| 预设配置 |人脸检测器配置 | mode：FaceRedactorMode.Combined，blurType：BlurType.Med，resolution：AnalysisResolution.SourceResolution   |
| 输出资产 |"ignite-redacted.mp4 |包含应用于人脸的模糊效果的视频 |

### <a name="analyze-mode"></a>分析模式

双阶段工作流的“分析”阶段采用视频输入并生成一个 JSON 文件，其中包含检测到的每张人脸的人脸位置、人脸 ID 和 jpg 图像列表。
请注意，在后续运行分析传递时，不保证人脸 ID 的相同。

| 阶段 | 文件名 | 说明 |
| --- | --- | --- |
| 输入资产 |"ignite-sample.mp4" |WMV、MPV 或 MP4 格式的视频 |
| 预设配置 |人脸检测器配置 |**mode**：FaceRedactorMode.Analyze，**resolution**：AnalysisResolution.SourceResolution|
| 输出资产 |ignite-sample_annotations.json |JSON 格式的面部位置批注数据。 在后续运行分析传递时，不保证人脸 ID 的相同。 用户可编辑此数据，以修改模糊边界框。 请查看以下示例。 |
| 输出资产 |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |裁剪后的 jpg 文件，显示每个检测到的面部，其中的数字指示面部的标签 ID |

#### <a name="output-example"></a>输出示例

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>编修（模糊处理）模式

工作流的第二步使用更大数量的输入，这些输入必须合并为单个资产。

这包括要模糊处理的 ID 的列表、原始视频和批注 JSON。 此模式使用批注来对输入视频进行模糊处理。

“分析”步骤的输出不包括原始视频。 需要将该视频上传到“修订”模式任务的输入资产中，并将其选作主文件。

| 阶段 | 文件名 | 说明 |
| --- | --- | --- |
| 输入资产 |"ignite-sample.mp4" |WMV、MPV 或 MP4 格式的视频。 与步骤 1 中相同的视频。 |
| 输入资产 |"ignite-sample_annotations.json" |完成第一阶段后生成的注释元数据文件，如果你想要更改已模糊处理的人脸，可以选择性地进行修改。 必须在外部应用程序、代码或文本编辑器中进行这种编辑。 |
| 输入资产 | "ignite-sample_IDList.txt"（可选） | 要进行修订的可选面部 ID 列表，以新行进行分隔。 如果留空，则会对源中的所有人脸应用模糊处理。 可以使用列表有选择地选择不要模糊处理的特定人脸。 |
| 人脸检测器预设  |预设配置  | mode：FaceRedactorMode.Redact，blurType：BlurType.Med  |
| 输出资产 |"ignite-sample-redacted.mp4" |基于批注进行了模糊处理的视频 |

#### <a name="example-output"></a>示例输出

这是来自选择了一个 ID 的 ID 列表的输出。
在后续运行分析传递时，不保证人脸 ID 的相同。

示例 foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>模糊类型

在“组合”或“编修”模式下，可通过 JSON 输入配置在五种不同的模糊处理模式中选择：“低”、“中”、“高”、“盒”和“黑色”      。 默认情况下使用“中”。

可以查找以下模糊类型的示例。

#### <a name="low"></a>低

![低分辨率模糊处理设置示例。](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>中

![中分辨率模糊处理设置示例。](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>高

![高分辨率模糊处理设置示例。](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![在调试输出时使用的盒模式。](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>黑色

![黑盒模式包括带有黑盒的所有人脸。](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>输出 JSON 文件中的元素

修订 MP 提供高精确度的面部位置检测和跟踪功能，可在一个视频帧中检测到最多 64 张人脸。 正面的面部可提供最佳效果，而检测和跟踪侧面的面部和较小的面部（小于或等于 24x24 像素）可能具有一定难度。

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET 示例代码

以下程序演示如何使用“组合”单阶段编修模式：

- 创建资产并将媒体文件上传到资产。
- 配置使用模式和 blurType 设置的人脸检测器预设。
- 使用人脸检测器预设创建新转换
- 下载输出经过编修的视频文件。

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含 .NET 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

该示例位于 [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) 文件夹。 打开下载的项目中的 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json)。 将值替换为从[访问 API](./access-api-howto.md) 获得的凭据。

（可选）可以复制存储库根目录中的 [sample.env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env) 文件，在其中填写详细信息，然后将该文件重命名为 .env（注意前面包含句点！），以便可以在存储库中的所有示例项目中使用该文件  。  这样就无需在每个示例中包含一个已填充的 appsettings.json 文件，另外，还可以在将任何设置签入到你自己的 GitHub 克隆存储库时提供保护。

#### <a name="examples"></a>示例

此代码演示如何为“组合”模式模糊处理设置 FaceDetectorPreset 。

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

此代码示例演示如何在创建过程中将预设传入到转换对象。 创建转换后，可以直接向其提交作业。

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
