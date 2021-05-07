---
title: 媒体保留单位 (MRU) 迁移指南
description: 本文提供基于 MRU 场景的指南，可帮助你从 Azure 媒体服务 V2 迁移到 V3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 5ca01bcea348b866c0f40f82ebe90dc31d032739
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927065"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>基于媒体保留单位 (MRU) 场景的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-4.svg)

本文提供基于 MRU 场景的指南，可帮助你从 Azure 媒体服务 V2 迁移到 V3。

- 对于在 Azure 门户中创建的新 V3 帐户或使用 2020-05-01 版本的 V3 API 创建的 V3 帐户，不再需要设置媒体保留单位 (MRU)。 系统现在将根据负载自动纵向扩展和缩减。
- 如果你有 2020-05-01 版本的 API 之前创建的 V3 或 V2 帐户，则仍然可以使用媒体保留单位控制作业的并发和性能。 有关详细信息，请参阅缩放媒体处理。 可以使用适用于媒体服务 V3 的 CLI 2.0 或使用 Azure 门户来管理 MRU。  
- 如果在 Azure 门户中看不到管理 MRU 的选项，则说明你正在运行使用 2020-05-01 API 或更高版本创建的帐户。
- 如果熟悉如何将 MRU 类型设置为 S3，则性能将会提高或保持不变。
- 如果你是现有的 V2 客户，则需要在迁移完成之前创建一个新的 V2 帐户以支持现有的应用程序。 
- 索引器 V1 或尚未完全弃用的其他媒体处理器可能需要再次启用。 

有关 MRU 的详细信息，请参阅[媒体保留单位](concept-media-reserved-units.md)和[如何缩放媒体保留单位](media-reserved-units-cli-how-to.md)。

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU 概念、教程和操作指南

### <a name="concepts"></a>概念

[媒体保留单位](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>操作方法指南

[如何缩放媒体保留单位](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>示例

还可[将代码示例中的 V2 和 V3 代码进行比较](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
