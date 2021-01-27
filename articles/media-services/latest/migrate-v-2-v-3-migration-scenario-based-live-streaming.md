---
title: 用于实时流式处理的媒体服务 v2 到 v3 迁移方案指南
description: 本文提供了基于实时流式处理方案的指南，可帮助你从 Azure 媒体服务 v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 65b1c8906463002e144ee520855121ce18693e03
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898267"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>实时流式处理基于方案的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-4.svg)

Azure 门户现在支持实时事件设置和管理。  建议在测试 V2 到 V3 迁移时尝试此方法。

## <a name="test-the-v3-live-event-workflow"></a>测试 V3 实时事件工作流

> [!NOTE]
> 用 v2 创建的通道和程序 (映射到中的实时事件和实时输出) 可通过 V3 api 进行管理。 本指南旨在在你可以停止现有 V2 通道时，在方便的时候切换到 V3 实时事件和实时输出。 目前尚不支持将连续运行的24x7 实时通道无缝迁移到新的 V3 实时事件。 因此，维护停机时间需要协调，以便为您的受众和企业提供最好的便利。

在将内容从 V2 移动到 V3 之前，请测试通过媒体服务传送实时事件的新方式。 下面是要使用的 V3 功能，并考虑进行迁移。

- 创建新的 v3 [实时事件](live-events-outputs-concept.md#live-events) 进行编码。 可以启用 [1080p 和720p 编码预设](live-event-types-comparison.md#system-presets)。
- 使用 [实时输出](live-events-outputs-concept.md#live-outputs) 实体而不是程序
- 创建 [流式处理](streaming-locators-concept.md)定位符。
- 考虑你对 [HLS](dynamic-packaging-overview.md) 实时流式处理的需求。
- 如果需要快速启动实时事件，请浏览新的 [待机模式](live-events-outputs-concept.md#standby-mode) 功能。
- 如果要在实时事件发生时对其进行转录，请浏览新的 [实时](live-transcription.md) 脚本功能。
- 如果需要更长的流式处理持续时间，请在 v3 中创建24x7x365 实时事件。
- 使用 [事件网格](monitor-events-portal-how-to.md) 监视实时事件。

有关具体步骤，请参阅下面的实时事件概念、教程和操作方法指南。

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>实时事件概念、教程和操作方法指南

### <a name="concepts"></a>概念

- [使用 Azure 媒体服务 v3 实时传送视频流](live-streaming-overview.md)
- [媒体服务中的实时事件和实时输出](live-events-outputs-concept.md)
- [经过验证的本地实时传送视频流编码器](recommended-on-premises-live-encoders.md)
- [使用时移和实时输出创建点播视频](live-event-cloud-dvr.md)
- [实时 (预览) ](live-transcription.md)
- [实时事件类型比较](live-event-types-comparison.md)
- [直播活动状态和计费](live-event-states-billing.md)
- [直播活动低延迟设置](live-event-latency.md)
- [媒体服务直播活动错误代码](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>教程和快速入门

- [教程：使用媒体服务进行实时流式传输](stream-live-tutorial-with-api.md)
- [使用 OBS 创建 Azure 媒体服务实时传送流](live-events-obs-quickstart.md)
- [快速入门：使用门户上传、编码和流式传输内容](manage-assets-quickstart.md)
- [快速入门：使用 Wirecast 创建 Azure 媒体服务实时流](live-events-wirecast-quickstart.md)

## <a name="samples"></a>示例

你还可以 [在代码示例中比较 V2 和 V3 代码](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
