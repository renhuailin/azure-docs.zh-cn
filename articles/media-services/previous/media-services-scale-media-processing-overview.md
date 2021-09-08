---
title: 媒体保留单位概述 | Microsoft Docs
description: 本文概述了如何使用 Azure 媒体服务缩放媒体处理能力。
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 19454d90b55ab92c92d2e9132198f918f38c6e2c
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835380"
---
# <a name="media-reserved-units"></a>媒体保留单位

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

媒体保留单位 (MRU) 之前用于控制编码的并发和性能。 MRU 仅用于以下即将被弃用的旧版媒体处理器。 请参阅 [Azure 媒体服务旧版组件](legacy-components.md)，了解有关这些旧版处理器的停用信息：

* 媒体编码器高级工作流
* Media Indexer V1 和 V2

对于所有其他媒体处理器，你不再需要管理 MRU，也不再需要请求增加任何媒体服务帐户的配额，因为系统将根据负载自动纵向扩展和缩减。 你还将看到性能与使用 MRU 的情况相当或较其有所提升。

## <a name="billing"></a>计费

虽然之前对媒体保留单位收费，但自 2021 年 4 月 17 日起，对配置媒体保留单位的帐户不再收取任何费用。

## <a name="scaling-mrus"></a>缩放 MRU

出于兼容性目的，可继续使用 Azure 门户或以下 API 来管理和缩放 MRU：

[.NET](media-services-dotnet-encoding-units.md)
[门户](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

但默认情况下，不会使用你设置的任何 MRU 配置来控制编码的并发或性能。 此配置只有一种例外情况，那就是使用旧版媒体处理器 Media Encoder Premium Workflow 或 Media Indexer V1 进行编码时。  
