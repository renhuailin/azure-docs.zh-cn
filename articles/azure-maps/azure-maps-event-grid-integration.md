---
title: 使用事件网格对 Azure Maps 事件做出响应
description: 了解如何应对涉及地理围栏的 Azure Maps 事件。 了解如何侦听映射事件以及如何使用事件网格将事件重新路由到事件处理程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: ff5b14705f05debf97fb0fe26f88993b001a52c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727984"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>使用事件网格对 Azure Maps 事件做出响应

Azure Maps 与 Azure 事件网格集成，这样，用户就可以向其他服务发送事件通知，并触发下游过程。 本文旨在帮助你配置业务应用程序来侦听 Azure Maps 事件。 这样，用户就可以采取可靠的、可缩放的和安全的方式来响应重要事件。 例如，用户可以生成应用程序在每当有设备进入地理围栏时更新数据库、创建票证，并发送电子邮件通知。

Azure 事件网格是一种完全托管的事件路由服务，它使用发布-订阅模型。 事件网格提供对 Azure 服务（如 [Azure Functions](../azure-functions/functions-overview.md) 和 [Azure 逻辑应用](../azure-functions/functions-overview.md)）的内置支持。 它可以使用 Webhook 将事件警报传递到非 Azure 服务。 有关受事件网格支持的事件处理程序的完整列表，请参阅 [Azure 事件网格简介](../event-grid/overview.md)。


![Azure 事件网格功能模型](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps 事件类型

事件网格使用[事件订阅](../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 Azure Maps 帐户发出以下事件类型： 

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 在接收到的坐标已从给定地理围栏的外部移动到内部时引发 |
| Microsoft.Maps.GeofenceExited | 在接收到的坐标已从给定地理围栏的内部移动到外部时引发 |
| Microsoft.Maps.GeofenceResult | 当地理围栏查询返回结果时引发，不管状态如何。 |

## <a name="event-schema"></a>事件架构

以下示例显示 GeofenceResult 的架构：

```JSON
{
    "id":"451675de-a67d-4929-876c-5c2bf0b2c000",
    "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}",
    "subject":"/spatial/geofence/udid/{udid}/id/{eventId}",
    "data":{
        "geometries":[
            {
                "deviceId":"device_1",
                "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169",
                "geometryId":"1",
                "distance":999.0,
                "nearestLat":47.609833,
                "nearestLon":-122.148274
            }
        ],
        "expiredGeofenceGeometryId":[
        ],
        "invalidPeriodGeofenceGeometryId":[
        ]
    },
    "eventType":"Microsoft.Maps.GeofenceResult",
    "eventTime":"2018-11-08T00:52:08.0954283Z",
    "metadataVersion":"1",
    "dataVersion":"1.0"
}

```

## <a name="tips-for-consuming-events"></a>使用事件的提示

处理 Azure Maps 地理围栏事件的应用程序应遵循以下建议的做法：

* 将多个订阅配置为将事件路由至同一事件处理程序。 不可假定事件均来自某个特定的源，这很重要。 始终检查消息主题，以确保该消息来自预期的源。
* 在响应标头中使用 `X-Correlation-id` 字段来了解对象的信息是否是最新的。 消息可能不按顺序到达，或者延迟达到。
* 在调用地理围栏 API 中的 GET 或 POST 请求（模式参数设置为 `EnterAndExit`）时，会为地理围栏中其状态不同于以前的地理围栏 API 调用的每个几何图形生成“进入”或“退出”事件。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用地理围栏功能来控制在构造站点进行的操作，请参阅：

> [!div class="nextstepaction"] 
> [使用 Azure Maps 设置地域隔离区](tutorial-geofence.md)