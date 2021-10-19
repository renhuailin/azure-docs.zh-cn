---
title: 如何在 Azure API for FHIR 中进行自定义搜索
description: 本文介绍如何在 Azure API 中定义你自己的自定义搜索参数，以便在数据库中使用 FHIR。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/03/2021
ms.author: cavoeg
ms.openlocfilehash: 28acdb257fe3eee41eb1585e9f1c60a26c91b0ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358502"
---
# <a name="defining-custom-search-parameters-for-azure-api-for-fhir"></a>为 Azure API for FHIR 定义自定义的搜索参数

FHIR 规范为所有资源定义了一组搜索参数，并且定义了特定于资源的搜索参数。 但在某些情况下，你可能想要针对资源中未由 FHIR 规范定义为标准搜索参数的某个元素进行搜索。 本文介绍如何定义你自己的、要在 Azure API for FHIR 中使用的[搜索参数](https://www.hl7.org/fhir/searchparameter.html)。

> [!NOTE]
> 每次创建、更新或删除搜索参数时，都需要运行一个[重新编制索引作业](how-to-run-a-reindex.md)来启用要在生产环境中使用的搜索参数。 下面将会概述如何在为整个 FHIR 服务器重新编制索引之前测试搜索参数。

## <a name="create-new-search-parameter"></a>创建新搜索参数

若要创建新搜索参数，请通过 `POST` 将 `SearchParameter` 资源发布到数据库。 以下代码示例演示如何将 [US Core Race SearchParameter](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) 添加到 `Patient` 资源。

```rest
POST {{FHIR_URL}}/SearchParameter

{
  "resourceType" : "SearchParameter",
  "id" : "us-core-race",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "Returns patients with a race extension matching the specified code.",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

``` 

> [!NOTE]
> 在将搜索参数发布到数据库并为数据库重新编制索引后，新搜索参数将显示在 FHIR 服务器的功能语句中。 只能通过查看功能语句中的 `SearchParameter` 来判断 FHIR 服务器是否支持某个搜索参数。 如果可以通过搜索该搜索参数找到该搜索参数，但在功能语句中看不到它，则仍然需要为该搜索参数编制索引。 在触发重新编制索引操作之前，可以发布多个搜索参数。

`SearchParameter` 的重要元素：

* url：用于描述搜索参数的唯一键。 许多组织（例如 HL7）对其定义的搜索参数使用标准 URL 格式，如上面的 US Core Race 搜索参数中所示。

* code：code 中存储的值是搜索时要使用的值 。 对于上面的示例，你将使用 `GET {FHIR_URL}/Patient?race=<code>` 进行搜索，以获取特定种族的所有患者。 对于搜索参数所应用到的资源，该代码必须是唯一的。

* base：描述搜索参数所应用到的资源。 如果搜索参数应用到所有资源，则你可以使用 `Resource`；否则，你可以列出所有相关资源。
 
* type：描述搜索参数的数据类型。 类型受限于对 Azure API for FHIR 的支持。 这意味着，不能定义 Special 类型的搜索参数，也不能定义[复合搜索参数](overview-of-search.md)，除非它是受支持的组合。

* expression：描述如何计算搜索值。 描述搜索参数时，即使规范不要求包含表达式，也必须包含表达式。 这是因为你需要表达式或 xpath 语法，而 Azure API for FHIR 会忽略 xpath 语法。

## <a name="test-search-parameters"></a>测试搜索参数

尽管在运行重新编制索引作业之前无法在生产环境中使用搜索参数，但可以在为整个数据库重新编制索引之前，通过多种方式测试搜索参数。 

首先，可以测试新搜索参数，以查看将要返回哪些值。 针对特定的资源实例运行以下命令（通过输入实例 ID）会得到一个值对列表，其中包含为特定患者存储的搜索参数名称和值。 此列表包含该资源的所有搜索参数，你可以滚动浏览以找到自己创建的搜索参数。 运行此命令不会更改 FHIR 服务器中的任何行为。 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
例如，若要查找某个患者的所有搜索参数，请运行以下命令：

```rest
GET https://{{FHIR_URL}}/Patient/{{PATIENT_ID}}/$reindex

```

结果将如下所示：

```json
{
  "resourceType": "Parameters",
  "id": "8be24e78-b333-49da-a861-523491c3437a",
  "meta": {
    "versionId": "1"
  },
  "parameter": [
    {
      "name": "deceased",
      "valueString": "http://hl7.org/fhir/special-values|false"
    },
    {
      "name": "language",
      "valueString": "urn:ietf:bcp:47|en-US"
    },
    {
      "name": "race",
      "valueString": "2028-9"
    },
...
```
看到搜索参数按预期显示后，可为单个资源重新编制索引，以使用相关元素测试搜索。 首先，为单个资源重新编制索引：

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

运行此命令将会针对你为该资源类型定义的特定资源的任何搜索参数设置索引。 这确实会更新 FHIR 服务器。 现在，可以搜索 use partial indices 头并将其设置为 true，这意味着，此命令返回的结果中的任何资源的搜索参数均已编制索引，即使并非所有该类型的资源的搜索参数均已编制索引。 

沿用前面的示例，可为一名患者编制索引以启用 US Core Race `SearchParameter`：

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

然后搜索特定种族的患者：

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

进行了测试并满意于搜索参数按预期正常工作后，请运行或计划重新编制索引作业，以便可以在 FHIR 服务器中将搜索参数用于生产用例。

## <a name="update-a-search-parameter"></a>更新搜索参数

若要更新某个搜索参数，请使用 `PUT` 创建该搜索参数的新版本。 必须在 `PUT` 请求正文的 `id` 元素中以及 `PUT` 调用中包含 `SearchParameter ID`。

> [!NOTE]
> 如果你不知道搜索参数的 ID，可以搜索此 ID。 使用 `GET {{FHIR_URL}}/SearchParameter` 会返回所有自定义搜索参数，你可以滚动浏览搜索参数以找到所需的搜索参数。 还可以按名称限制搜索。 对于以下示例，可以使用 `USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace` 搜索名称。

```rest
PUT {{FHIR_ULR}}/SearchParameter/{SearchParameter ID}

{
  "resourceType" : "SearchParameter",
  "id" : "SearchParameter ID",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "New Description!",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

```

结果将是更新的 `SearchParameter`，版本将会递增。

> [!Warning]
> 更新已在数据库中编制索引的 SearchParameter 时请小心。 更改现有 SearchParameter 的行为可能会影响预期行为。 建议立即运行重新编制索引作业。

## <a name="delete-a-search-parameter"></a>删除搜索参数

如果需要删除搜索参数，请使用以下命令：

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> 删除已在数据库中编制索引的 SearchParameter 时请小心。 更改现有 SearchParameter 的行为可能会影响预期行为。 建议立即运行重新编制索引作业。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何创建搜索参数。 接下来，可以了解如何为 FHIR 服务器重新编制索引。

>[!div class="nextstepaction"]
>[如何运行重新索引作业](how-to-run-a-reindex.md)
