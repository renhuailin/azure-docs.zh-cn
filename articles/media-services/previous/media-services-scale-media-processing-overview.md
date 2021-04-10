---
title: 媒体保留单位概述 | Microsoft Docs
description: 本文概述了如何使用 Azure 媒体服务缩放媒体处理能力。
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012711"
---
# <a name="media-reserved-units"></a>媒体保留单位

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

使用 Azure 媒体服务，可以通过管理媒体保留单位 (MRU) 来缩放媒体处理能力。 MRU 提供编码媒体所需的额外计算能力。 MRU 的数量决定了处理媒体任务的速度，以及在帐户中可并发处理的媒体任务数。 例如，如果帐户具有 5 个 MRU，并且有要处理的任务，就可以同时运行 5 个媒体任务。 其余任何任务将排队，并且在运行的任务完成后可被选择按顺序进行处理。 你预配的每个 MRU 都将导致产能预留，但不会为你提供专用资源。 在需求量极高的时候，你的所有 MRU 可能不会立即开始处理。

## <a name="choosing-between-different-reserved-unit-types"></a>在不同的预留单位类型之间进行选择

下表有助于在不同的编码速度之间进行选择时做出决定。  它显示了一个 7 分钟的 1080p 视频的编码持续时间（具体取决于所使用的 MRU）。

|RU 类型|方案|7 分钟 1080p 视频的示例结果 |
|---|---|---|
| **S1**|单比特率编码。 <br/>具有 SD 或更低分辨率的文件，不具有高时效性，成本低。|使用“H264 单比特率 SD 16x9”编码为单比特率 SD 分辨率 MP4 文件大约需要 7 分钟。|
| **S2**|单比特率和多比特率编码。<br/>SD 和 HD 编码的正常使用情况。|预设为“H264 单比特率 720p”的编码大约需要 6 分钟。<br/><br/>预设为“H264 多比特率 720p”的编码大约需要 12 分钟。|
| **S3**|单比特率和多比特率编码。<br/>全高清和 4K 分辨率视频。 对时间敏感，更快的编码周转。|预设为“H264 单比特率 1080p”的编码大约需要 3 分钟。<br/><br/>预设为“H264 多比特率 1080p”的编码大约需要 8 分钟。|

> [!NOTE]
> 如果你没有为帐户预配 MRU，则将以 S1 MRU 的性能处理你的媒体任务，并且将按顺序选取任务。 没有预留处理容量，因此完成一个任务和开始下一个任务之间的等待时间将取决于系统中资源的可用性。

## <a name="considerations"></a>注意事项

* 对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈推荐为帐户预配 10 个 S3 单位。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。
* 对于没有 MRU 的编码任务，任务在排队状态下可花费的时间没有上限，并且一次最多只能运行一个任务。

## <a name="billing"></a>计费

根据在帐户中预配的媒体预留单位的分钟数计费。 这与帐户中是否有作业运行无关。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。

## <a name="quotas-and-limitations"></a>配额和限制

有关配额和限制以及如何开具支持票证的信息，请参阅[配额和限制](media-services-quotas-and-limitations.md)。

## <a name="next-steps"></a>后续步骤

尝试通过以下技术之一调整媒体处理能力：

[.NET](media-services-dotnet-encoding-units.md)
[门户](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)