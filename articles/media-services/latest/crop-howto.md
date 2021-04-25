---
title: 如何用“媒体服务”裁剪视频文件-.NET |Microsoft Docs
description: 裁剪是指在视频帧内选择一个矩形窗口并且只编码该窗口内的像素的过程。 本主题演示如何使用.NET 通过“媒体服务”裁剪视频文件。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572196"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>如何用 媒体服务 - .NET 裁剪视频文件

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

可以使用“媒体服务”裁剪输入视频。 裁剪是指在视频帧内选择一个矩形窗口并且只编码该窗口内的像素的过程。 下图可帮助说明该过程。

## <a name="pre-processing-stage"></a>预处理阶段

裁剪是预处理阶段，因此，编码预设​​值中的裁剪参数适用于输入视频。 编码是后续阶段，因此，宽度/高度设置适用于 *经过预先处理* 的视频，而非原始视频。 计划预设时，请执行以下操作：

1. 根据原始输入视频选择裁剪参数
1. 根据裁剪后的视频选择编码设置。

> [!WARNING]
> 如果编码设置与裁剪后的视频不匹配，输出会不如预期。

比如你输入的视频分辨率为 1920x1080 像素（16:9 纵横比），不过因为左侧和右侧有黑色长条（圆柱框），导致包含活动视频的区域只有 4:3 窗口或 1440x1080 像素。 您可以裁剪黑色条，并对 1440x1080 区域编码。

## <a name="transform-code"></a>转换代码

下面的代码段演示如何在.NET 中编写转换码来裁剪视频。  代码假设你有一个要处理的本地文件。

- “Left”是裁剪的最左侧位置。
- “Top”是裁剪最顶部的位置。
- “Width”为裁剪的最终宽度。
- “Height”为剪裁的最终高度。

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
