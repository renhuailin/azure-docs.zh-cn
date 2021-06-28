---
title: 概念：Azure IoT Connector for FHIR (预览版中的) 模板Azure API for FHIR
description: 了解如何在 Azure IoT Connector for FHIR (预览版) 。 设备映射模板将设备数据转换为规范化架构。 FHIR 映射模板将规范化消息转换为基于 FHIR 的观察资源。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: rabhaiya
ms.openlocfilehash: 690a406c9cb0dd7644656c78236a2001ae0f0f2e
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112991588"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）映射模版
本文详细介绍如何使用映射Azure IoT为 快速医疗保健互操作性资源 (FHIR&#174;) * 配置连接器。

适用于 FHIR 的 Azure IoT 连接器需要两种类型的基于 JSON 的映射模板。 第一种类型 **"设备** 映射"负责映射发送到 Azure 事件 `devicedata` 中心终点的设备有效负载。 它提取类型、设备标识符、测量日期时间和 () 。 第二种类型 **（FHIR 映射**）控制 FHIR 资源的映射。 它允许配置观察周期的长度、用于存储值的 FHIR 数据类型，以及用于 (术语) 。 

映射模板根据类型组成 JSON 文档。 然后，这些 JSON 文档会通过 Azure IoT 添加到适用于 FHIR 的 Azure 门户。 通过"配置设备映射"页和"FHIR 映射"文档，通过"配置 FHIR 映射"页添加"**设备映射"** 文档。

> [!NOTE]
> 映射模板存储在基础 Blob 存储中，并按计算执行从 Blob 加载。 更新后，它们应立即生效。 

## <a name="device-mapping"></a>设备映射
设备映射提供映射功能，用于将设备内容提取为通用格式，供进一步评估。 接收的每条消息都针对所有模板进行评估。 此方法允许将单个入站消息预测到多个出站消息，这些消息稍后映射到 FHIR 中的不同观察结果。 结果是一个规范化数据对象，表示模板分析的值。 规范化数据模型具有一些必须找到和提取的必需属性：

| 属性 | 描述 |
| - | - |
|**Type**|用于对度量进行分类的名称/类型。 此值用于绑定到所需的 FHIR 映射模板。  多个模板可以输出到同一类型，从而可以将多个设备的不同表示形式映射到单个常见输出。|
|**OccurenceTimeUtc**|测量发生的时间。|
|**DeviceId**|设备的标识符。 此值应匹配目标 FHIR 服务器上存在的设备资源上的标识符。|
 |**属性**|提取至少一个 属性，以便值可以保存在创建的观察资源中。  属性是规范化期间提取的键值对的集合。|

下面是规范化期间发生的情况的概念示例。

![规范化示例](media/concepts-iot-mapping-templates/normalization-example.png)

内容有效负载本身是 Azure 事件中心消息，由三部分组成：正文、属性和 SystemProperties。 `Body`是表示 UTF-8 编码字符串的字节数组。 在模板评估期间，字节数组会自动转换为字符串值。 `Properties` 是供消息创建者使用的键值集合。 `SystemProperties` 也是 Azure 事件中心框架保留的键值集合，其自动填充条目。

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

### <a name="mapping-with-json-path"></a>使用 JSON 路径进行映射
目前支持的三种设备内容模板类型都依赖于 JSON 路径来匹配所需的模板和提取的值。 有关 JSON 路径的信息，可在此处 [找到](https://goessner.net/articles/JsonPath/)。 所有三种模板类型都使用 [JSON .NET 实现](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 来解析 JSON 路径表达式。

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate 允许使用 JSON 路径在事件中心消息中匹配和提取值。

| 属性 | 说明 |<div style="width:150px">示例</div>
| --- | --- | --- 
|**TypeName**|要与与模板匹配的度量关联的类型。|`heartrate`
|**TypeMatchExpression**|针对事件中心有效负载求得的 JSON 路径表达式。 如果找到匹配的 JToken，则模板将被视为匹配项。 将针对此处匹配的提取的 JToken 计算所有后续表达式。|`$..[?(@heartRate)]`
|**TimestampExpression**|用于提取度量的 OccurenceTimeUtc 的时间戳值的 JSON 路径表达式。|`$.endDate`
|**DeviceIdExpression**|用于提取设备标识符的 JSON 路径表达式。|`$.deviceId`
|**PatientIdExpression**|*可选*：用于提取患者标识符的 JSON 路径表达式。|`$.patientId`
|**EncounterIdExpression**|*可选*：用于提取遇到标识符的 JSON 路径表达式。|`$.encounterId`
|**Values[]。ValueName**|要与后续表达式提取的值关联的名称。 用于在 FHIR 映射模板中绑定所需的值/组件。 |`hr`
|**Values[]。ValueExpression**|提取所需值的 JSON 路径表达式。|`$.heartRate`
|**Values[]。必填**|将要求值存在于有效负载中。  如果未找到，将不会生成度量，并且将引发 InvalidOperationException。|`true`

##### <a name="examples"></a>示例
---
**心率**

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
**血压**

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

**从单个消息中预测多个度量**

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
*模板 1*
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
*模板 2*
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

**在消息中从数组中预测多个度量**

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

IotJsonPathContentTemplate 类似于 JsonPathContentTemplate，但不需要 DeviceIdExpression 和 TimestampExpression。

使用此模板的假设是，评估的消息是使用 Azure IoT 中心[设备](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)SDK 或导出数据 (旧版) 功能[](../../iot-central/core/howto-export-data-legacy.md)[Azure IoT Central。](../../iot-central/core/overview-iot-central.md) 使用这些 SDK 时，设备标识 (假定来自 Azure Iot Hub/Central 的设备标识符注册为目标 FHIR 服务器) 上的设备资源的标识符，并且消息的时间戳是已知的。 如果使用的是 Azure IoT 中心 SDK，但在消息正文中对设备标识或度量时间戳使用自定义属性，则仍可以使用 JsonPathContentTemplate。

*注意：使用 IotJsonPathContentTemplate 时，TypeMatchExpression 应解析为 JToken 作为整个消息。请参阅以下示例。* 
##### <a name="examples"></a>示例
---
**心率**

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
**血压**

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

IotCentralJsonPathContentTemplate 也不需要 DeviceIdExpression 和 TimestampExpression，并且当通过 Azure IoT Central 的"导出数据"[](../../iot-central/core/howto-export-data.md)功能发送要[评估的消息时，使用](../../iot-central/core/overview-iot-central.md)IotCentralJsonPathContentTemplate。 使用此功能时， (Azure Iot Central 中的设备标识符注册为目标 FHIR 服务器上设备资源的标识符) 并且知道消息的时间戳。 如果使用的是 Azure IoT Central导出功能，但在消息正文中对设备标识或度量时间戳使用自定义属性，则仍可以使用 JsonPathContentTemplate。

*注意：使用 IotCentralJsonPathContentTemplate 时，TypeMatchExpression 应解析为 JToken 作为整个消息。请参阅以下示例。* 
##### <a name="examples"></a>示例
---
**心率**

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
**血压**

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
将设备内容提取到规范化模型中后，会根据设备标识符、度量类型和时间段收集和分组数据。 将发送此分组的输出，以转换为当前正在 ([的](https://www.hl7.org/fhir/observation.html) FHIR) 。 此处，FHIR 映射模板控制如何将数据映射到 FHIR 观察。 是创建一个观察点还是创建一个小时内的观察值？ 应在观察结果中添加哪些代码？ 值应表示为[SampledData 还是](https://www.hl7.org/fhir/datatypes.html#SampledData) [Quantity？](https://www.hl7.org/fhir/datatypes.html#Quantity) 这些数据类型都是 FHIR 映射配置控件的所有选项。

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate 目前是 FHIR 映射目前唯一支持的模板。  它允许定义代码、有效周期和观察值。 支持多种值类型[：SampledData、CodeableConcept](https://www.hl7.org/fhir/datatypes.html#SampledData)和[Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)。 [](https://www.hl7.org/fhir/datatypes.html#CodeableConcept) 除了这些可配置值外，会自动处理观察资源的标识符以及链接到适当的设备和患者资源。

| 属性 | 说明 
| --- | ---
|**TypeName**| 此模板应绑定到的度量类型。 应至少有一个输出此类型的设备映射模板。
|**PeriodInterval**|创建的观察值应表示的时间段。 支持的值为 0 (实例) ，60 (小时) ，每天 1440 (1440) 。
|**类别**|任意数目 [的 CodeableConcepts，](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) 用于对创建的观察类型进行分类。
|**代码**|要应用于 [所创建的](http://hl7.org/fhir/datatypes-definitions.html#coding) 观察结果的一个或多个编码。
|**Codes[]。代码**|编码 [的代码](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[]。系统**|编码 [的系统](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[]。显示**|编码 的 [显示](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**值**|要提取的值，在观察中表示。 有关详细信息，请参阅值 [类型模板](#valuetypes)。
|**组件**|*可选：* 要基于观察值创建的一个或多个组件。
|**Components[]。代码**|要应用于 [组件的](http://hl7.org/fhir/datatypes-definitions.html#coding) 一个或多个编码。
|**Components[]。价值**|在组件中提取和表示的值。 有关详细信息，请参阅值 [类型模板](#valuetypes)。

### <a name="value-type-templates"></a>值类型模板 <a name="valuetypes"></a>
下面是当前支持的值类型模板。 将来可能会添加其他模板。
#### <a name="sampleddata"></a>SampledData
表示 [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR 数据类型。观察度量将写入值流，从时间点开始，使用定义的周期向前递增。 如果不存在值， `E` 则 将写入数据流。 如果期间使另外两个值在数据流中占据相同的位置，则使用最新值。 更新使用 SampledData 的观察结果时，将应用相同的逻辑。

| 属性 | 说明 
| --- | ---
|**DefaultPeriod**|使用的默认时间段（以毫秒为单位）。 
|**单位**|在 SampledData 的原点上设置的单元。 

#### <a name="quantity"></a>数量
表示 [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) FHIR 数据类型。 如果分组中存在多个值，则仅使用第一个值。 当映射到同一观测值的新值到达时，它将覆盖旧值。

| 属性 | 说明 
| --- | --- 
|**单位**| 单位表示形式。
|**代码**| 单元的编码形式。
|**系统**| 定义编码单位窗体的系统。

### <a name="codeableconcept"></a>CodeableConcept
表示 [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR 数据类型。 实际值未使用。

| 属性 | 说明 
| --- | --- 
|**文本**|纯文本表示形式。 
|**代码**|要应用于 [所创建的](http://hl7.org/fhir/datatypes-definitions.html#coding) 观察结果的一个或多个编码。
|**Codes[]。代码**|编码 [的代码](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[]。系统**|编码 [的系统](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[]。显示**|编码 的 [显示](http://hl7.org/fhir/datatypes-definitions.html#coding)。

### <a name="examples"></a>示例
**心速 - SampledData**
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
**步骤 - SampledData**
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
**压力 - SampledData**
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
**压力 - 数量**
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
**已删除设备 - CodeableConcept**
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

查看有关 Azure IoT Connector for FHIR (预览版) 。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 常见问题解答](fhir-faq.yml)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。