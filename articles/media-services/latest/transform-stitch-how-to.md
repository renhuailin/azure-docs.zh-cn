---
title: 如何使用 .NET 拼结两个或更多视频文件 | Microsoft Docs
description: 本文介绍如何拼结两个或更多视频文件。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: c94976fd494cd4745a5da95557f3e6a4954e721a
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930652"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>如何使用 .NET 拼结两个或更多视频文件

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>拼结两个或更多视频文件

以下示例演示如何生成预设来拼结两个或更多视频文件。 最常见的应用场景：希望在主视频中添加标题或预告片。

> [!NOTE]
> 一起编辑的视频文件应共享属性（视频分辨率、帧速率、音轨计数等）。 务必注意不要混合使用不同帧速率或不同音轨数的视频。

## <a name="prerequisites"></a>先决条件

克隆或下载[媒体服务 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet/)。  下面引用的代码位于 [EncodingWithMESCustomStitchTwoAssets 文件夹](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)中。
