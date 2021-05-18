---
title: 媒体服务实时流式处理迁移指南
description: 本文提供基于实时传送视频流场景的指南，可帮助你从 Azure 媒体服务 v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d021267c38f0043d2361b1a6392fbacd0634a164
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279691"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>基于实时传送视频流场景的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-4.svg)

Azure 门户现在支持直播活动设置和管理。  欢迎在测试 V2 到 V3 的迁移时试用。

## <a name="test-the-v3-live-event-workflow"></a>测试 V3 直播活动工作流

> [!NOTE]
> 使用 V3 api 来管理通过 v2 创建的频道和节目（映射到 v3 中的直播活动和实时输出）。 本指南介绍当你方便停止现有 V2 通道时，如何切换到 V3 直播活动和实时输出。 目前不支持将连续运行的全天候实时频道无缝迁移到新的 V3 直播活动。 因此，需要将维护停机时间协调在受众和企业都方便的时候。

在将内容从 V2 迁移到 V3 之前，测试通过媒体服务交付直播活动的新方式。 下面是要使用并考虑迁移的 V3 功能。

- 创建新的 v3 [直播活动](live-event-outputs-concept.md#live-events)进行编码。 可以启用 [1080P 和 720P 编码预设](live-event-types-comparison-reference.md#system-presets)。
- 使用[实时输出](live-event-outputs-concept.md#live-outputs)实体而不是节目
- 创建[创建流定位器](stream-streaming-locators-concept.md)。
- 请考虑是否需要 [HLS 和 DASH](encode-dynamic-packaging-concept.md) 实时传送视频流。
- 如果需要快速启动直播活动，请了解新的[备用模式](live-event-outputs-concept.md#standby-mode)功能。
- 如果要在实时事件发生时对其进行转录，请浏览新推出的[实时听录](live-event-live-transcription-how-to.md)功能。
- 如果需要较长的流式传输持续时间，请在 v3 中创建 24x7x365 直播活动。
- 使用[事件网格](monitoring/monitor-events-portal-how-to.md)来监视直播活动。

有关具体步骤，请参阅以下直播活动概念、教程和操作指南。

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>直播活动概念、教程和操作指南

### <a name="concepts"></a>概念

- [使用 Azure 媒体服务 v3 实时传送视频流](stream-live-streaming-concept.md)
- [媒体服务中的实时事件和实时输出](live-event-outputs-concept.md)
- [经过验证的本地实时传送视频流编码器](encode-recommended-on-premises-live-encoders.md)
- [使用时移和实时输出创建点播视频](live-event-cloud-dvr-time-how-to.md)
- [直播活动实时转录（预览）](live-event-live-transcription-how-to.md)
- [实时事件类型比较](live-event-types-comparison-reference.md)
- [直播活动状态和计费](live-event-states-billing-concept.md)
- [直播活动低延迟设置](live-event-latency-reference.md)
- [媒体服务直播活动错误代码](live-event-error-codes-reference.md)

### <a name="tutorials-and-quickstarts"></a>教程和快速入门

- [教程：使用媒体服务进行实时流式传输](stream-live-tutorial-with-api.md)
- [使用 OBS 创建 Azure 媒体服务实时传送流](live-event-obs-quickstart.md)
- [快速入门：使用门户上传、编码和流式传输内容](asset-create-asset-upload-portal-quickstart.md)
- [快速入门：使用 Wirecast 创建 Azure 媒体服务实时传送流](live-event-wirecast-quickstart.md)

## <a name="samples"></a>示例

还可[将代码示例中的 V2 和 V3 代码进行比较](migrate-v-2-v-3-migration-samples.md)。
