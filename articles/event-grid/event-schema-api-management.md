---
title: 将 API 管理用作事件网格源
description: 本文介绍了如何将 Azure API 管理用作事件网格事件源。 其中提供了架构和操作指南文章的链接。
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 07/12/2021
ms.openlocfilehash: e2f56f8886a387158c148edaf9ae557deac3783f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659146"
---
# <a name="azure-api-management-as-an-event-grid-source-preview"></a>将 Azure API 管理用作事件网格源（预览版）

本文提供了 [Azure API 管理](../api-management/index.yml)事件的属性和架构。  有关事件架构的简介，请参阅 [Azure 事件网格事件架构](./event-schema.md)。 它还提供了使用 API 管理作为事件源的文章链接。

## <a name="available-event-types"></a>可用事件类型

API Management 发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.ApiManagement.UserCreated | 创建用户时引发。 |
| Microsoft.ApiManagement.UserUpdated | 更新用户时引发。 |
| Microsoft.ApiManagement.UserDeleted | 删除用户时引发。 |
| Microsoft.ApiManagement.APICreated | 创建 API 时引发。 |
| Microsoft.ApiManagement.APIUpdated | 更新 API 时引发。 |
| Microsoft.ApiManagement.APIDeleted | 删除 API 时引发。 |
| Microsoft.ApiManagement.ProductCreated | 创建产品时引发。 |
| Microsoft.ApiManagement.ProductUpdated | 更新产品时引发。 |
| Microsoft.ApiManagement.ProductDeleted | 删除产品时引发。 |
| Microsoft.ApiManagement.ReleaseCreated | 创建 API 发布时引发。 |
| Microsoft.ApiManagement.ReleaseUpdated | 更新 API 发布时引发。 |
| Microsoft.ApiManagement.ReleaseDeleted | 删除 API 发布时引发。 |
| Microsoft.ApiManagement.SubscriptionCreated | 创建订阅时引发。 |
| Microsoft.ApiManagement.SubscriptionUpdated | 更新订阅时引发。 |
| Microsoft.ApiManagement.SubscriptionDeleted | 删除订阅时引发。 |

## <a name="example-event"></a>示例事件

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
以下示例显示了产品创建事件的架构。 其他 API 管理资源创建事件的架构类似。

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "eventType": "Microsoft.ApiManagement.ProductCreated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示了产品创建事件的架构。 其他 API 管理资源创建事件的架构类似。 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "Type": "Microsoft.ApiManagement.ProductCreated",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
以下示例显示了用户删除事件的架构。 其他 API 管理资源删除事件的架构类似。

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "eventType": "Microsoft.ApiManagement.UserDeleted",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示了用户删除事件的架构。 其他 API 管理资源删除事件的架构类似。 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "Type": "Microsoft.ApiManagement.UserDeleted",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示了 API 更新事件的架构。 其他 API 管理资源更新事件的架构类似。 
```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "resourceUri": "/subscriptions/subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "eventType": "Microsoft.ApiManagement.APIUpdated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-12T23:13:44.9048323Z"
}]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示了 API 更新事件的架构。 其他 API 管理资源更新事件的架构类似。 

```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "Type": "Microsoft.ApiManagement.APIUpdated",
  "Time": "2021-07-12T23:13:44.9048323Z",
  "specversion":1.0
}]
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
| `data` | object | API 管理事件数据。 |
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
| `data` | object | API 管理事件数据。 |
| `specversion` | 字符串 | CloudEvents 架构规范版本。 |

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `resourceUri` | string | 符合性状态更改所针对的资源的完全限定 ID，包括资源名称和资源类型。 使用 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/Microsoft.ApiManagement/service/<ServiceName>/<ResourceType>/<ResourceName>` 格式 |

## <a name="tutorials-and-how-tos"></a>教程和操作指南

|标题  |说明  |
|---------|---------|
| [将事件从 API 管理发送到事件网格](../api-management/how-to-event-grid.md)| 如何使用事件网格订阅 API 管理事件。 |

## <a name="next-steps"></a>后续步骤

- 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](./overview.md)
- 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](./subscription-creation-schema.md)。


