---
author: baanders
description: Azure 数字孪生路由筛选器选项的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: 811e59e8fd2fea1d8ee90014d4a5ea11229eec63
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257292"
---
| 筛选器名称 | 说明 | 筛选器文本架构 | 支持的值 | 
| --- | --- | --- | --- |
| True/False | 允许创建无筛选的路由，或禁用路由，这样就不会发送事件 | `<true/false>` | `true` = 启用无筛选的路由 <br> `false` = 禁用路由 |
| 类型 | 流经数字孪生体实例的[事件类型](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | 以下是可能的事件类型值： <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 源 | Azure 数字孪生实例的名称 | `source = '<hostname>'`| 以下是可能的主机名值： <br> **用于通知**：`<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **用于遥测**：`<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Subject | 上述事件源上下文中事件的说明 | `subject = '<subject>'` | 以下是可能的使用者值： <br>**对于通知**：使用者为 `<twinid>` <br> 或使用者的 URI 格式（由多个部件或 ID 唯一标识）：<br>`<twinid>/relationships/<relationshipid>`<br> **对于遥测**：使用者是组件路径（如果遥测是从一个孪生体组件发出的），例如 `comp1.comp2`。 如果遥测不是从组件发出的，则其使用者字段为空。 |
| 数据架构 | DTDL 模型 ID | `dataschema = '<model-dtmi-ID>'` | **对于遥测**：数据架构是孪生体或发出遥测的组件的模型 ID。 例如： `dtmi:example:com:floor4;2` <br>**对于通知（创建/删除）** ：可以通过 `$body.$metadata.$model` 访问通知正文中的数据架构。 <br>**对于通知（更新）** ：可以通过 `$body.modelId` 访问通知正文中的数据架构|
| 内容类型 | 数据值的内容类型 | `datacontenttype = '<contentType>'` | 内容类型为 `application/json` |
| 规范版本 | 所使用的事件架构的版本 | `specversion = '<version>'` | 版本必须为 `1.0`。 这指示 CloudEvents 架构版本 1.0 |
| 通知正文 | 引用通知的 `data` 字段中的任何属性 | `$body.<property>` | 有关通知示例，请参阅 [*概念：事件通知*](../articles/digital-twins/concepts-event-notifications.md)。 `data` 字段中的任何属性都可以使用 `$body` 进行引用

以下数据类型可以用作对上述数据进行引用后返回的值：

| 数据类型 | 示例 |
|-|-|-|
|**字符串**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**整数**|`$body.errorCode > 200`|
|**双精度**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

定义路由筛选器时支持以下运算符：

|系列|运算符|示例|
|-|-|-|
|逻辑|AND、OR、( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|比较|<、<=、>、>=、=、!=|`$body.temperature <= 5.5`

定义路由筛选器时支持以下函数：

|函数|说明|示例|
|--|--|--|
|STARTS_WITH(x,y)|如果值 `x` 以字符串 `y` 开头，则返回 true。|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | 如果值 `x` 以字符串 `y` 结尾，则返回 true。|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 如果值 `x` 包含字符串 `y`，则返回 true。|`CONTAINS(subject, '<twinID>')`|

实现或更新筛选器时，更改可能需要几分钟时间才能在数据管道中反映出来。