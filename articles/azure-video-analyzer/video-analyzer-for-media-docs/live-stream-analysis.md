---
title: 使用 Azure 视频分析器媒体版（之前称为视频索引器）进行实时流分析
description: 本文演示如何使用 Azure 视频分析器媒体版（之前称为视频索引器）进行实时流分析。
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 18017bbe4d83d11e360efebd7d69eed8d67ae15a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627437"
---
# <a name="live-stream-analysis-with-video-analyzer-for-media"></a>使用视频分析器媒体版进行实时流分析

Azure 视频分析器媒体版（之前称为视频索引器）这一项 Azure 服务旨在从离线视频和音频文件中提取深刻见解。 这是为了分析某个已经预先创建的给定媒体文件。 但是，对于有些用例，必须尽快从直播节目供给系统获取媒体见解，以解锁有时间压力的运营和其他用例。 例如，内容生产者可以使用实时传送流上的此类丰富元数据来自动进行电视节目生产。

利用本文所述的解决方案，客户能够以直播节目供给系统上的准实时分辨率使用视频分析器媒体版。 通过使用这个解决方案，编制索引时的延迟时间可以缩短至四分钟，具体时长取决于编制索引的数据块、输入分辨率、内容类型，以及用于此过程的计算能力。

![实时传送流上的视频分析器媒体版元数据](./media/live-stream-analysis/live-stream-analysis01.png)

图 1 - 显示实时传送流上的视频分析器媒体版元数据的示例播放器

现有的[流分析解决方案](https://aka.ms/livestreamanalysis)使用 Azure Functions 和两个逻辑应用，通过视频分析器媒体版在 Azure 媒体服务中处理来自某个直播频道的直播节目，并使用 Azure Media Player 显示结果，结果中会呈现准实时生成的流。

概括地说，其中包含两个主要步骤。 第一个步骤每 60 秒运行一次，可获取最近 60 秒播放的子剪辑，根据该子剪辑创建资产并通过视频分析器媒体版为该子剪辑编制索引。 然后，在索引编制完成后，调用第二个步骤。 捕获到的见解会得到处理，发送到 Azure Cosmos DB，已编制索引的子剪辑会被删除。

示例播放器使用专用的 Azure 函数来播放实时传送流并从 Azure Cosmos DB 获取见解。 它会显示与直播视频同步的元数据和缩略图。

![在云中始终都在处理实时传送流的两个逻辑应用](./media/live-stream-analysis/live-stream-analysis02.png)

图 2 - 在云中始终都在处理实时传送流的两个逻辑应用。

## <a name="step-by-step-guide"></a>分步指南 

有关如何部署结果的完整代码和分步指南，可参阅[有关使用视频分析器媒体版进行实时媒体分析的 GitHub 项目](https://aka.ms/livestreamanalysis)。 

## <a name="next-steps"></a>后续步骤

[视频分析器媒体版概述](video-indexer-overview.md)
