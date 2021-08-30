---
title: 媒体保留单位 - Azure
description: 媒体保留单位可用于缩放媒体处理能力并决定媒体处理任务的速度。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 44205bc628a839dd28cd574dbd19a22e9856d999
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719419"
---
# <a name="media-reserved-units"></a>媒体预留单位

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒体服务，可以通过管理媒体保留单位 (MRU) 来缩放媒体处理能力。 MRU 提供编码媒体所需的额外计算能力。 MRU 的数量决定了处理媒体任务的速度，以及在帐户中可并发处理的媒体任务数。 例如，如果帐户具有 5 个 MRU，并且有要处理的任务，就可以同时运行 5 个媒体任务。 其余任何任务将排队，并且在运行的任务完成后可被选择按顺序进行处理。 你预配的每个 MRU 都将导致产能预留，但不会为你提供专用资源。 在需求量极高的时候，你的所有 MRU 可能不会立即开始处理。

任务是对资产执行的单个操作，例如自适应流式处理编码。 提交作业时，媒体服务负责将作业分解为单个的操作（即任务），然后这些操作将与单独的 MRU 相关联。

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

* 对于由媒体服务 v3 或 Azure 视频分析器媒体版触发的音频分析和视频分析作业，强烈推荐为帐户预配 10 个 S3 单位。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。
* 对于没有 MRU 的编码任务，任务在排队状态下可花费的时间没有上限，并且一次最多只能运行一个任务。

## <a name="billing"></a>计费

计费金额基于在帐户中预配的媒体保留单位的分钟数（无论是否有任何作业正在运行）。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。

## <a name="next-step"></a>后续步骤
[使用 CLI 缩放媒体保留单位](media-reserved-units-cli-how-to.md)
[分析视频](analyze-videos-tutorial.md)

## <a name="see-also"></a>另请参阅

* [配额和限制](limits-quotas-constraints-reference.md)
