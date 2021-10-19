---
title: $validate Azure 医疗保健 API 中的 FHIR 服务上的配置文件创建 FHIR 资源
description: $validate FHIR 服务中的配置文件创建 FHIR 资源
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: b52389b6007c436614840a9bad568a0e81cf7fa2
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779563"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>如何根据配置文件验证 FHIR 资源

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

HL7 FHIR 定义了一种标准且可互操作的方式，以存储和交换医疗保健数据。 即使在基本 FHIR 规范中，根据使用 FHIR 的上下文定义其他规则或扩展也很有帮助。 对于 FHIR 的此类特定于上下文的用法，FHIR 配置文件可用于实现额外的规范层。

[FHIR 配置文件](https://www.hl7.org/fhir/profiling.html)描述了表示为 `StructureDefinition` 的资源中的其他上下文，例如约束或扩展。 HL7 FHIR 标准定义了一组基本资源，且这些标准基本资源具有通用定义。 通过 FHIR 配置文件，可以使用约束和扩展缩小资源定义范围并对其进行自定义。

Azure 医疗保健 API 中的 FHIR (称为 FHIR 服务) 允许根据配置文件验证资源，以查看资源是否符合配置文件。 本文将逐步介绍 FHIR 配置文件的基础知识，以及在创建和更新资源时如何根据配置文件使用 `$validate` 验证资源。

## <a name="fhir-profile-the-basics"></a>FHIR 配置文件：基础知识

配置文件在资源上设置其他上下文，通常表示为 `StructureDefinition` 资源。 `StructureDefinition` 定义了一组有关资源或数据类型内容的规则，例如资源所具有的字段以及这些字段所可以采用的值。 例如，配置文件可以限制基数（例如，将最大基数设置为 0 以排除元素），将元素的内容限制为单个固定值，或定义资源所需的扩展。 该资源还可以指定对现有配置文件的其他约束。 `StructureDefinition` 由其规范 URL 标识：

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

在 `{profile}` 字段中，可指定配置文件的名称。

例如：

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` 是基本配置文件，需要有关患者出生登记地址的信息。
- `http://hl7.org/fhir/StructureDefinition/bmi` 是另一个基本配置文件，用于定义如何表示身体质量指数 (BMI) 观察值。
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` 是 US Core 配置文件，用于设置与患者关联的 `AllergyIntolerance` 资源的最低预期，并标识扩展和值集等必填字段。

当资源符合配置文件时，其配置文件在 `profile` 字段内的资源中指定。

```json
{
  "resourceType" : "Patient",
  "id" : "ExamplePatient1",
  "meta" : {
    "lastUpdated" : "2020-10-30T09:48:01.8512764-04:00",
    "source" : "Organization/PayerOrganizationExample1",
    "profile" : [
      "http://hl7.org/fhir/us/carin-bb/StructureDefinition/C4BB-Patient"
    ]
  },
```

### <a name="base-profile-and-custom-profile"></a>基本配置文件和自定义配置文件

存在两种类型的配置文件，即基本配置文件和自定义配置文件。 基本配置文件是资源需要符合的基本 `StructureDefinition`，由基本资源（如 `Patient` 或 `Observation`）定义。 例如，身体质量指数 (BMI) `Observation` 配置文件如下所示：

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

自定义配置文件是除基本配置文件之外的一组附加约束，用于限制或添加不属于基本规范的资源参数。 自定义配置文件非常有用，原因在于可以通过指定现有基本资源上的约束和扩展来自定义自己的资源定义。 例如，你可能希望生成一个基于 `Patient` 性别显示 `AllergyIntolerance` 资源实例的配置文件，在这种情况下，你可以在具有 `AllergyIntolerance` 配置文件的现有 `Patient` 配置文件的基础上创建自定义配置文件。

> [!NOTE]
> 自定义配置文件必须建立在基本资源基础之上，并且不能与基本资源相冲突。 例如，如果元素的基数为 1..1，则自定义配置文件无法将其设为可选。

自定义配置文件也由各种实现指南指定。 一些常见的实现指南包括：

|名称 |URL
|---- |----
US Core |<https://www.hl7.org/fhir/us/core/>
CARIN 蓝色按钮 |<http://hl7.org/fhir/us/carin-bb/>
Da Vinci 付款人数据交换 |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>访问配置文件和存储配置文件

### <a name="storing-profiles"></a>存储配置文件

若要将配置文件存储到服务器，可以执行 `POST` 请求：

```rest
POST http://<your FHIR service base URL>/StructureDefinition
```

例如，若要存储 `us-core-allergyintolerance` 配置文件，可以执行下列操作：

```rest
POST https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

存储并检索 US Core 过敏不耐受配置文件的位置：

```json
{
    "resourceType" : "StructureDefinition",
    "id" : "us-core-allergyintolerance",
    "text" : {
        "status" : "extensions"
    },
    "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance",
    "version" : "3.1.1",
    "name" : "USCoreAllergyIntolerance",
    "title" : "US  Core AllergyIntolerance Profile",
    "status" : "active",
    "experimental" : false,
    "date" : "2020-06-29",
        "publisher" : "HL7 US Realm Steering Committee",
    "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
    "description" : "Defines constraints and extensions on the AllergyIntolerance resource for the minimal set of data to query and retrieve allergy information.",

...
```

大多数配置文件具有 `StructureDefinition` 资源类型，但也可以是 `ValueSet` 和 `CodeSystem` 类型，这都是[术语](http://hl7.org/fhir/terminologies.html)资源。 例如，如果以 JSON 格式 `POST` 一个 `ValueSet` 配置文件，服务器将返回存储的配置文件，并针对该配置文件分配 `id`，跟 `StructureDefinition` 的情况一样。 如下是上传“[条件严重性](https://www.hl7.org/fhir/valueset-condition-severity.html)”配置文件时获取的示例，该配置文件指定了条件/诊断严重性等级的标准：

```json
{
    "resourceType": "ValueSet",
    "id": "35ab90e5-c75d-45ca-aa10-748fefaca7ee",
    "meta": {
        "versionId": "1",
        "lastUpdated": "2021-05-07T21:34:28.781+00:00",
        "profile": [
            "http://hl7.org/fhir/StructureDefinition/shareablevalueset"
        ]
    },
    "text": {
        "status": "generated"
    },
    "extension": [
        {
            "url": "http://hl7.org/fhir/StructureDefinition/structuredefinition-wg",
            "valueCode": "pc"
        }
    ],
    "url": "http://hl7.org/fhir/ValueSet/condition-severity",
    "identifier": [
        {
            "system": "urn:ietf:rfc:3986",
            "value": "urn:oid:2.16.840.1.113883.4.642.3.168"
        }
    ],
    "version": "4.0.1",
    "name": "Condition/DiagnosisSeverity",
    "title": "Condition/Diagnosis Severity",
    "status": "draft",
    "experimental": false,
    "date": "2019-11-01T09:29:23+11:00",
    "publisher": "FHIR Project team",
...
```

可以看到 `resourceType` 是 `ValueSet`，配置文件的 `url` 还指定这是类型 `ValueSet`: `"http://hl7.org/fhir/ValueSet/condition-severity"`。

### <a name="viewing-profiles"></a>查看配置文件

可以使用 `GET` 请求访问服务器中现有的自定义配置文件。 所有有效的配置文件（如实现指南中具有有效规范 URL 的配置文件）均应可通过查询进行访问：

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

其中，字段 `{canonicalUrl}` 将替换为配置文件的规范 URL。

例如，若要查看 US Core `Goal` 资源配置文件：

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

这会返回 US Core Goal 配置文件的 `StructureDefinition` 资源，如下所示：

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "us-core-goal",
  "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal",
  "version" : "3.1.1",
  "name" : "USCoreGoalProfile",
  "title" : "US Core Goal Profile",
  "status" : "active",
  "experimental" : false,
  "date" : "2020-07-21",
  "publisher" : "HL7 US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
  "description" : "Defines constraints and extensions on the Goal resource for the minimal set of data to query and retrieve a patient's goal(s).",
...
```

FHIR 服务不返回基本配置文件的实例，但可以在 HL7 网站上轻松找到这些实例， `StructureDefinition` 例如：

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>功能语句中的配置文件

列出了 FHIR 服务的所有可能的行为，这些行为将用作服务器功能的语句，例如结构 `Capability Statement` 定义和值集。 FHIR 服务使用以下形式上传和存储的配置文件的信息更新功能语句：

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

上述语句将显示配置文件（可描述对资源的总体支持）的所有规范，包括对基数、绑定、扩展等限制的任何约束。 因此，以 `StructureDefinition` 的形式 `POST` 配置文件，以及 `GET` 资源元数据以查看完整的功能语句时，将在 `supportedProfiles` 参数旁边看到自己上传的配置文件的所有详细信息。

例如，如果 `POST` US Core 患者配置文件，如下所示：

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "url": "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient",
  "version": "3.1.1",
  "name": "USCorePatientProfile",
  "title": "US Core Patient Profile",
  "status": "active",
  "experimental": false,
  "date": "2020-06-27",
  "publisher": "HL7 US Realm Steering Committee",
...
```

为 `metadata` 发送 `GET` 请求：

```rest
GET http://<your FHIR service base URL>/metadata
```

你将收到 `CapabilityStatement`，其中包含有关你上传至 FHIR 服务器的 US Core 患者配置文件的以下信息：

```json
...
{
    "type": "Patient",
    "profile": "http://hl7.org/fhir/StructureDefinition/Patient",
    "supportedProfile":[
        "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
    ],
...
```

## <a name="validating-resources-against-the-profiles"></a>根据配置文件验证资源

FHIR 资源（如 `Patient` 或 `Observation`）可以表示其符合特定的配置文件。 这允许 FHIR 服务 **根据** 关联的配置文件或指定的配置文件验证给定的资源。 根据配置文件验证资源意味着检查资源是否符合配置文件，包括 `Resource.meta.profile` 或实现指南中列出的规范。

可以使用下列两种方法验证资源。 首先，可以针对 `$validate` FHIR 服务中已有的资源使用操作。 其次，可以将该资源作为资源 `Update` 或 `Create` 操作的一部分 `POST` 到服务器。 在这两种情况下，都可以通过 FHIR 服务配置决定在资源不符合所需配置文件时要执行什么操作。

### <a name="using-validate"></a>使用 $validate

`$validate` 操作检查所提供的配置文件是否有效，以及资源是否符合指定的配置文件。 如 [HL7 FHIR 规范](https://www.hl7.org/fhir/resource-operation-validate.html)中所述，还可以为 `$validate` 指定 `mode`，例如 `create` 和 `update`：

- `create`：服务器检查配置文件内容在现有资源中是否唯一，以及是否可以将其创建为新资源
- `update`：检查配置文件是否是针对指定的现有资源的更新（例如，未对不可变字段进行更改）

服务器将始终返回 `OperationOutcome` 作为验证结果。

#### <a name="validating-an-existing-resource"></a>验证现有资源

若要验证现有资源，请在 `GET` 请求中使用 `$validate`：

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

例如：

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

在上述示例中，将验证现有 `Patient` 资源 `a6e11662-def8-4dde-9ebc-4429e68d130e`。 如果该资源有效，将得到 `OperationOutcome`，如下所示：

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "information",
            "code": "informational",
            "diagnostics": "All OK"
        }
    ]
}
```

如果该资源无效，将收到错误代码和错误消息，其中详细说明资源无效的原因。 `4xx` 或 `5xx` 错误表示无法执行验证本身，并且不知道资源是否有效。 返回的带有错误消息的示例 `OperationOutcome` 可能如下所示：

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.identifier.value' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient.identifier[1]"
            ]
        },
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.gender' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient"
            ]
        }
    ]
}
```

在上述示例中，资源不符合所提供的 `Patient` 配置文件，该配置文件需要患者标识符值和性别。

如果希望将配置文件指定为参数，则可以指定要验证的配置文件的规范 URL，例如 `heartrate` 的 HL7 基本配置文件示例如下：

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>验证新资源

若要验证上传到服务器的新资源，可以执行 `POST` 请求：

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

例如：

```rest
POST https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Patient/$validate 
```

此请求将创建你在请求有效负载中指定的新资源（无论是 JSON 格式还是 XML 格式）并验证上传的资源。 然后，此请求将返回 `OperationOutcome`，作为对新资源进行验证的结果。

### <a name="validate-on-resource-create-or-resource-update"></a>创建资源或更新资源时进行验证

你可以选择何时验证资源，例如在创建资源或更新资源时。 可以在服务器配置设置中的 `CoreFeatures` 下指定此时机：

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

如果资源符合所提供的 `Resource.meta.profile` 并且该配置文件存在于系统中，则服务器将按照上述配置设置执行相应操作。 如果服务器中不存在所提供的配置文件，则验证请求将遭到忽略并留在 `Resource.meta.profile` 中。
验证通常是一项代价高昂的操作，往往只在测试服务器或一小部分资源上运行，因此，必须使用上述方法在服务器端打开或关闭验证操作。 如果服务器配置指定在创建/更新资源时退出验证，则用户可以通过在创建/更新请求的 `header` 中进行指定以替代此行为：

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>后续步骤

本文介绍了 FHIR 配置文件，以及如何使用 $validate 根据配置文件验证资源。 若要了解 FHIR 服务的其他受支持功能，请查看：

>[!div class="nextstepaction"]
>[FHIR 支持的功能](fhir-features-supported.md)
