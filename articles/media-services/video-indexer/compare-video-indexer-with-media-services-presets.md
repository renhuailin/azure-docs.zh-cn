---
title: 视频索引器和 Azure 媒体服务 v3 预设的比较
description: 本文比较视频索引器功能和 Azure 媒体服务 v3 预设。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: ceb105409ff218cd633eb36d793e8fc16c7d135c
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961534"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>比较 Azure 媒体服务 v3 预设和视频索引器 

本文比较视频索引器 API 和媒体服务 v3 API 的功能。 

目前，[视频索引器 API](https://api-portal.videoindexer.ai/) 和[媒体服务 v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json) 提供的功能之间存在重叠。 下表提供当前用于了解二者异同的准则。 

## <a name="compare"></a>比较

|功能|视频索引器 API |媒体服务 v3 API 中的<br/>视频分析器和音频分析器预设|
|---|---|---|
|数据见解|[增强版](video-indexer-output-json-v2.md) |[基础](../latest/analyze-video-audio-files-concept.md)|
|体验|查看支持的功能的完整列表： <br/> [概述](video-indexer-overview.md)|仅返回视频见解|
|计费|[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|合规性|有关最新的符合性更新，请访问 [Azure 合规性产品.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) 并搜索“视频索引器”，查看其是否符合相关证书的要求。|有关最新的符合性更新，请访问 [Azure 合规性产品.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) 并搜索“媒体服务”，查看其是否符合相关证书的要求。|
|免费试用版|美国东部|不可用|
|上市区域|请参阅[按区域划分的认知服务可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|请参阅[按区域划分的媒体服务可用性](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。|

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)

[媒体服务 v3 概述](../latest/media-services-overview.md)
