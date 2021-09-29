---
title: 充当事件网格源的 Azure Maps
description: 介绍针对 Azure 事件网格中的 Azure Maps 事件提供的属性和架构
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 2715cd4ea67ebac0767332a90770ffae9a5d6069
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626943"
---
# <a name="azure-maps-as-an-event-grid-source"></a>充当事件网格源的 Azure Maps

本文提供 Azure Maps 事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](./event-schema.md)。 它还提供了一个快速入门和教程的列表，这些快速入门和教程介绍如何使用 Azure Maps 作为事件源。

## <a name="available-event-types"></a>可用事件类型

Azure Maps 帐户发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 当接收的坐标从给定地理围栏的外部进入内部时引发 |
| Microsoft.Maps.GeofenceExited | 当接收的坐标从给定地理围栏的内部移到外部时引发 |
| Microsoft.Maps.GeofenceResult | 当地理围栏查询返回结果时引发，不管状态如何。 |

## <a name="example-events"></a>示例事件

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
以下示例显示了 **GeofenceEntered** 事件的架构

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

以下示例显示 **GeofenceResult** 的架构 

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
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
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

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)
以下示例显示了 **GeofenceEntered** 事件的架构

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceEntered", 
   "time":"2018-11-08T00:54:17.6408601Z", 
   "specversion":"1.0" 
}
```

以下示例显示 **GeofenceResult** 的架构 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
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
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceResult", 
   "time":"2018-11-08T00:52:08.0954283Z", 
   "specversion":"1.0" 
}
```
---

## <a name="event-properties"></a>事件属性

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `topic` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `eventType` | string | 此事件源的一个注册事件类型。 |
| `eventTime` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | object | 地理围栏事件数据。 |
| `dataVersion` | string | 数据对象的架构版本。 发布者定义架构版本。 |
| `metadataVersion` | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)
事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `source` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `type` | string | 此事件源的一个注册事件类型。 |
| `time` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | object | 地理围栏事件数据。 |
| `specversion` | 字符串 | CloudEvents 架构规范版本。 |

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `apiCategory` | 字符串 | 事件的 API 类别。 |
| `apiName` | 字符串 | 事件的 API 名称。 |
| `issues` | object | 列出处理过程中遇到的问题。 如果返回了任何问题，则不会随响应一起返回几何图形。 |
| `responseCode` | 数字 | HTTP 响应代码 |
| `geometries` | object | 列出围栏几何图形，这些几何图形包含坐标位置，或者覆盖该位置周围的 searchBuffer。 |

当 Maps API 中发生错误时，将返回 error 对象。 error 对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `error` | ErrorDetails |当 Maps API 中发生错误时，将返回此对象  |

当 Maps API 中发生错误时，将返回 ErrorDetails 对象。 ErrorDetails 对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `code` | 字符串 | HTTP 状态代码。 |
| `message` | 字符串 | 在适用的情况下，将提供该错误的用户可读说明。 |
| `innererror` | InnerError | 在适用的情况下，将提供一个包含有关该错误的特定于服务的信息的对象。 |

InnerError 是包含有关该错误的特定于服务的信息的对象。 InnerError 对象具有以下属性： 

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `code` | 字符串 | 错误消息。 |

几何图形对象，其中列出了相对于请求中用户时间已过期的地理围栏的几何图形 ID。 geometries 对象包含具有以下属性的几何图形项： 

| 属性 | 类型 | 说明 |
|:-------- |:---- |:----------- |
| `deviceid` | 字符串 | 设备的 ID。 |
| `distance` | 字符串 | <p>从坐标到最近的地理围栏边界的距离。 正值表示坐标在地理围栏的外部。 如果坐标在地理围栏外部，但大于 searchBuffer 与最近地理围栏边界之间的距离值，则该值为 999。 负值表示坐标在地理围栏的内部。 如果坐标在多边形的内部，但大于 searchBuffer 与最近地理围栏边界之间的距离值，则该值为 -999。 值 999 表示坐标位于地理围栏外部的置信度很高。 值 -999 表示坐标位于地理围栏内部的置信度很高。<p> |
| `geometryid` |字符串 | 用于标识地理围栏几何图形的唯一 ID。 |
| `nearestlat` | 数字 | 最近几何图形点的纬度。 |
| `nearestlon` | 数字 | 最近几何图形点的经度。 |
| `udId` | 字符串 | 上传地理围栏时由用户上传服务返回的唯一 ID。 不会包含在地理围栏 POST API 中。 |

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `expiredGeofenceGeometryId` | string[] | 相对于请求中用户时间已过期的地理围栏的几何图形 ID 列表。 |
| `geometries` | geometries[] |列出围栏几何图形，这些几何图形包含坐标位置，或者覆盖该位置周围的 searchBuffer。 |
| `invalidPeriodGeofenceGeometryId` | string[]  | 相对于请求中用户时间处于失效期的地理围栏的几何图形 ID 列表。 |
| `isEventPublished` | boolean | 如果至少已将一个事件发布到 Azure Maps 事件订阅服务器，则为 true；如果未将任何事件发布到 Azure Maps 事件订阅服务器，则为 false。 |

## <a name="tutorials-and-how-tos"></a>教程和操作指南
|标题  |说明  |
|---------|---------|
| [使用事件网格对 Azure Maps 事件做出响应](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 概述了 Azure Maps 与事件网格的集成。 |
| [教程：设置地理围栏](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 本教程将引导你完成使用 Azure Maps 设置地理围栏的基本步骤。 你将使用 Azure 事件网格来流式传输地理围栏结果，并根据地理围栏结果设置通知。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
