---
title: IoT 连接器中的 FHIR 映射模板 - Azure Healthcare APIs
description: 本文介绍了如何使用 IoT 连接器中的 FHIR 映射模板。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: rabhaiya
ms.openlocfilehash: be40d72ea7e0da6d1ef48b2b9ca28d73f85f4d29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779701"
---
# <a name="how-to-use-the-fhir-mapping-template"></a>如何使用 FHIR 映射模板

本文介绍如何使用 FHIR 映射模板配置 IoT 连接器。

## <a name="fhir-mapping"></a>FHIR 映射

将设备内容提取到规范化模型中后，会根据设备标识符、度量类型和时间段收集和分组数据。 将发送此分组的输出，以转换为 FHIR 资源（目前的[观察结果](https://www.hl7.org/fhir/observation.html)）。 FHIR 映射模板控制如何将数据映射到 FHIR 观察结果。 是创建一个观察点还是超过一个小时的时间段的观察值？ 应在观察结果中添加哪些代码？ 值应该表示为 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 还是 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)？ 这些数据类型是 FHIR 映射配置控件的所有选项。

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate

CodeValueFhirTemplate 目前是 FHIR 映射唯一支持的模板。  它支持定义代码、有效周期和观察值。 支持多种值类型：[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)、[CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept) 和 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)。 除了这些可配置的值，观察结果资源的标识符以及与适当的设备和患者资源的链接也将被自动处理。

| 属性 | 说明 
| --- | ---
|TypeName| 此模板应绑定到的度量类型。 应该至少有一个输出此类型的设备映射模板。
|PeriodInterval|创建的观察结果应表示的时间段。 支持的值为 0 （一个实例）、60（一个小时）、1440（一天）。
|**类别**|用于对创建的观察类型进行分类的任意数量的 [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept)。
|**代码**|应用于创建的观察结果的一个或多个[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[].Code**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的代码。
|**Codes[].System**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的系统。
|**Codes[].Display**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的显示。
|**值**|要在观察结果中提取并表示的值。 有关详细信息，请参阅[值类型模板](#value-type-templates)。
|**组件**|*可选：* 要基于观察结果创建的一个或多个组件。
|**Components[].Codes**|应用于组件的一个或多个[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Components[].Value**|要组件中提取并表示的值。 有关详细信息，请参阅[值类型模板](#value-type-templates)。

### <a name="value-type-templates"></a>值类型模板

下面是当前支持的值类型模板：

#### <a name="sampleddata"></a>SampledData

表示[SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR 数据类型。观察度量值将写入一个值流，此流始于某个时间点，并按定义的时间段向前递增。 如果不存在值，则将 `E` 写入数据流。 如果该时间段会导致新增的两个值在数据流中占据相同的位置，则会使用最新值。 当使用 SampledData 得到的观察结果被更新时，也会应用这一逻辑。

| 属性 | 说明 
| --- | ---
|**DefaultPeriod**|使用的默认周期（以毫秒为单位）。 
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
|**Codes[].Code**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的代码。
|**Codes[].System**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的系统。
|**Codes[].Display**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的显示。

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


## <a name="next-steps"></a>后续步骤


>[!div class="nextstepaction"]
>[如何使用设备映射模板](how-to-use-device-mapping-iot.md)
