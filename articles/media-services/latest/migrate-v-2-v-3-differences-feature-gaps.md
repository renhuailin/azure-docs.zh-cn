---
title: Azure 媒体服务 V2 和 V3 之间的功能差异
description: 本文介绍 Azure 媒体服务 V2 与 V3 之间的功能差异。
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
ms.openlocfilehash: f77a41a172bc868ad056d74d018b50f734286f0f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702447"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Azure 媒体服务 V2 和 V3 之间的功能差异

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-2.svg)

迁移指南的此部分将详细介绍 V2 和 V3 API 之间的差异。

## <a name="feature-gaps-between-v2-and-v3-apis"></a>V2 和 V3 API 之间的功能差异

与 V2 API 相比，V3 API 存在以下功能差异。 V2 API 中的一些 Media Encoder Standard 高级功能目前在 V3 中不可用：

- 在输入不包含音频时插入静音轨迹，因为 Azure Media Player 不再需要此功能。

- 在输入不包含视频时插入视频轨迹。

- 包含转码的直播活动目前不支持静态图像插入中间流，以及通过 API 调用执行的广告标记插入。

- V2 中不再支持 Azure 媒体高级编码器。 如果要使用它进行 8 位 HEVC 编码，请在标准编码器中使用新的 HEVC 支持。 
    - 添加了对音频通道映射到标准编码器的支持。  请参阅[媒体服务编码 Swagger 文档中的音频](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)。
    - 如果你使用的是第三方许可产品（如 MXF 或 ProRes）的高级功能或输出格式，请使用 Telestream 中的 Azure 合作伙伴解决方案，该解决方案在 V2 停用时可以处理事务。 或者，你可以使用 Imagine Communications 或 [Bitmovin](http://bitmovin.com)。

- 不再支持 V2 中流式处理终结点上的 "可用性集" 属性。 请参阅 V3 API 中[高可用性 VOD](./media-services-high-availability-encoding.md) 交付的示例项目和指南。

- 在媒体服务 V3 中，无法指定 FairPlay IV。 尽管这不会影响使用媒体服务进行打包和许可证传递的客户，但在使用第三方 DRM 系统提供 FairPlay 许可证（混合模式）时可能会遇到问题。

- 用于保护静态资产的客户端存储加密已从 V3 API 中删除，并由静态数据的存储服务加密代替。 V3 API 会继续处理现有的存储加密资产，但不会允许创建新资产。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]