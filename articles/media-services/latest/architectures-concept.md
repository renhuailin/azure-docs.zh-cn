---
title: 媒体服务体系结构
titleSuffix: Azure Media Services
description: 本文介绍 Media Services 的体系结构。
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
ms.openlocfilehash: e00079190371b3ae0e318aaadd8cf724d9d339d0
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512561"
---
# <a name="media-services-architectures"></a>媒体服务体系结构

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>实时传送视频流数字媒体

实时流式处理解决方案使你可以实时捕获视频，并实时将其广播给消费者，如流式访谈、会议和体育活动。 在此解决方案中，视频通过视频相机捕获并发送到频道输入终结点。 通道接收实时输入流，并使其可用于通过流式处理终结点流式传输到 web 浏览器或移动应用。 通道还提供预览监视终结点，用于在进一步处理和传递之前预览和验证流。 频道还可以记录和存储引入的内容，以便以后 (视频点播) 进行流式处理。

此解决方案基于 Azure 托管服务：媒体服务和内容交付网络。 这些服务在高可用性环境中运行，进行了修补和支持，使你可以专注于解决方案而不是运行它们的环境。

请参阅 Azure 体系结构中心中的 [实时流式处理数字媒体](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-live-stream) 。

## <a name="instant-broadcasting-on-serverless-architecture"></a>无服务器体系结构的即时广播

使用无服务器代码简化一对多实时通信和更新。 此体系结构使用 Azure Functions、SignalR 服务、存储帐户和 SQL 数据库。

请参阅 Azure 体系结构中心的 [无服务器体系结构的即时广播](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/instant-broadcasting-on-serverless-architecture) 。

## <a name="video-on-demand-digital-media"></a>点播视频数字媒体

基本的视频点播解决方案，使你能够将电影、新闻剪辑、体育段、培训视频和客户支持教程等录制的视频内容流式传输到任何支持视频的终结点设备、移动应用程序或桌面浏览器。 视频文件将上传到 Azure Blob 存储，编码为多比特率标准格式，然后通过所有主要自适应比特率流式处理协议分发 (HLS，MPEG-短线，平滑) 到 Azure Media Player 客户端。

此解决方案基于 Azure 托管服务： Blob 存储、内容交付网络和 Azure Media Player。 这些服务在高可用性环境中运行，进行了修补和支持，使你可以专注于解决方案而不是运行它们的环境。

请参阅 Azure 体系结构中心的 [视频点播数字媒体](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-video) 。

## <a name="gridwich-media-processing-system"></a>Gridwich 媒体处理系统

Gridwich 系统体现了在 Azure 上处理和传送媒体资产的最佳实践。 尽管 Gridwich 系统是特定于媒体的，但消息处理和事件框架可以应用于任何无状态事件处理工作流。

请参阅 Azure 体系结构中心中的 [Gridwich media 处理系统](https://docs.microsoft.com/azure/architecture/reference-architectures/media-services/gridwich-architecture) 。

## <a name="next-steps"></a>后续步骤

> [Azure 媒体服务概述](media-services-overview.md)