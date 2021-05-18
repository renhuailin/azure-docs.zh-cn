---
title: 使用视频索引器进行实时传送流分析
titleSuffix: Azure Media Services
description: 本文介绍如何使用视频索引器执行实时传送流分析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "74185999"
---
# <a name="live-stream-analysis-with-video-indexer"></a>使用视频索引器进行实时传送流分析

Azure 媒体服务视频索引器是一项 Azure 服务，旨在从脱机的视频和音频文件中提取深入见解。 这是为了分析某个已经预先创建的给定媒体文件。 但是，对于有些用例，必须尽快从直播节目供给系统获取媒体见解，以解锁有时间压力的运营和其他用例。 例如，内容生产者可以使用实时传送流上的此类丰富元数据来自动进行电视节目生产。

本文所述的解决方案允许客户以直播节目供给系统上的准实时分辨率使用视频索引器。 通过使用这个解决方案，编制索引时的延迟时间可以缩短至四分钟，具体时长取决于编制索引的数据块、输入分辨率、内容类型，以及用于此过程的计算能力。

![实时传送流上的视频索引器元数据](./media/live-stream-analysis/live-stream-analysis01.png)

图 1 - 显示实时传送流上的视频索引器元数据的示例播放器

现有的[流分析解决方案](https://aka.ms/livestreamanalysis)使用 Azure Functions 和两个逻辑应用，通过视频索引器在 Azure 媒体服务中处理来自某个直播频道的直播节目，并使用 Azure Media Player 显示结果，结果中会呈现准实时生成的流。

概括地说，其中包含两个主要步骤。 第一个步骤每 60 秒运行一次，可获取最近 60 秒播放的子剪辑，根据该子剪辑创建资产并通过视频索引器为该子剪辑编制索引。 然后，在索引编制完成后，调用第二个步骤。 捕获到的见解会得到处理，发送到 Azure Cosmos DB，已编制索引的子剪辑会被删除。

示例播放器使用专用的 Azure 函数来播放实时传送流并从 Azure Cosmos DB 获取见解。 它会显示与直播视频同步的元数据和缩略图。

![在云中始终都在处理实时传送流的两个逻辑应用](./media/live-stream-analysis/live-stream-analysis02.png)

图 2 - 在云中始终都在处理实时传送流的两个逻辑应用。

## <a name="step-by-step-guide"></a>分步指南 

有关如何部署结果的完整代码和分步指南，可参阅[有关使用视频索引器进行实时媒体分析的 GitHub 项目](https://aka.ms/livestreamanalysis)。 

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)
