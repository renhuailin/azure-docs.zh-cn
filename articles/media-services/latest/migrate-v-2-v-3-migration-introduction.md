---
title: 关于从媒体服务 v2 迁移到 v3 的介绍
description: 本文介绍如何从媒体服务 v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 49d804fdf354dc942ca256e1b5960fd201358a81
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114708174"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>关于从媒体服务 v2 迁移到 v3 的介绍

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

媒体服务迁移指南可帮助你基于利用现有新功能的迁移，从媒体服务 V2 API 迁移到 V3 API。 你应该利用自己的最佳判断力来确定最适合你的情况的方式。

[!INCLUDE [v2 deprecation notice](./includes/v2-deprecation-notice.md)]

## <a name="how-to-use-this-guide"></a>如何使用本指南

### <a name="navigating"></a>导航

在整个指南中，你将看到以下图形。

![迁移步骤](./media/migration-guide/steps.svg)<br/>

你所在的步骤将通过步骤编号的颜色变化来表示，如下所示：

![迁移步骤 2](./media/migration-guide/steps-2.svg)<br/>

在每页末尾，你将在“后续步骤”标题下看到指向其余迁移文档的链接。

### <a name="guidance"></a>指南

此处提供的指南是“常规”。 它包含的内容可让你更了解 V3 中现在可用的内容以及媒体服务工作流中的已更改内容。

如需更详细的指导（包括屏幕截图和概念图），可以使用每个基于场景的主题提供的指向概念、教程、快速入门、示例和 API 参考的链接。 我们还列出了一些示例，有助于你将 V2 API 与 V3 API 进行比较。

## <a name="migration-guidance-overview"></a>迁移指南概述

在迁移过程中，需要执行以下四个常规步骤：

## <a name="step-1-benefits"></a>步骤 1 好处

<hr color="#5ea0ef" size="10">

[了解迁移到媒体服务 API V3 的好处](migrate-v-2-v-3-migration-benefits.md)。

## <a name="step-2-differences"></a>步骤 2 区别

<hr color="#5ea0ef" size="10">

了解媒体服务 V2 API 与 V3 API 之间的区别。

- [API 访问](migrate-v-2-v-3-differences-api-access.md)
- [功能差异](migrate-v-2-v-3-differences-feature-gaps.md)
- [术语和实体更改](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>步骤 3 SDK 设置

<hr color="#5ea0ef" size="10">

了解 SDK 区别，并进行[设置以迁移到 V3 REST API 或客户端 SDK](migrate-v-2-v-3-migration-setup.md)。

## <a name="step-4-scenario-based-guidance"></a>步骤 4 基于场景的指南

<hr color="#5ea0ef" size="10">

你的媒体服务 V2 应用场景可能是唯一的。 因此，我们基于你过去可能使用媒体服务的方式以及该服务每个功能的步骤提供了基于应用场景的指导，例如：

- [编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [实时传送视频流](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [打包和交付](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [内容保护](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [媒体保留单位 (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)
