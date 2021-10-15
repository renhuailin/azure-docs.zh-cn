---
title: Azure 媒体服务中的直播活动状态和计费
description: 本主题概述了 Azure 媒体服务直播活动状态和计费。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 961c563a12a3edf4df5c898f0c8ee8f1a5b2daf0
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390114"
---
# <a name="live-event-states-and-billing"></a>直播活动状态和计费

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒体服务中，一旦直播活动的状态转换为“正在运行”或“等待”，就会开始计费 。 即使没有视频流经该服务，也会向你收费。 若要停止对直播活动的计费，必须停止直播活动。 实时听录的计费方式与直播活动相同。

将[直播活动](/rest/api/media/liveevents)上的 LiveEventEncodingType 设置为 Standard 或 Premium1080p 后，媒体服务就会自动关闭在输入源丢失 12 小时后仍处于“正在运行”状态但却没有“直播输出”运行的直播活动  。 但是，在直播活动处于“正在运行”状态的时间段内，仍会进行计费。

> [!NOTE]
> 直通（基本或标准）直播活动不会自动关闭，必须通过 API 显式停止，以避免过多计费。

## <a name="states"></a>状态

直播活动可能会处于以下任一状态。

|状态|说明|
|---|---|
|**已停止**| 这是直播活动在创建后的初始状态（除非设置了自动启动）此状态下不会发生计费。 直播活动无法接收任何输入。 |
|**正在启动**| 直播活动正在启动，资源已分配。 此状态下不会发生计费。  如果发生错误，则直播活动会返回到“已停止”状态。|
| **正在分配** | 已在直播活动上调用了分配操作，并且正在为此直播活动预配资源。 此操作成功完成后，直播活动将转换为“等待”状态。
|**等待**| 直播活动资源已预配，并且已准备好启动。 此状态下将进行计费。  大多数属性仍可进行更新，但在此状态下不允许引入或流式传输。
|**正在运行**| 已分配了直播活动资源，已生成了引入和预览 URL，并且能够接收实时传送流。 此时，计费处于活动状态。 必须显式对直播活动资源调用停止操作才能停止进一步计费。|
|**正在停止**| 正在停止直播活动并解除预配资源。 此暂时性状态下不会发生计费。 |
|**正在删除**| 正在删除直播活动。 此暂时性状态下不会发生计费。 |

你可以选择在创建直播活动时启用实时听录。 如果启用，每当直播活动处于“正在运行”状态时，系统会收取听录费用。 注意，即使没有音频流过直播活动，系统也会向你收费。

## <a name="next-steps"></a>后续步骤

- [实时传送视频流概述](stream-live-streaming-concept.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)
