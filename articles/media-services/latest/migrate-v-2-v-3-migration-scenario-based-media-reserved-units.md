---
title: 媒体保留单位 (MRU) 迁移指南
description: 本文提供基于 MRU 场景的指南，可帮助你从 Azure 媒体服务 V2 迁移到 V3。
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 01d62055de8e8327b518bc3638cab85426917247
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864639"
---
# <a name="media-reserved-units-migration-guidance"></a>媒体保留单位迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-4.svg)

本文提供基于 MRU 场景的指南，可帮助你从 Azure 媒体服务 V2 迁移到 V3。

> [!Important]
> 任何媒体服务帐户都不再需要媒体保留单位，因为系统会根据负载自动纵向扩展和缩减。 

## <a name="scenario-guidance"></a>方案指南

请根据以下方案迁移 MRU：

* 对于所有媒体服务帐户，你不再需要设置媒体保留单位 (MRU)。 系统现在将根据负载自动纵向扩展和缩减。
* 如果你有在 2020-05-01 版 API 之前创建的帐户，则你仍有权访问 API 来管理 MRU，但系统不会将你所设置的任何 MRU 配置用于控制编码并发性或性能。 有关详细信息，请参阅[缩放媒体处理](../previous/media-services-scale-media-processing-overview.md)。 可以使用适用于媒体服务 V3 的 CLI 2.0 或使用 Azure 门户来管理 MRU。
* 如果在 Azure 门户中看不到管理 MRU 的选项，则说明你正在运行使用 2020-05-01 API 或更高版本创建的帐户。
* 如果你熟悉如何将 MRU 类型设置为 S3，则性能会随着 MRU 的移除而提高或保持不变。
* 如果你是现有的 V2 客户，则需要在迁移完成之前创建一个新的 V3 帐户以支持现有的应用程序。 
* 索引器 V1 或尚未完全弃用的其他媒体处理器可能需要再次启用。 

有关 MRU 的详细信息，请参阅[媒体保留单位](concept-media-reserved-units.md)和[如何缩放媒体保留单位](media-reserved-units-cli-how-to.md)。

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU 概念、教程和操作指南

### <a name="concepts"></a>概念

[媒体保留单位](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>操作方法指南

[如何缩放媒体保留单位](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>示例

还可[将代码示例中的 V2 和 V3 代码进行比较](migrate-v-2-v-3-migration-samples.md)。
