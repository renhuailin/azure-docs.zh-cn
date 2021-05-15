---
title: 事件架构 - Azure 事件网格 IoT Edge | Microsoft Docs
description: IoT Edge 上事件网格中的事件架构。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: ea36c40f2038d016afb0c45944a98d4d90df6240
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171561"
---
# <a name="event-schemas"></a>事件架构

事件网格模块接受并传递 JSON 格式的事件。 事件网格目前支持三种架构：

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

可以配置发布者在创建主题过程中必须遵循的架构。 如果未指定，则默认为 EventGridSchema。 不符合预期架构的事件将被拒绝。

订阅者也可以配置其希望事件传递的架构。 如果未指定，则默认为主题的架构。
当前，订阅者的传递架构必须与其主题的输入架构匹配。 

## <a name="eventgrid-schema"></a>EventGrid 架构

EventGrid 架构包含发布实体必须遵循的一组必需属性。 每个发布者都必须填充顶级字段。

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid 架构属性

所有事件均包含以下顶级数据：

| 属性 | 类型 | 必需 | 说明 |
| -------- | ---- | ----------- |-----------
| 主题 | string | 否 | 应与其发布的主题匹配。 如果未指定，事件网格将用其发布的主题名称对其填充。 |
| subject | 字符串 | 是 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 是 | 此事件源的事件类型，例如 BlobCreated。 |
| EventTime | 字符串 | 是 | 基于提供程序 UTC 时间的事件生成时间。 |
| ID | string | 否 | 事件的唯一标识符。 |
| data | object | 否 | 用于捕获特定于发布实体的事件数据。 |
| dataVersion | 字符串 | 是 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 否 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

### <a name="example--eventgrid-schema-event"></a>示例 - EventGrid 架构事件

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent 架构

在自定义架构中，没有像 EventGrid 架构一样要求强制执行的必需属性。 发布实体可以完全控制事件架构。 它提供极大的灵活性，并可实现这样的方案：已经拥有基于事件的系统，并且想要重复使用现有事件和/或不希望关联至某一特定的架构。

### <a name="custom-schema-properties"></a>自定义架构属性

无强制属性。 由发布实体来确定有效负载。

### <a name="example--custom-schema-event"></a>示例 - 自定义架构事件

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent 架构

除上述架构之外，事件网格本身还支持 [CloudEvents JSON 架构](https://github.com/cloudevents/spec/blob/master/json-format.md)的事件。 CloudEvents 是一种用于描述事件数据的开放规范。 它提供通用的事件架构以供发布和使用事件，可简化互操作性。 它是 [CNCF](https://www.cncf.io/) 的一部分，当前可用的版本为 1.0-rc1。

### <a name="cloudevent-schema-properties"></a>CloudEvent 架构属性

有关必需的包络线属性，请参阅 [CloudEvents 规范](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)。

### <a name="example--cloud-event"></a>示例 - 云事件
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
