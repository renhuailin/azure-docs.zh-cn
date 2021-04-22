---
title: 媒体服务体系结构
description: 本文介绍媒体服务的体系结构。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: 4d14ab8b72e3e120e8ee8cc2be4ae29f20c32e87
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963640"
---
# <a name="media-services-architectures"></a>媒体服务体系结构

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>实时传送视频流数字媒体

实时流式处理解决方案用于实时捕获视频，并将其实时广播给使用者，例如在线进行的流式访谈、会议和体育活动。 在此解决方案中，摄像机捕获视频，并将视频发送到频道输入终结点。 频道接收实时输入流，并使其可通过流式处理终结点流式传输到 Web 浏览器或移动应用。 频道还提供了一个预览监视终结点，用于在进一步处理和传送流之前对流进行预览和验证。 频道还可以记录和存储引入的内容，以便稍后进行流式处理（视频点播）。

此解决方案基于 Azure 托管服务：媒体服务和内容分发网络。 这些服务在高可用性环境中运行，经过修补和支持改进，让你可专注于解决方案而不是其运行环境。

请参阅 Azure 体系结构中心中的[实时传送视频流数字媒体](/azure/architecture/solution-ideas/articles/digital-media-live-stream)。

## <a name="video-on-demand-digital-media"></a>点播视频数字媒体

一个基本的视频点播解决方案，让你能够将电影、新闻剪辑、体育片段、培训视频和客户支持教程等录制的视频内容流式传输到任何支持视频的终结点设备、移动应用程序或桌面浏览器。 视频文件上传到 Azure Blob 存储，编码为多比特率标准格式，然后通过所有主要的自适应比特率流式处理协议（HLS、MPEG-DASH、平滑）分发到 Azure Media Player 客户端。

此解决方案基于 Azure 托管服务：Blob 存储、内容分发网络和 Azure Media Player。 这些服务在高可用性环境中运行，经过修补和支持改进，让你可专注于解决方案而不是其运行环境。

请参阅 Azure 体系结构中心中的[视频点播数字媒体](/azure/architecture/solution-ideas/articles/digital-media-video)。

## <a name="gridwich-media-processing-system"></a>Gridwich 媒体处理系统

Gridwich 系统展示了在 Azure 上处理和传送媒体资产的最佳做法。 虽然 Gridwich 系统特定于媒体，但消息处理和事件框架可以应用于任何无状态事件处理工作流。

请参阅 Azure 体系结构中心中的 [Gridwich 媒体处理系统](/azure/architecture/reference-architectures/media-services/gridwich-architecture)。

## <a name="next-steps"></a>后续步骤

> [Azure 媒体服务概述](media-services-overview.md)