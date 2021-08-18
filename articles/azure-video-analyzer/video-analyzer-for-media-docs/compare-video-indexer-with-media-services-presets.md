---
title: 比较 Azure 视频分析器媒体版（以前为媒体索引器）与 Azure 媒体服务 v3 预设
description: 本文比较了 Azure 视频分析器媒体版（以前为媒体索引器）与 Azure 媒体服务 v3 预设二者的功能。
services: azure-video-analyzer
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: cdd354adc7a34cdcb7e0811ffd2a5d0b50018fee
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121500"
---
# <a name="compare-azure-media-services-v3-presets-and-video-analyzer-for-media"></a>比较 Azure 媒体服务 v3 预设与视频分析器媒体版 

本文比较了视频分析器媒体版（以前为媒体索引器）API 与媒体服务 v3 API 二者的功能。 

目前，[视频分析器媒体版 API](https://api-portal.videoindexer.ai/) 和[媒体服务 v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json) 提供的功能之间存在重叠。 下表提供当前用于了解二者异同的准则。 

## <a name="compare"></a>比较

|功能|视频分析器媒体版 API |媒体服务 v3 API 中的<br/>视频分析器和音频分析器预设|
|---|---|---|
|数据见解|[增强版](video-indexer-output-json-v2.md) |[基础](../../media-services/latest/analyze-video-audio-files-concept.md)|
|体验|查看支持的功能的完整列表： <br/> [概述](video-indexer-overview.md)|仅返回视频见解|
|计费|[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|合规性|有关最新的合规性更新，请访问 [Azure 合规性产品.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) 并搜索“视频分析器媒体版”，查看其是否符合相关证书的要求。|有关最新的符合性更新，请访问 [Azure 合规性产品.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) 并搜索“媒体服务”，查看其是否符合相关证书的要求。|
|免费试用版|美国东部|不可用|
|上市区域|请参阅[按区域划分的认知服务可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|请参阅[按区域划分的媒体服务可用性](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。|

## <a name="next-steps"></a>后续步骤

[视频分析器媒体版概述](video-indexer-overview.md)

[媒体服务 v3 概述](../../media-services/latest/media-services-overview.md)
