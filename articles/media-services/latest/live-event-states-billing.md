---
title: Azure 媒体服务中的实时事件状态和计费 |Microsoft Docs
description: 本主题概述 Azure 媒体服务实时事件状态和计费。
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
ms.openlocfilehash: 2d3d3f5c56bd42aeb148c19fefebc0e7d364cd1c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782359"
---
# <a name="live-event-states-and-billing"></a>实时事件状态和计费

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒体服务中，实时事件会在其状态转换为 " **正在运行** " 或 " **待机** " 时立即开始计费。 即使没有视频流经该服务，也会向你收费。 若要停止计费活动事件，必须停止实时事件。 实时脚本按与实时事件相同的方式计费。

如果 [live 事件](/rest/api/media/liveevents)上的 **LiveEventEncodingType** 设置为 Standard 或 Premium1080p，则媒体服务自动关闭输入源丢失12小时后仍处于 **运行** 状态的任何实时事件，并且没有正在运行的 **活动输出** 。 但是，在实时事件处于 " **正在运行** " 状态时，仍会向你收费。

> [!NOTE]
> 传递实时事件不会自动关闭，必须通过 API 显式停止，以避免过多的计费。

## <a name="states"></a>状态

实时事件可能处于以下状态之一。

|状态|说明|
|---|---|
|**已停止**| 这是创建 (后实时事件的初始状态，除非自动启动设置为 true。 ) 在此状态下不会发生计费。 实时事件无法接收任何输入。 |
|**正在启动**| 实时事件正在启动，资源已分配。 此状态下不会发生计费。  如果发生错误，则实时事件返回到停止状态。|
| **误** | 已在实时事件上调用了分配操作，正在为此实时事件预配资源。 成功完成此操作后，实时事件会转换为待机状态。
|**转入**| 实时事件资源已预配，并且已准备好启动。 计费发生在此状态。  大多数属性仍可进行更新，但在此状态下，不允许引入或流式传输。
|**正在运行**| 已分配实时事件资源，已生成引入和预览 Url，并且能够接收实时流。 此时，计费处于活动状态。 必须显式对直播活动资源调用停止操作才能停止进一步计费。|
|**正在停止**| 正在停止实时事件，正在取消预配资源。 此暂时性状态下不会发生计费。 |
|**正在删除**| 正在删除实时事件。 此暂时性状态下不会发生计费。 |

你可以选择在创建实时事件时启用实时转录。 如果这样做，每当实时事件处于 " **正在运行** " 状态时，就会向你收取转录费用。 请注意，即使没有通过实时事件流动的音频，也会向你收费。

## <a name="next-steps"></a>后续步骤

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)
