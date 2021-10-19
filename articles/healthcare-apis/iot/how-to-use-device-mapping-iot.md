---
title: IoT 连接器中的设备映射模板 - Azure Healthcare APIs
description: 本文介绍如何使用本文中的设备映射IoT Connector。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: a0cc7037ca95fe4262b6c10dc9cb260a971f7c31
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005532"
---
# <a name="how-to-use-device-mapping"></a>如何使用设备映射

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

IoT 连接器需要两种类型的基于 JSON 的映射。 第一种是“设备映射”，它负责映射发送到 `devicedata` Azure 事件中心终结点的设备有效负载。 它提取类型、设备标识符、度量日期时间和度量值。 

第二种类型 **（快速医疗保健互操作性资源 (FHIR&#174;) 映射）** 控制 FHIR 资源的映射。 它允许配置观察期时长、用来存储值的 FHIR 数据类型，以及术语代码。 

这两种类型的映射根据其类型组成 JSON 文档。 然后，这些 JSON 文档会通过 Azure 门户 添加到 IoT 连接器。 设备映射文档通过"设备映射" **页和** FHIR 目标映射文档通过"目标" **页** 添加。

> [!NOTE]
> 映射存储在基础 Blob 存储中，并按计算执行从 Blob 加载。 更新后，它们应立即生效。 

## <a name="device-mapping"></a>设备映射

设备映射提供了映射功能，可将设备内容提取为常见格式以进一步进行评估。 将针对所有模板评估收到的每条消息。 此方法允许将单条入站消息投影到多条出站消息，这些出站消息稍后会映射到 FHIR 中的不同观察结果。 结果是一个规范化数据对象，表示由模板分析的一个或多个值。 规范化数据模型有一些必须查找和提取的必需属性：

| 属性             | 描述                                                                                                                                                                                                                                                   |
|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**             | 用来对度量进行分类的名称/类型。 此值用于绑定到所需的 FHIR 目标映射。  多个映射可以输出到同一类型，因此可以将多个设备的不同表示形式映射到单个公共输出。 |
| OccurenceTimeUtc | 度量的发生时间。                                                                                                                                                                                                                            |
| **DeviceId**         | 设备的标识符。 此值应匹配目标 FHIR 服务上存在的设备资源的标识符。                                                                                                                       |
| **属性**       | 提取至少一个属性，以便可以将值保存在创建的观察资源中。  属性是在规范化期间提取的键值对的集合。                                                                                  |

下面是规范化过程中所发生情况的概念性示例。

![规范化示例](media/concepts-iot-mapping-templates/normalization-example.png#lightbox)

内容有效负载本身是一条 Azure 事件中心消息，由以下三部分组成：Body、Properties 和 SystemProperties。 `Body` 是一个字节数组，表示一个 UTF-8 编码字符串。 在模板评估期间，字节数组自动转换为字符串值。 `Properties` 是供消息创建者使用的键值集合。 `SystemProperties` 也是 Azure 事件中心框架保留的键值集合，其中的条目由其自动填充。

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
### <a name="mapping-with-json-path"></a>通过 JSON 路径进行映射

目前支持的三种设备内容映射类型都依赖于 JSON 路径来匹配所需的映射和提取的值。 可在[此处](https://goessner.net/articles/JsonPath/)找到有关 JSON 路径的详细信息。 所有三种模板类型都使用 [JSON .NET 实现](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm)来解析 JSON 路径表达式。

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate 允许使用 JSON 路径来匹配和提取事件中心消息中的值。

| 属性 | 说明 |示例 |
| --- | --- | --- |
|TypeName|要与模板所匹配的度量关联的类型。|`heartrate`
|TypeMatchExpression|针对事件中心有效负载进行评估的 JSON 路径表达式。 如果找到匹配的 JToken，则会将模板视为匹配项。 将会针对此处匹配的已提取 JToken 对所有后续表达式进行评估。|`$..[?(@heartRate)]`
|TimestampExpression|JSON 路径表达式，用于提取度量的 OccurenceTimeUtc 的时间戳值。|`$.endDate`
|DeviceIdExpression|用于提取设备标识符的 JSON 路径表达式。|`$.deviceId`
|PatientIdExpression|可选：用于提取患者标识符的 JSON 路径表达式。|`$.patientId`
|EncounterIdExpression|可选：用于提取就医标识符的 JSON 路径表达式。|`$.encounterId`
|Values[].ValueName|要与后续表达式提取的值关联的名称。 用于绑定 FHIR 目标映射中所需的值/组件。 |`hr`
|Values[].ValueExpression|用于提取必需值的 JSON 路径表达式。|`$.heartRate`
|Values[].Required|将要求该值存在于有效负载中。  如果未找到，则不会生成度量，会引发 InvalidOperationException。|`true`

##### <a name="examples"></a>示例

心率

*消息*

```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*模板*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
血压

*消息*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*模板*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
投影来自单条消息的多个度量

*消息*

```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
模板 1

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

模板 2

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
投影来自消息中的数组的多个度量

*消息*

```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2021-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2021-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2021-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*模板*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate 类似于 JsonPathContentTemplate，但 DeviceIdExpression 和 TimestampExpression 不是必需的。

使用此模板时，将假设要评估的消息已使用 [Azure IoT 中心设备 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 或使用 [Azure IoT Central](../../iot-central/core/overview-iot-central.md) 的[导出数据（旧式）](../../iot-central/core/howto-export-data-legacy.md)功能发送。 使用这些 SDK 时，设备标识 (假定来自 Azure Iot Hub/Central 的设备标识符注册为目标 FHIR 服务) 上的设备资源的标识符，并且消息的时间戳是已知的。 如果你使用 Azure IoT 中心设备 SDK，但在消息正文中为设备标识或度量时间戳使用自定义属性，则仍可使用 JsonPathContentTemplate。

> [!NOTE]
> 使用 `IotJsonPathContentTemplate` 时，`TypeMatchExpression` 应当作为单个 JToken 解析为整条消息。 有关更多详细信息，请参阅以下示例。

##### <a name="examples"></a>示例

心率

*消息*

```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*模板*

```json

    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

血压

*消息*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*模板*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate 也不需要 DeviceIdExpression 和 TimestampExpression。 如果要评估的消息是通过 [Azure IoT Central](../../iot-central/core/overview-iot-central.md) 的[导出数据](../../iot-central/core/howto-export-data.md)功能发送的，则将使用此模板。 使用此功能时，设备标识（假设 Azure IoT Central 的设备标识符注册为目标 FHIR 服务器上的设备资源的标识符）和消息的时间戳是已知的。 如果使用 Azure IoT Central 的数据导出功能，但在消息正文中为设备标识或度量时间戳使用自定义属性，则仍可使用 JsonPathContentTemplate。

> [!NOTE]
> 使用 IotCentralJsonPathContentTemplate 时，TypeMatchExpression 会作为单个 JToken 解析为整条消息。 有关更多详细信息，请参阅以下示例。
 
##### <a name="examples"></a>示例

心率

*消息*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*模板*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

血压

*消息*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*模板*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[如何使用 FHIR 目标映射](how-to-use-fhir-mapping-iot.md)

 (FHIR&#174;) 是 [HL7](https://hl7.org/fhir/) 的商标，并经 HL7 授权使用。