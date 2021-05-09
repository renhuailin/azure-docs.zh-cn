---
title: 如何在 Azure API for FHIR 中执行自定义搜索
description: 本文介绍如何定义要在数据库中使用的自定义搜索参数。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/03/2021
ms.author: cavoeg
ms.openlocfilehash: d61e886771fb3bc667ff9e219de736c03e591ba7
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108765190"
---
# <a name="defining-custom-search-parameters"></a>定义自定义搜索参数

FHIR 规范定义了)  (资源的所有资源和搜索参数的一组搜索参数。 但是，在某些情况下，你可能想要在不是由 FHIR 规范定义为标准搜索参数的资源中搜索元素。 本文介绍如何在用于 FHIR 的 Azure API 中定义自己的 [搜索参数](https://www.hl7.org/fhir/searchparameter.html) 。

> [!NOTE]
> 每次创建、更新或删除搜索参数时，都需要运行 [索引编制作业](how-to-run-a-reindex.md) ，以使搜索参数在生产中使用。 下面概述了如何在重新索引整个 FHIR 服务器之前测试搜索参数。

## <a name="create-new-search-parameter"></a>创建新的搜索参数

若要创建新的搜索参数，请 `POST` 使用 `SearchParameter` 数据库的资源。 下面的代码示例演示如何将 [美国核心种族 SearchParameter](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) 添加到 `Patient` 资源。

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
> 将搜索参数发布到数据库后，新的搜索参数将出现在 FHIR 服务器的功能声明中 **，并** 对数据库重新进行索引。 `SearchParameter`在功能语句中查看是唯一的方法，告诉你的 FHIR 服务器是否支持搜索参数。 如果可以通过搜索搜索参数找到搜索参数，但在功能声明中看不到，则仍需为搜索参数编制索引。 可以在触发索引编制操作之前发布多个搜索参数。

的重要元素 `SearchParameter` ：

* **url**：用于描述搜索参数的唯一键。 许多组织（如 HL7）对其定义的搜索参数使用标准 URL 格式，如上文中的美国核心种族搜索参数中所示。

* **代码**：在 **代码** 中存储的值是搜索时使用的值。 在上面的示例中，您将搜索 `GET {FHIR_URL}/Patient?race=<code>` 以获取特定种族的所有患者。 ) 搜索参数适用于的资源 (的代码必须是唯一的。

* **基本**：描述搜索参数适用)  (的资源。 如果搜索参数适用于所有资源，则可以使用 `Resource` ; 否则，可以列出所有相关资源。
 
* **类型**：描述搜索参数的数据类型。 类型受限于 Azure API for FHIR 的支持。 这意味着，不能定义类型为 "特殊" 或 "定义 [复合搜索参数](overview-of-search.md) " 的搜索参数，除非它是受支持的组合。

* **expression**：描述如何计算搜索的值。 描述搜索参数时，必须包括表达式，即使规范不需要该表达式。 这是因为需要表达式或 xpath 语法，而用于 FHIR 的 Azure API 将忽略 xpath 语法。

## <a name="test-search-parameters"></a>测试搜索参数

虽然在运行索引编制作业之前不能在生产中使用搜索参数，但在重新索引整个数据库之前有几种方法可以测试搜索参数。 

首先，可以测试新的搜索参数，以查看将返回哪些值。 通过在特定资源实例上运行以下命令 (通过输入其 ID) ，你将获得值对列表，其中包含搜索参数名称和为特定患者存储的值。 这将包括资源的所有搜索参数，你可以滚动浏览查找所创建的搜索参数。 运行此命令不会更改 FHIR 服务器中的任何行为。 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
例如，若要查找患者的所有搜索参数：

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
看到搜索参数按预期方式显示后，可以将单个资源重新索引为使用元素测试搜索。 首先，将对单个资源重新编制索引：

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

运行此参数会为你为该资源类型定义的特定资源设置任何搜索参数的索引。 这会对 FHIR 服务器进行更新。 现在，你可以搜索并将 "使用部分索引" 标头设置为 "true"，这意味着它将返回任何资源带有索引搜索参数的结果，即使并非该类型的所有资源都已建立索引。 

继续上面的示例，可以为一个患者编制索引，以实现美国核心竞赛 `SearchParameter` ：

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

然后搜索具有特定争用的患者：

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

经过测试并满足搜索参数按预期方式工作后，运行或计划重建索引作业，以便可以在 FHIR 服务器中将搜索参数用于生产用例。

## <a name="update-a-search-parameter"></a>更新搜索参数

若要更新搜索参数，请使用 `PUT` 创建新版本的搜索参数。 必须 `SearchParameter ID` 在 `id` 请求正文的元素中以及在调用中包含 `PUT` `PUT` 。

> [!NOTE]
> 如果你不知道搜索参数的 ID，则可以搜索该 ID。 使用 `GET {{FHIR_URL}}/SearchParameter` 将返回所有自定义搜索参数，你可以滚动搜索参数以查找所需的搜索参数。 你还可以按名称限制搜索。 在下面的示例中，可以使用搜索名称 `USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace` 。

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

结果将是已更新的 `SearchParameter` ，版本将会递增。

> [!Warning]
> 更新已在数据库中建立索引的 SearchParameters 时，请务必小心。 更改现有 SearchParameter 的行为可能会影响预期的行为。 建议立即运行索引编制作业。

## <a name="delete-a-search-parameter"></a>删除搜索参数

如果需要删除搜索参数，请使用以下内容：

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> 删除已在数据库中建立索引的 SearchParameters 时，请务必小心。 更改现有 SearchParameter 的行为可能会影响预期的行为。 建议立即运行索引编制作业。

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建搜索参数。 接下来，可以了解如何重新索引 FHIR 服务器。

>[!div class="nextstepaction"]
>[如何运行重新索引作业](how-to-run-a-reindex.md)