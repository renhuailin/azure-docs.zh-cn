---
title: $validate上的配置文件创建 FHIR Azure API for FHIR
description: $validate配置文件创建 FHIR 资源
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/06/2021
ms.author: ginle
ms.openlocfilehash: 679d8b2ac86ec63d33fcd5cd069a3135d33ab981
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109657036"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>如何根据配置文件验证 FHIR 资源

HL7 FHIR 定义了存储和交换医疗保健数据的标准且可互操作的方式。 即使在基本 FHIR 规范中，根据使用 FHIR 的上下文定义其他规则或扩展也很有帮助。 对于 FHIR 的此类特定于上下文的用法 **，FHIR** 配置文件用于额外的规范层。

[FHIR 配置文件](https://www.hl7.org/fhir/profiling.html) 描述表示为 的资源的其他上下文，例如约束或扩展 `StructureDefinition` 。 HL7 FHIR 标准定义了一组基本资源，并且这些标准基资源具有通用定义。 通过 FHIR 配置文件，可以使用约束和扩展缩小范围并自定义资源定义。

Azure API for FHIR允许针对配置文件验证资源，以查看资源是否符合配置文件。 本文逐步介绍 FHIR 配置文件的基础知识，以及如何在创建和更新资源时根据配置文件 `$validate` 使用 验证资源。

## <a name="fhir-profile-the-basics"></a>FHIR 配置文件：基础知识

配置文件设置资源的其他上下文，通常表示为 `StructureDefinition` 资源。 `StructureDefinition` 定义一组有关资源或数据类型的内容的规则，例如资源具有哪些字段以及这些字段可以具有哪些值。 例如，配置文件可以限制基数 (例如，将最大基数设置为 0 以排除元素) ，将元素的内容限制为单个固定值，或定义资源的所需扩展。 它还可以指定现有配置文件的其他约束。 `StructureDefinition`由其规范 URL 标识：

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

在 字段中 `{profile}` ，指定配置文件的名称。

例如：

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` 是一个基本配置文件，需要有关患者出生日期的注册地址的信息。
- `http://hl7.org/fhir/StructureDefinition/bmi` 是另一个基本配置文件，用于定义如何表示 BMI 观测 (体) 索引。
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` 是美国核心配置文件，用于设置与患者关联的资源的最小期望 `AllergyIntolerance` ，并标识了必填字段，例如扩展和值集。

### <a name="base-profile-and-custom-profile"></a>基本配置文件和自定义配置文件

有两种类型的配置文件：基本配置文件和自定义配置文件。 基本配置文件是资源需要遵从的基本配置文件 `StructureDefinition` ，由或等基本资源定义 `Patient` `Observation` 。 例如， (BMI) 配置文件的主体大容量索引 `Observation` 将会如下所示：

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

自定义配置文件是一组在基本配置文件之上的附加约束，限制或添加不属于基本规范的资源参数。 自定义配置文件非常有用，因为可以通过在现有基础资源上指定约束和扩展来自定义自己的资源定义。 例如，你可能想要生成一个配置文件，该配置文件显示 `AllergyIntolerance` 基于性别的资源实例 `Patient` ，在这种情况下，你将在 `Patient` 具有配置文件的现有配置文件的基础上创建自定义配置文件 `AllergyIntolerance` 。

> [!NOTE]
> 自定义配置文件必须在基资源的基础上生成，并且不能与基资源冲突。 例如，如果某个元素的基数为 1.. 1，则自定义配置文件不能是可选的。

自定义配置文件也由各种实现指南指定。 一些常见的实现指南包括：

|名称 |URL
|---- |----
美国核心 |<https://www.hl7.org/fhir/us/core/>
CARIN 蓝按钮 |<http://hl7.org/fhir/us/carin-bb/>
Da Vinci 付款人数据交换 |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>访问配置文件和存储配置文件

### <a name="viewing-profiles"></a>查看配置文件

您可以使用请求来访问服务器中的现有自定义配置文件 `GET` 。 所有有效的配置文件（例如实现指南中具有有效规范 Url 的配置文件）都应可通过查询来访问：

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

字段 `{canonicalUrl}` 将替换为配置文件的规范 URL。

例如，若要查看 US Core 资源 `Goal` 配置文件：

```rest
GET http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

这会返回 `StructureDefinition` "美国核心目标"配置文件的资源，该资源将如下所示：

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

我们的 FHIR 服务器不返回基本配置文件的实例，但可以在 HL7 网站上轻松找到这些实例， `StructureDefinition` 例如：

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`

### <a name="storing-profiles"></a>存储配置文件

若要将配置文件存储到服务器，可以执行 `POST` 请求：

```rest
POST http://<your FHIR service base URL>/{Resource}
```

其中，字段 `{Resource}` 将替换为 ，并且你的资源会以 `StructureDefinition` 或 `StructureDefinition` `POST` 格式被导出 `JSON` `XML` 到服务器。

大多数配置文件具有资源类型 `StructureDefinition` ，但它们也可以是 和 类型， `ValueSet` `CodeSystem` 它们是 [术语资源](http://hl7.org/fhir/terminologies.html) 。 例如，如果使用 JSON 格式的配置文件，服务器将返回存储的配置文件，并为其分配 ，就像使用 `POST` `ValueSet` `id` 一样 `StructureDefinition` 。 下面是上传"条件严重性"配置文件时获取的示例，[](https://www.hl7.org/fhir/valueset-condition-severity.html)该配置文件指定条件/诊断严重性等级的条件：

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
        "status": "generated",
        "div": "<div>!-- Snipped for Brevity --></div>"
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

### <a name="profiles-in-the-capability-statement"></a>功能语句中的配置文件

列出了 FHIR 服务器的所有可能行为，这些行为将用作服务器功能的语句，例如结构 `Capability Statement` 定义和值集。 Azure API for FHIR以以下形式更新功能语句，并提供有关上传和存储的配置文件的信息：

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

这些说明将显示配置文件的所有规范，该规范描述对资源的总体支持，包括对基数、绑定、扩展或其他限制的任何约束。 因此，当你以 的形式创建配置文件，以及资源元数据来查看完整的功能语句时，你将在参数旁边看到你上传的配置文件 `POST` `StructureDefinition` `GET` `supportedProfiles` 的所有详细信息。

例如，如果你的 `POST` "美国核心患者"配置文件，其开头如下所示：

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

并发送 `GET` 对 的请求 `metadata` ：

```rest
GET http://<your FHIR service base URL>/metadata
```

将返回， `CapabilityStatement` 其中包含上传到 FHIR 服务器的美国核心患者配置文件中的以下信息：

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

## <a name="validating-resources-against-the-profiles"></a>针对配置文件验证资源

FHIR 资源（如 `Patient` 或 `Observation` ）可以将其符合性表达给特定的配置文件。 这允许我们的 FHIR 服务器针对关联的配置文件或指定的配置文件来 **验证** 给定的资源。 针对配置文件验证资源意味着检查资源是否符合配置文件，包括或实现指南中列出的规范 `Resource.meta.profile` 。

可以通过两种方式来验证资源。 首先，你可以 `$validate` 对已在 FHIR 服务器中的资源使用操作。 其次，您可以将 `POST` 它作为资源或操作的一部分进行 `Update` 服务器 `Create` 。 在这两种情况下，你都可以通过 FHIR 服务器配置来决定当资源不符合所需的配置文件时要执行的操作。

### <a name="using-validate"></a>使用 $validate

`$validate`操作检查提供的配置文件是否有效，以及资源是否符合指定的配置文件。 如 [HL7 FHIR 规范](https://www.hl7.org/fhir/resource-operation-validate.html)中所述，还可以为指定 `mode` `$validate` ，例如 `create` 和 `update` ：

- `create`：服务器检查配置文件内容在现有资源中是否唯一，以及是否可将其创建为新资源
- `update`：检查配置文件是否为针对已命名现有资源的更新 (例如，不对不可变字段进行任何更改) 

服务器将始终返回 `OperationOutcome` 作为验证结果。

#### <a name="validating-an-existing-resource"></a>验证现有资源

若要验证现有资源，请 `$validate` 在请求中使用 `GET` ：

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

例如：

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

在上面的示例中，您将验证现有 `Patient` 资源 `a6e11662-def8-4dde-9ebc-4429e68d130e` 。 如果有效，你将获得如下所示的 `OperationOutcome` ：

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

如果资源无效，您将收到错误代码和错误消息，其中包含有关资源无效原因的详细信息。 或错误表示无法执行验证本身，并且资源 `4xx` `5xx` 是否有效是未知的。 返回 `OperationOutcome` 的错误消息示例可能如下所示：

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

在以上示例中，资源不符合提供的配置文件， `Patient` 该配置文件需要患者标识符值和性别。

如果要将配置文件指定为参数，可以指定要验证的配置文件的规范 URL，例如以下示例，包含 US Core 配置文件和 的 `Patient` 基配置文件 `heartrate` ：

```rest
GET http://<your FHIR service base URL>/{Resource}/{Resource ID}/$validate?profile={canonicalUrl}
```

例如：

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate?profile=http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient
GET http://my-fhir-server.azurewebsites.net/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>验证新资源

如果要验证要上传到服务器的新资源，可以执行 `POST` 请求：

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

例如：

```rest
POST http://my-fhir-server.azurewebsites.net/Patient/$validate 
```

此请求将创建请求有效负载中指定的新资源（无论是 JSON 格式还是 XML 格式）并验证上传的资源。 然后，它将作为 `OperationOutcome` 新资源的验证结果返回 。

### <a name="validate-on-resource-create-or-resource-update"></a>在资源 CREATE 或资源更新上进行验证

可以选择何时验证资源，例如资源 CREATE 或 UPDATE。 可以在服务器配置设置中的 下指定 `CoreFeatures` 此名称：

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

如果资源符合提供的 并且配置文件存在于系统中，则服务器将按照上述 `Resource.meta.profile` 配置设置执行相应操作。 如果提供的配置文件不存在于服务器中，则验证请求将被忽略，并留在 中 `Resource.meta.profile` 。
验证通常是一项成本高昂的操作，因此它通常仅在测试服务器或一小部分资源中运行 - 因此，在服务器端启用或关闭验证操作非常重要。 如果服务器配置指定在资源"创建/更新"上选择退出验证，则用户可以通过在"创建/更新"请求的 中指定该行为来 `header` 替代该行为：

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>后续步骤

本文介绍了 FHIR 配置文件，以及如何使用 FHIR 配置文件验证$validate。 若要了解适用于 FHIR 的其他支持功能的 Azure API，请参阅：

>[!div class="nextstepaction"]
>[FHIR 支持的功能](fhir-features-supported.md)
