---
title: Azure 媒体服务旧组件 | Microsoft Docs
description: 本主题介绍 Azure 媒体服务旧组件。
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: d86a77e724bffeaea6cb39ffc1a20e8737ef6d81
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835536"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒体服务旧组件

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

随着时间的推移，我们会增强媒体服务组件并停用旧组件。 本文可帮助你将应用程序从旧组件迁移到当前组件。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>旧组件的停用计划和迁移指南

Windows Azure 媒体编码器 (WAME) 和 Azure 媒体编码器 (AME) 媒体处理器已弃用。

* [从 Windows Azure 媒体编码器迁移到 Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [从 Azure 媒体编码器迁移到 Media Encoder Standard](migrate-azure-media-encoder.md)

以下媒体分析媒体处理器已弃用或即将弃用：

 
| **媒体处理器名称** | **停用日期** | **其他说明** |
| --- | --- | ---|
| Azure Media Indexer | 2020 年 1 月 1 日 | 此媒体处理器将替换为[媒体服务 v3 AudioAnalyzerPreset 基本模式](../latest/analyze-video-audio-files-concept.md)。 有关详细信息，请参阅[从 Azure Media Indexer 2 迁移到 Azure 视频分析器媒体版](migrate-indexer-v1-v2.md)（以前称为“视频索引器”）。 |
| Azure Media Indexer 2 | 2023 年 3 月 1 日 | 此媒体处理器将替换为[媒体服务 v3 AudioAnalyzerPreset 基本模式](../latest/analyze-video-audio-files-concept.md)。 有关详细信息，请参阅[从 Azure Media Indexer 2 迁移到 Azure 视频分析器媒体版](migrate-indexer-v1-v2.md)（以前称为“视频索引器”）。 |
| 动作检测 | 2020 年 6 月 1 日|目前无替换计划。 |
| 视频摘要 |2020 年 6 月 1 日|目前无替换计划。|
| 视频光学字符识别 | 2020 年 6 月 1 日 |此媒体处理器已被 Azure 视频分析器媒体版取代。 另外，请考虑使用 [Azure 媒体服务 v3 API](../latest/analyze-video-audio-files-concept.md)。 <br/>请参阅[比较 Azure 媒体服务 v3 预设与视频分析器媒体版](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)。 |
| 面部检测器 | 2020 年 6 月 1 日 | 此媒体处理器已被 Azure 视频分析器媒体版取代。 另外，请考虑使用 [Azure 媒体服务 v3 API](../latest/analyze-video-audio-files-concept.md)。 <br/>请参阅[比较 Azure 媒体服务 v3 预设与视频分析器媒体版](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)。 |
| 内容审查器 | 2020 年 6 月 1 日 |此媒体处理器已被 Azure 视频分析器媒体版取代。 另外，请考虑使用 [Azure 媒体服务 v3 API](../latest/analyze-video-audio-files-concept.md)。 <br/>请参阅[比较 Azure 媒体服务 v3 预设与视频分析器媒体版](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)。 |
| 媒体编码器高级工作流 | 2024 年 2 月 29 日 | AMS v2 API 不再支持高级编码器。 如果以前将基于工作流的高级编码器用于 HEVC 编码，应迁移到具有 HEVC 编码支持的[新 v3 标准编码器](../latest/encode-media-encoder-standard-formats-reference.md)。 <br/> 如果需要高级编码器的高级工作流功能，建议你开始使用来自 [Imagine Communications](https://imaginecommunications.com/)、[Telestream](https://telestream.net) 或 [Bitmovin](https://bitmovin.com) 的 Azure 高级编码合作伙伴。 |

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](../latest/migrate-v-2-v-3-migration-introduction.md)
