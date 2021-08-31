---
title: 概念：Azure API for FHIR 的适用于 FHIR 的 Azure IoT 连接器（预览版）功能中的映射模板
description: 了解如何在适用于 FHIR 的 Azure IoT 连接器（预览版）中创建两种类型的映射模板。 设备映射模板将设备数据转换为规范化架构。 FHIR 映射模板将规范化消息转换为基于 FHIR 的观察资源。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: rabhaiya
ms.openlocfilehash: 690a406c9cb0dd7644656c78236a2001ae0f0f2e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778494"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）映射模版
本文详细介绍了如何使用映射模板配置适用于快速医疗保健互操作性资源 (FHIR&#174;)* 的 Azure IoT 连接器。

适用于 FHIR 的 Azure IoT 连接器需要两种基于 JSON 的映射模板。 第一种是“设备映射”，它负责映射发送到 `devicedata` Azure 事件中心终结点的设备有效负载。 它提取类型、设备标识符、度量日期时间和度量值。 第二种是“FHIR 映射”，它控制 FHIR 资源的映射。 它允许配置观察期时长、用来存储值的 FHIR 数据类型，以及术语代码。 

这些映射模板根据其类型组合到 JSON 文档中。 然后，这些 JSON 文档通过 Azure 门户添加到适用于 FHIR 的 Azure IoT 连接器中。 设备映射文档是通过“配置设备映射”页面添加的，FHIR 映射文档是通过“配置 FHIR 映射”页面添加的。

> [!NOTE]
> 映射模板存储在基础 blob 存储中，在每次执行计算时从 blob 加载。 更新后，它们应立即生效。 

## <a name="device-mapping"></a>设备映射
设备映射提供了映射功能，可将设备内容提取为常见格式以进一步进行评估。 将针对所有模板评估收到的每条消息。 此方法允许将单条入站消息投影到多条出站消息，这些出站消息稍后会映射到 FHIR 中的不同观察结果。 结果是一个规范化数据对象，表示由模板分析的一个或多个值。 规范化数据模型有一些必须查找和提取的必需属性：

| 属性 | 描述 |
| - | - |
|**Type**|用来对度量进行分类的名称/类型。 此值用于绑定到所需的 FHIR 映射模板。  多个模板可以输出到同一类型，这使你能够将多个设备中的不同表示形式映射到单个常见输出。|
|OccurenceTimeUtc|度量的发生时间。|
|**DeviceId**|设备的标识符。 此值应与目标 FHIR 服务器上存在的设备资源上的标识符相匹配。|
 |**属性**|提取至少一个属性，以便可以将值保存在创建的观察资源中。  属性是在规范化期间提取的键值对的集合。|

下面是规范化过程中所发生情况的概念性示例。

![规范化示例](media/concepts-iot-mapping-templates/normalization-example.png)

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
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>通过 JSON 路径进行映射
目前支持的三种设备内容模板类型依赖于 JSON 路径来匹配所需的模板和提取的值。 可在[此处](https://goessner.net/articles/JsonPath/)找到有关 JSON 路径的详细信息。 所有三种模板类型都使用 [JSON .NET 实现](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm)来解析 JSON 路径表达式。

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate 允许使用 JSON 路径来匹配和提取事件中心消息中的值。

| 属性 | 说明 |<div style="width:150px">示例</div>
| --- | --- | --- 
|TypeName|要与模板所匹配的度量关联的类型。|`heartrate`
|TypeMatchExpression|针对事件中心有效负载进行评估的 JSON 路径表达式。 如果找到匹配的 JToken，则会将模板视为匹配项。 将会针对此处匹配的已提取 JToken 对所有后续表达式进行评估。|`$..[?(@heartRate)]`
|TimestampExpression|JSON 路径表达式，用于提取度量的 OccurenceTimeUtc 的时间戳值。|`$.endDate`
|DeviceIdExpression|用于提取设备标识符的 JSON 路径表达式。|`$.deviceId`
|PatientIdExpression|可选：用于提取患者标识符的 JSON 路径表达式。|`$.patientId`
|EncounterIdExpression|可选：用于提取就医标识符的 JSON 路径表达式。|`$.encounterId`
|Values[].ValueName|要与后续表达式提取的值关联的名称。 用来绑定 FHIR 映射模板中的必需值/组件。 |`hr`
|Values[].ValueExpression|用于提取必需值的 JSON 路径表达式。|`$.heartRate`
|Values[].Required|将要求该值存在于有效负载中。  如果未找到，则不会生成度量，会引发 InvalidOperationException。|`true`

##### <a name="examples"></a>示例
---
心率

*消息*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
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
---
血压

*消息*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
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
---

投影来自单条消息的多个度量

*消息*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
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
---

投影来自消息中的数组的多个度量

*消息*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
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

使用此模板时，将假设要评估的消息已使用 [Azure IoT 中心设备 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 或使用 [Azure IoT Central](../../iot-central/core/overview-iot-central.md) 的[导出数据（旧式）](../../iot-central/core/howto-export-data-legacy.md)功能发送。 使用这些 SDK 时，设备标识（假设 Azure IoT Central/中心的设备标识符注册为目标 FHIR 服务器上的设备资源的标识符）和消息的时间戳是已知的。 如果你使用 Azure IoT 中心设备 SDK，但在消息正文中为设备标识或度量时间戳使用自定义属性，则仍可使用 JsonPathContentTemplate。

注意：使用 IotJsonPathContentTemplate 时，TypeMatchExpression 会作为单个 JToken 解析为整条消息。请参阅下面的示例。 
##### <a name="examples"></a>示例
---
心率

*消息*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*模板*
```json
{
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
---
血压

*消息*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
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

IotCentralJsonPathContentTemplate 也不需要 DeviceIdExpression 和 TimestampExpression，并且当通过 [Azure IoT Central](../../iot-central/core/overview-iot-central.md) 的[导出数据](../../iot-central/core/howto-export-data.md)功能发送要评估的消息时，使用 IotCentralJsonPathContentTemplate。 使用此功能时，设备标识（假设 Azure IoT Central 的设备标识符注册为目标 FHIR 服务器上的设备资源的标识符）和消息的时间戳是已知的。 如果使用 Azure IoT Central 的数据导出功能，但在消息正文中为设备标识或度量时间戳使用自定义属性，则仍可使用 JsonPathContentTemplate。

注意：使用 IotCentralJsonPathContentTemplate 时，TypeMatchExpression 会作为单个 JToken 解析为整条消息。请参阅下面的示例。 
##### <a name="examples"></a>示例
---
心率

*消息*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
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
---
血压

*消息*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
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

## <a name="fhir-mapping"></a>FHIR 映射
将设备内容提取到规范化模型中后，会根据设备标识符、度量类型和时间段收集和分组数据。 将发送此分组的输出，以转换为 FHIR 资源（目前的[观察结果](https://www.hl7.org/fhir/observation.html)）。 本文介绍了 FHIR 映射模板控件如何将数据映射到 FHIR 观察结果。 是创建一个观察点还是超过一个小时的时间段的观察值？ 应在观察结果中添加哪些代码？ 值应该表示为 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 还是 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)？ 这些数据类型是 FHIR 映射配置控件的所有选项。

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate 目前是 FHIR 映射目前唯一支持的模板。  它允许定义代码、有效周期和观察值。 支持多种值类型：[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)、[CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept) 和 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)。 除了这些可配置的值，观察结果资源的标识符以及与适当的设备和患者资源的链接也将被自动处理。

| 属性 | 说明 
| --- | ---
|TypeName| 此模板应绑定到的度量类型。 应该至少有一个输出此类型的设备映射模板。
|PeriodInterval|创建的观察结果应表示的时间段。 支持的值为 0 （一个实例）、60（一个小时）、1440（一天）。
|**类别**|用于对创建的观察类型进行分类的任意数量的 [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept)。
|**代码**|应用于创建的观察结果的一个或多个[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|Codes[].Code|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的代码。
|Codes[].System|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的系统。
|Codes[].Display|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的显示。
|**值**|要在观察结果中提取并表示的值。 有关详细信息，请参阅[值类型模板](#valuetypes)。
|**组件**|可选：要基于观察结果创建的一个或多个组件。
|Components[].Codes|应用于组件的一个或多个[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|Components[].Value|要在组件中提取并表示的值。 有关详细信息，请参阅[值类型模板](#valuetypes)。

### <a name="value-type-templates"></a>值类型模板 <a name="valuetypes"></a>
下面是当前支持的值类型模板。 将来可能会添加其他模板。
#### <a name="sampleddata"></a>SampledData
表示 [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR 数据类型。观察度量将写入一个值流，从某个时间点开始，使用定义的周期向前递增。 如果不存在值，则将 `E` 写入数据流。 如果周期使另外两个值在数据流中占据相同的位置，则使用最新值。 当使用 SampledData 的观察结果被更新时，也会应用相同的逻辑。

| 属性 | 说明 
| --- | ---
|DefaultPeriod|使用的默认周期（以毫秒为单位）。 
|**单位**|要设置在 SampledData 原点上的单元。 

#### <a name="quantity"></a>数量
表示 [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) FHIR 数据类型。 如果分组中有多个值，则只使用第一个值。 当映射到相同观察结果的新值到达时，它将覆盖旧值。

| 属性 | 说明 
| --- | --- 
|**单位**| 单位的表示形式。
|**代码**| 单位的代码形式。
|**系统**| 定义代码单位形式的系统。

### <a name="codeableconcept"></a>CodeableConcept
表示 [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR 数据类型。 实际值未使用。

| 属性 | 说明 
| --- | --- 
|**文本**|纯文本表示形式。 
|**代码**|应用于创建的观察结果的一个或多个[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|Codes[].Code|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的代码。
|Codes[].System|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的系统。
|Codes[].Display|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的显示。

### <a name="examples"></a>示例
心率 - SampledData
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
步骤 - SampledData
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
血压 - SampledData
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
血压 - Quantity
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
已删除的设备 - CodeableConcept
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>后续步骤

查看与适用于 FHIR 的 Azure IoT 连接器（预览版）相关的常见问题解答。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器常见问题解答](fhir-faq.yml)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。