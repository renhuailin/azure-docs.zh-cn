---
title: 媒体保留单位 - Azure
description: 媒体保留单位可用于缩放媒体处理能力并决定媒体处理任务的速度。
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 3fc68505712bc5cae0defd216b30a3c5913c3c77
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866434"
---
# <a name="media-reserved-units"></a>媒体预留单位

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

媒体保留单位 (MRU) 之前在 Azure 媒体服务 v2 中用于控制编码的并发和性能。 你不再需要管理 MRU 或请求增加任何媒体服务帐户的配额，因为系统将根据负载自动纵向扩展和缩减。 你还将看到性能与使用 MRU 的情况相当或较其有所提升。 

如果你有一个使用 2020-05-01 版 API 之前的版本创建的帐户，则你仍有权访问 API 来管理 MRU，但所设置的 MRU 配置均不用于控制编码并发性或性能。 如果在 Azure 门户中看不到管理 MRU 的选项，则说明你具有一个使用 2020-05-01 API 或更高版本创建的帐户。 

## <a name="billing"></a>计费

虽然之前对媒体保留单位收费，但自 2021 年 4 月 17 日起，对配置媒体保留单位的帐户不再收取任何费用。 有关编码作业计费的详细信息，请参阅[使用媒体服务编码视频和音频](encoding-concept.md)

对于使用 2020-05-01 版（即 v3 版本）API 创建的或通过 Azure 门户创建的帐户，不再需要缩放和媒体保留单位。 缩放现在由服务在内部自动处理。 任何 Azure 媒体服务帐户都不再需要或支持媒体保留单位。 请参阅[媒体保留单位（旧版）](concept-media-reserved-units.md)以了解其他信息。

## <a name="see-also"></a>另请参阅

* [从媒体服务 v2 迁移到 v3](migrate-v-2-v-3-migration-introduction.md)
* [使用 CLI 缩放媒体预留单位](media-reserved-units-cli-how-to.md)
