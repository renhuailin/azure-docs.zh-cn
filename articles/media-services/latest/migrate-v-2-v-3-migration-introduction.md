---
title: 从 Azure 媒体服务 v2 迁移到 v3 简介 |Microsoft Docs
description: 本文介绍如何从 Media Services v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: fb9abd8f3186405edc31b4af48ee98482e080c68
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690258"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>从 Media Services v2 迁移到 v3 简介

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

媒体服务迁移指南可帮助你根据使用现有的新功能和功能的迁移，从媒体服务 V2 Api 迁移到 V3 Api。 你应使用最佳判断，并确定最适合你的方案。

## <a name="how-to-use-this-guide"></a>如何使用本指南

### <a name="navigating"></a>导航

在整个指南中，你将看到以下图形。

![迁移步骤](./media/migration-guide/steps.svg)<br/>

你所在的步骤将通过步骤编号中的颜色变化来表示，如下所示：

![迁移步骤2](./media/migration-guide/steps-2.svg)<br/>

在每个页面的末尾，你将看到其他迁移文档的链接，你可以在 **后续步骤** 标题下读取这些内容。

### <a name="guidance"></a>指南

此处提供的指南是 *常规* 指南。 它包括的内容可用于改进对 V3 中提供的内容以及媒体服务工作流中的更改。

有关更详细的指南，包括屏幕截图和概念图形，请参考每个基于方案的主题中的概念、教程、快速入门、示例和 API 参考的链接。 我们还列出了可帮助你将 V2 API 与 V3 API 进行比较的示例。

## <a name="migration-guidance-overview"></a>迁移指南概述

在迁移过程中需要执行以下四个常规步骤：

## <a name="step-1-benefits"></a>步骤1的优点

<hr color="#5ea0ef" size="10">

[了解](migrate-v-2-v-3-migration-benefits.md) 迁移到媒体服务 API V3 的好处。

## <a name="step-2-differences"></a>步骤2差异

<hr color="#5ea0ef" size="10">

了解媒体服务 V2 API 与 V3 API 之间的差异。

- [API 访问](migrate-v-2-v-3-differences-api-access.md)
- [功能间隙](migrate-v-2-v-3-differences-feature-gaps.md)
- [术语和实体更改](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>步骤 3 SDK 安装

<hr color="#5ea0ef" size="10">

了解 SDK 差异，并 [将其设置为迁移到 V3 REST API 或客户端 SDK](migrate-v-2-v-3-migration-setup.md)。

## <a name="step-4-scenario-based-guidance"></a>步骤4基于方案的指南

<hr color="#5ea0ef" size="10">

媒体服务 V2 的应用程序可能是唯一的。 因此，我们提供了基于方案的指南，其中基于过去 *你如何* 使用媒体服务以及服务的每项功能的步骤，如：

- [编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [实时流式处理](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [打包和交付](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [内容保护](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [媒体保留单位 (MRU) ](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
