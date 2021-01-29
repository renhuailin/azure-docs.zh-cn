---
title: 用于 Redis 的 Azure 缓存作为事件网格源
description: 介绍通过 Azure 事件网格为 Azure Cache for Redis 事件提供的属性
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055701"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>用于 Redis 的 Azure 缓存作为事件网格源

本文提供适用于 Azure Cache for Redis 事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>适用于 Redis REST Api 的 Azure 缓存的事件列表

当客户端通过调用 Redis REST Api 的 Azure 缓存来导出、导入或缩放时，将触发这些事件。 修补事件由 Redis 更新触发。

 |事件名称 |说明|
 |----------|-----------|
 |**ExportRDBCompleted** |导出缓存数据时触发。 |
 |**ImportRDBCompleted** |导入缓存数据时触发。 |
 |**PatchingCompleted** |修补完成时触发。 |
 |**ScalingCompleted** |缩放完成后触发。 |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>事件响应的内容

触发某个事件后，事件网格服务会将有关该事件的数据发送到订阅终结点。

本部分包含有关 Redis 事件的每个 Azure 缓存的数据外观的示例。

### <a name="microsoftcachepatchingcompleted-event"></a>PatchingCompleted 事件

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>ImportRDBCompleted 事件

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>ExportRDBCompleted 事件

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | string | 事件主题的发布者定义路径。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| EventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| id | string | 事件的唯一标识符。 |
| data | 对象 (object) | Azure Cache for Redis 事件数据。 |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| timestamp | string | 发生事件的时间。 |
| name | string | 事件的名称。 |
| status | string | 事件的状态。 失败或成功。 |


## <a name="quickstarts"></a>快速入门

若要试用 Azure Cache for Redis 事件，请参阅以下任一快速入门文章：

|若要使用此工具：    |请参阅此文： |
|--|-|
|Azure 门户    |[快速入门：将 Azure Cache for Redis 事件路由到具有 Azure 门户的 web 终结点](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[快速入门：通过 PowerShell 将 Azure Cache for Redis 事件路由到 web 终结点](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[快速入门：将 Azure Cache for Redis 事件路由到 Azure CLI 的 web 终结点](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。

