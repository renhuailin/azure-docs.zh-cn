---
title: 如何创建图像覆盖
description: 了解如何创建图像覆盖
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 532e26e486e758b3fd8079f6f61f3e00e82dbdca
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662298"
---
# <a name="how-to-create-an-image-overlay"></a>如何创建图像覆盖

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

媒体服务可用于在视频顶部覆盖图像、音频文件或其他视频。 输入必须仅指定一个图像文件。 可以指定 JPG、PNG、GIF 或 BMP 格式的图像文件，或者指定音频文件（如 WAV、MP3、WMA 或 M4A 文件）或某种受支持文件格式的视频文件。


## <a name="prerequisites"></a>先决条件

* 在示例中收集配置 appsettings.json 文件所需的帐户信息。 如果你不确定怎么做，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-register-app.md)。 Appsettings.json 文件中应有以下值。

```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
```

如果不熟悉创建“转换”，建议完成以下活动：

* 阅读[使用媒体服务编码视频和音频](encode-concept.md)
* 阅读[如何对自定义转换进行编码 - .NET](transform-custom-presets-how-to.md)。 按照那篇文章中的步骤来设置处理转换所需的 .NET，然后返回此处尝试一个覆盖预设示例。
* 请参阅[转换参考文档](/rest/api/media/transforms)。

熟悉了转换后，请下载覆盖示例。

## <a name="overlays-preset-sample"></a>覆盖预设示例

克隆媒体服务 .NET 示例存储库。

```bash
    git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

导航到解决方案文件夹，并启动 Visual Studio Code 或 Visual Studio 2019。

VideoEncoding 文件夹中提供了许多编码示例。 打开 [VideoEncoding/Encoding_OverlayImage](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_OverlayImage) 解决方案文件夹中的项目，以开始学习如何使用覆盖。

示例项目包含两个媒体文件。 一个视频文件，一个要覆盖在视频顶部的徽标图像。
* ignite.mp4
* cloud.png

在此示例中，我们首先在 CreateCustomTransform 方法中创建一个自定义转换，可将图像覆盖在视频上。  使用 StandardEncoderPreset *[](/rest/api/media/transforms/create-or-update#standardencoderpreset)* 的 *筛选器 [](/rest/api/media/transforms/create-or-update#filters)* 属性，我们分配一个包含视频覆盖设置的新筛选器集合。

[VideoOverlay](/rest/api/media/transforms/create-or-update#videooverlay) 包含一个名为 InputLabel 的属性，必须具有此属性才能从提交到作业的作业输入文件列表中进行映射，并定位用作覆盖图像或视频的正确输入源文件。  提交作业时，将使用相同的标签名称，以便与“转换”中的设置匹配。 在示例中，我们使用了标签名称“logo”，如字符串常量 OverlayLabel 中所示。

以下代码片段显示了如何将“转换”格式化为使用覆盖。

```csharp
new TransformOutput
                {
                    Preset = new StandardEncoderPreset
                    {
                        Filters = new Filters
                        {
                            Overlays = new List<Overlay>
                            {
                                new VideoOverlay
                                {
                                    InputLabel = OverlayLabel,   // same as the one used in the JobInput to identify which asset is the overlay image
                                    Position = new Rectangle( "1200","670") // left, top position of the overlay in absolute pixel position relative to the source videos resolution. 
    
                                }
                            }
                        },
                        Codecs = new List<Codec>
                        {
                            new AacAudio
                            {
                            },
                            new H264Video
                            {
                                KeyFrameInterval = TimeSpan.FromSeconds(2),
                                Layers = new List<H264Layer>
                                {
                                    new H264Layer
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 1000000, // 1Mbps
                                        Width = "1280",
                                        Height = "720"
                                    },
                                    new H264Layer   // Adding a second layer to see that the image also is scaled and positioned the same way on this layer. 
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 600000, // 600 kbps
                                        Width = "480",
                                        Height = "270"
                                    }
                                }
                            }
                        },
                        Formats = new List<Format>
                        {
                            new Mp4Format
                            {
                                FilenamePattern = "{Basename}_{Bitrate}{Extension}",
                            }
                        }
                    }
                }
```

将作业提交到“转换”，必须首先创建两个输入资产。

* 资产 1 - 在此示例中，创建的第一个资产是本地视频文件“ignite.mp4”。 我们将这个视频用作合成背景，并在这个视频上覆盖一个徽标图像。 
* 资产 2 - 在此示例中，第二个资产（存储在 overlayImageAsset 变量中）包含要用于徽标的 .png 文件。 此图像将在编码过程中定位到视频上。

在 SubmitJobAsync 方法中创建作业时，我们首先使用 List<> 对象构造一个 JobInput 数组。  List 将包含对两个源资产的引用。

为了识别和匹配哪个输入资产将用作上文“转换”中定义的筛选器中的覆盖，我们再次使用标签名称“logo”来处理匹配。 标签名称将添加到 .png 图像的 JobInputAsset 中。 这会告诉“转换”在执行覆盖操作时使用哪个资产。 可以对存储在媒体服务中的不同资产（包含希望覆盖的各种徽标或图形）重复使用这同一个“转换”，只需更改传递到作业的资产名称，同时对“转换”使用相同的标签名称“logo”以与其匹配。

``` csharp
    // Add both the Video and the Overlay image assets here as inputs to the job.
    List<JobInput> jobInputs = new List<JobInput>() {
        new JobInputAsset(assetName: inputAssetName),
        new JobInputAsset(assetName: overlayAssetName, label: OverlayLabel)
    };
```

通过在 Visual Studio Code 的“运行和调试”窗口中选择项目来运行示例。 该示例将输出编码操作的进度，最后将内容下载到项目根目录中的 /Output 文件夹中，如果是完整 Visual Studio，则可能位于 /bin/Output 文件夹中。 

该示例还发布流式处理内容，并将输出可在任何兼容播放器中使用的完整 HLS、DASH 和平滑流式处理清单文件 URL。  还可以轻松地将清单 URL 复制到 [Azure Media Player 演示](http://ampdemo.azureedge.net/)，还可以将以 /manifest 结尾的 URL 粘贴到 URL 框中，然后单击“更新播放器”。

## <a name="api-references"></a>API 参考

* [VideoOverlay 对象](/rest/api/media/transforms/create-or-update#videooverlay)
* [筛选器](/rest/api/media/transforms/create-or-update#filters)
* [StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)


[!INCLUDE [reference dotnet sdk references](./includes/reference-dotnet-sdk-references.md)]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
