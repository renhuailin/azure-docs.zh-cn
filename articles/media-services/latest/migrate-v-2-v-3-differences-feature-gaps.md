---
title: Azure 媒体服务 V2 和 V3 之间的功能差距 |Microsoft Docs
description: 本文介绍 Azure 媒体服务 V2 与 v3 之间的功能差距。
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
ms.openlocfilehash: 0f15c2bcd921c431dba1d1cce0454a6b2e752141
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690313"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Azure 媒体服务 V2 与 V3 之间的功能差距

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-2.svg)

迁移指南的此部分提供了有关 V2 和 V3 Api 之间的差异的详细信息。

## <a name="feature-gaps-between-v2-and-v3-apis"></a>V2 和 V3 Api 之间的功能间隙

V3 API 与 V2 API 具有以下功能差距。 版本2中的 Media Encoder Standard 的两个高级功能当前在 V3 中不可用：

- 当输入不包含音频时插入无提示音频轨，因为 Azure Media Player 不再需要这样做。

- 当输入不包含视频时插入视频轨。

- 带有转码的实时事件当前不支持通过 API 调用将中间流和广告标记插入。

- V2 中不再支持 Azure 媒体高级编码器。 如果将其用于8位 HEVC 编码，请在标准编码器中使用新的 HEVC 支持。 
    - 添加了对标准编码器的音频通道映射的支持。  请参阅 [媒体服务编码 Swagger 文档中的音频](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)。
    - 如果你使用的是第三方许可产品（如 MXF 或 ProRes）的高级功能或输出格式，请使用 Telestream 中的 Azure 合作伙伴解决方案，该解决方案将在 V2 停用的时间进行交易。 或者，您也可以使用 "想象通信" 或 [Bitmovin](http://bitmovin.com)。

- 不再支持 V2 中流式处理终结点上的 "可用性集" 属性。 请参阅 V3 API 中的示例项目和 [高可用性 VOD](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding) 交付指南。

- 在媒体服务 V3 中，无法指定 FairPlay IV。 尽管这不会影响使用媒体服务进行打包和许可证传递的客户，但当使用第三方 DRM 系统将 FairPlay 许可证传送 (混合模式) 时，这可能会是一个问题。

- 用于保护静态资产的客户端存储加密已从 V3 API 中删除，由静态数据的存储服务加密取代。 V3 Api 继续使用现有的存储加密资产，但不允许创建新的资产。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
