---
title: 基于方案的打包和交付方案的迁移指南
description: 本文提供了有关打包和传递的基于方案的指南，可帮助你从 Azure 媒体服务 v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 78f3e6ee53d5158096882f5554054c3ff06a32b4
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895231"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>基于方案的打包和交付方案的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-4.svg)

本文为你提供了打包和传递的基于方案的指南，可帮助你从 Azure 媒体服务 v2 迁移到 v3。

V3 API 中发布内容的方式的重大更改。 新的发布模型已简化，并使用较少的实体来创建流式处理定位符。 API 缩减为仅限两个实体，而不是之前所需的四个实体。 内容密钥策略和流式定位符现在替代了、、和的需求 `ContentKeyAuthoriationPolicy` `AssetDeliveyPolicy` `ContentKey` `AccessPolicy` 。

## <a name="packaging-and-delivery-in-v3"></a>V3 中的打包和交付

1. 创建 [内容密钥策略](content-key-policy-concept.md)。
1. 创建 [流式处理](streaming-locators-concept.md)定位符。
1. 获取 [流路径](create-streaming-locator-build-url.md) 
    1. 将其配置为 [短划线](dynamic-packaging-overview.md#mpeg-dash-protocol) 或 [HLS](dynamic-packaging-overview.md#hls-protocol) 播放器。

有关具体步骤，请参阅发布概念、教程和操作方法指南。

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>发布概念、教程和操作方法指南

### <a name="concepts"></a>概念

- [媒体服务 v3 中的动态打包](dynamic-packaging-overview.md)
- [筛选器](filters-concept.md)
- [使用动态打包器筛选清单](filters-dynamic-manifest-overview.md)
- [Azure 媒体服务中的流式处理终结点（来源）](streaming-endpoint-concept.md)
- [通过 CDN 集成流式传输内容](scale-streaming-cdn.md)
- [流式处理定位符](streaming-locators-concept.md)

### <a name="how-to-guides"></a>操作方法指南

- [用媒体服务 v3 管理流式处理终结点](manage-streaming-endpoints-howto.md)
- [CLI 示例：发布资产](cli-publish-asset.md)
- [创建流定位器并生成 URL](create-streaming-locator-build-url.md)
- [下载作业结果](download-results-howto.md)
- [指示描述性音频轨道](signal-descriptive-audio-howto.md)
- [Azure Media Player 完全安装](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-full-setup)
- [如何在 Azure 媒体服务中使用 Video.js 播放器](how-to-video-js-player.md)
- [如何将 Shaka player 用于 Azure 媒体服务](how-to-shaka-player.md)

## <a name="samples"></a>示例

你还可以 [在代码示例中比较 V2 和 V3 代码](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
