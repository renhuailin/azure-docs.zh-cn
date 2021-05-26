---
title: Azure 媒体服务旧组件 | Microsoft Docs
description: 本主题介绍 Azure 媒体服务旧组件。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: b1e8cbc61d228c5baa58e5c303392a062fd0fa13
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367040"
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
| Azure Media Indexer 2 | 2020 年 1 月 1 日 | 此媒体处理器将替换为[媒体服务 v3 AudioAnalyzerPreset 基本模式](../latest/analyze-video-audio-files-concept.md)。 有关详细信息，请参阅[从 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)。 |
| Azure Media Indexer | 2023 年 3 月 1 日 | 此媒体处理器将替换为[媒体服务 v3 AudioAnalyzerPreset 基本模式](../latest/analyze-video-audio-files-concept.md)。 有关详细信息，请参阅[从 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)。 |
| 动作检测 | 2020 年 6 月 1 日|目前无替换计划。 |
| 视频摘要 |2020 年 6 月 1 日|目前无替换计划。|
| 视频光学字符识别 | 2020 年 6 月 1 日 |此媒体处理器被 Azure 媒体服务视频索引器替换。 另外，请考虑使用 [Azure 媒体服务 v3 API](../latest/analyze-video-audio-files-concept.md)。 <br/>请参阅比较 Azure 媒体服务 v3 预设和视频索引器。 |
| 面部检测器 | 2020 年 6 月 1 日 | 此媒体处理器被 Azure 媒体服务视频索引器替换。 另外，请考虑使用 [Azure 媒体服务 v3 API](../latest/analyze-video-audio-files-concept.md)。 <br/>请参阅比较 Azure 媒体服务 v3 预设和视频索引器。 |
| 内容审查器 | 2020 年 6 月 1 日 |此媒体处理器被 Azure 媒体服务视频索引器替换。 另外，请考虑使用 [Azure 媒体服务 v3 API](../latest/analyze-video-audio-files-concept.md)。 <br/>请参阅比较 Azure 媒体服务 v3 预设和视频索引器。 |

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](../latest/migrate-v-2-v-3-migration-introduction.md)
