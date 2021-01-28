---
title: 媒体保留单位 (Mru) 迁移指南
description: 本文提供了基于 MRU 方案的指南，可帮助你从 Azure 媒体服务 V2 迁移到 V3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 5ca01bcea348b866c0f40f82ebe90dc31d032739
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927065"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>媒体保留单位 (Mru) 基于方案的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-4.svg)

本文提供了基于 MRU 方案的指南，可帮助你从 Azure 媒体服务 V2 迁移到 V3。

- 对于在 Azure 门户中创建的新 V3 帐户，或与版本为2020-05-01 的 V3 API，你不再需要设置媒体保留单位 (Mru) 。 系统现在将根据负载自动扩展和缩减。
- 如果你有在2020-05-01 版 API 之前创建的 V3 或 V2 帐户，仍然可以使用媒体保留单位来控制作业的并发性和性能。 有关详细信息，请参阅缩放媒体处理。 可以使用适用于 Media Services V3 的 CLI 2.0 或 Azure 门户来管理 Mru。  
- 如果在 Azure 门户中看不到用于管理 Mru 的选项，则运行的是使用 2020-05-01 API 或更高版本创建的帐户。
- 如果你熟悉如何将 MRU 类型设置为 S3，性能将会提高或保持不变。
- 如果你是现有的 V2 客户，则需要在完成迁移之前创建新的 V2 帐户以支持现有应用程序。 
- 尚未完全弃用的索引器 V1 或其他媒体处理器可能需要重新启用。 

有关 Mru 的详细信息，请参阅 [媒体保留单位](concept-media-reserved-units.md) 和 [如何缩放媒体保留单位](media-reserved-units-cli-how-to.md)。

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU 概念、教程和操作方法指南

### <a name="concepts"></a>概念

[媒体保留单位](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>操作方法指南

[如何缩放媒体保留单位](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>示例

你还可以 [在代码示例中比较 V2 和 V3 代码](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
