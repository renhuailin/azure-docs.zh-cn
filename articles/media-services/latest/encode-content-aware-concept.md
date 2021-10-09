---
title: 内容感知编码预设
description: 本文介绍 Microsoft Azure 媒体服务 v3 中的内容感知编码。
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a34e2d16edb4a8ec9ba40a4426fcbbd9b2127b16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590397"
---
# <a name="content-aware-encoding"></a>内容感知型编码

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview-of-the-content-aware-encoding-preset"></a>内容感知编码预设概述

若要使用[自适应比特率流式处理](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)准备要传送的内容，需要以多种比特率（从高到低）和多种分辨率将视频编码。 此项技术使得当今 Apple iOS、Android、Windows 和 Mac 上的新式视频播放器能够使用流式处理协议来流畅地流式传输内容，而不需要缓冲内容。 这些使用不同显示大小（分辨率）和质量（比特率）的再现技术使播放器能够选择当前网络条件支持的最佳视频版本。 LTE、4G、5G、公共 Wi-Fi 和家庭网络的差异很大。

将内容编码为多个再现内容的过程需要生成“编码梯度”— 一个包含分辨率和比特率的表，用于告知编码器要生成哪些内容。 有关这种梯度的示例，请参阅媒体服务的[内置编码预设](/rest/api/media/transforms/createorupdate#encodernamedpreset)。

在理想情况下，需要知道你正在编码的内容的类型。 使用此信息可以优化编码梯度，以便与源视频中的复杂度和动作相匹配。 这意味着，在使用梯度中的每种显示大小（分辨率）时，应该有一个参照比特率，如果超过该比特率，质量的任何提升都不会被感知到 — 编码器将按照这个最佳比特率值运行。

可以做出的下一级优化是基于内容选择分辨率 — 例如，如果将包含小字号文本的 PowerPoint 演示文稿视频编码为高度小于 720 像素的行，则该视频会显得很模糊。 此外，你可能还会有这样的视频：它会根据其拍摄和编辑方式不断地改变动作和复杂度。  这就需要在每个场景或镜头边界处优化和调整编码设置。 可将智能编码器设定为优化视频中每个镜头的编码设置。

Azure 媒体服务提供[自适应流式处理](encode-autogen-bitrate-ladder.md)预设，可以部分解决源视频比特率和分辨率变化的问题。 但是，此预设不会分析源内容来确定其复杂度或其包含的动作丰富程度。 

内容感知编码预设在更静态的“自适应比特率流式处理”编码预设的基础上有所改进，因为其中添加了一个逻辑，使编码器能够寻找适用于给定分辨率的最佳比特率值，而不需要进行大量的计算分析。 此预设基于源文件输出 GOP 对齐的 MP4 的唯一“梯度”。 在给定了源视频的情况下，预设将对输入内容执行初始快速分析，并使用结果确定所需的最佳层数、比特率和分辨率，以提供最高质量的自适应比特率流式处理体验。 此预设对于中低复杂度视频非常有效，其中输出文件的比特率低于更静态的自适应流式处理预设，但质量仍可确保为受众提供良好的体验。 输出文件夹将包含多个 MP4 文件，这些文件包含已准备好流式传输的视频和音频。

## <a name="configure-output-settings"></a>配置输出设置

此外，在确定用于为自适应比特率流式处理梯度编码的最佳设置时，开发人员还可以控制内容感知编码预设使用的输出范围。

使用 PresetConfigurations 类，开发人员可以将一组约束和选项传入内容感知编码预设，以控制编码器生成的结果文件。 如果你想要将所有编码限制为特定的最大分辨率，以控制编码作业的体验或成本，则属性特别有用。  如果能够控制受众在移动网络或带宽受限的全球区域中可支持的最大和最小比特率，则也很有帮助。

## <a name="supported-codecs"></a>支持的编解码器

内容感知编码预设可与以下编解码器配合使用：
-  H.264
-  HEVC (H.265)

## <a name="how-to-use"></a>使用方法

有关在代码中使用该预设的详细信息以及完整示例的链接，请参阅[内容感知编码操作指南](./encode-content-aware-How-to.md)。

## <a name="technical-details-on-content-aware-preset"></a>内容感知预设的技术细节

现在，让我们更深入地了解内容感知编码预设的工作原理。  以下示例图形显示了使用 [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)和 [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion) 等质量指标做出的比较。 源的创建方式是将电影和电视节目中的高复杂性拍摄画面的简短剪辑相连接，旨在给编码器提供压力。 按照定义，此预设产生的结果因内容而异 – 这也意味着，对于某些内容，比特率可能不会显著降低，或者质量不会显著提高。

![使用 PSNR 的速率失真 (RD) 曲线](media/encode-content-aware-concept/msrv1.png)

图 1：**使用高复杂性源的 PSNR 指标的速率失真 (RD) 曲线**

![使用 VMAF 的速率失真 (RD) 曲线](media/encode-content-aware-concept/msrv2.png)

图 2：**使用高复杂性源的 VMAF 指标的速率失真 (RD) 曲线**

下面是另一个源内容类别的结果，其中，编码器可以确定输入质量较差（由于比特率较低，因此生成了很多压缩项目）。 使用内容感知预设时，编码器确定只生成一个输出层 – 该层的比特率足够低，使大多数客户端能够播放流，而不会出现停顿。

![使用 PSNR 的 RD 曲线](media/encode-content-aware-concept/msrv3.png)

图 3：**使用低质量输入的 PSNR 的 RD 曲线（分辨率为 1080p）**

![使用 VMAF 的 RD 曲线](media/encode-content-aware-concept/msrv4.png)

图 4：**使用低质量输入的 VMAF 的 RD 曲线（分辨率为 1080p）**

  
## <a name="next-steps"></a>后续步骤
* [如何使用内容感知编码预设](encode-content-aware-how-to.md)
* [教程：使用媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)