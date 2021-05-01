---
title: 如何在 Azure API for FHIR 中执行自定义搜索
description: 本文介绍如何定义要在数据库中使用的自定义搜索参数。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: 1482bd7f054c75c4f26b77907d50a6471c389d68
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108322506"
---
# <a name="defining-custom-search-parameters"></a>定义自定义搜索参数

FHIR 规范定义了)  (资源的所有资源和搜索参数的一组搜索参数。 但是，在某些情况下，你可能希望在不是由规范定义为标准搜索参数的资源中搜索字段。 本文介绍如何定义要在 FHIR 服务器中使用的自己的 [搜索参数](https://www.hl7.org/fhir/searchparameter.html) 。

> [!NOTE]
> 每次创建、更新或删除搜索参数时，都需要运行一个重新 [索引作业](how-to-run-a-reindex.md) 来启用数据库中的更改。

> [!Warning]
> 如果更新或删除搜索参数，请确保立即运行 [索引编制作业](how-to-run-a-reindex.md)。 由于需要为更改编制索引，因此数据库可能处于异常状态，其中的更新或删除搜索参数仍处于活动状态。 

## <a name="create-new-search-parameter"></a>创建新的搜索参数

若要创建新的搜索参数，请 `POST` 向该数据库创建一个新的搜索参数。 下面的代码示例演示如何向患者资源添加 [US 核心争用搜索参数](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) 。

```json
POST {fhirurl}/SearchParameter
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
> 在将新的搜索参数发布到 FHIR 服务器并对其重新编制索引后，该参数将在您的功能语句中出现。 在运行 [索引编制作业](how-to-run-a-reindex.md)之前，它将不可用。 这是立即判断是否支持某个搜索参数的唯一方法。 如果可以通过搜索搜索参数找到搜索参数，但在功能声明中看不到，请运行索引编制作业。 在触发索引索引操作之前，您可以发布多个搜索参数。

重要字段说明：

* **url**：这是描述搜索参数的唯一键。 许多组织（如 HL7）对其定义的 Url 使用标准格式，如上文所述，在美国核心种族搜索参数中。

* **代码**：此处的值是搜索时使用的值。 对于上述示例，您将搜索 `GET {FHIR URL}/Patient?race=2028-9` 以获取所有亚洲患者。 此值对于其适用) 资源 (必须是唯一的。

* **基本**：描述搜索参数适用)  (的资源。 如果它适用于所有资源，则可以只使用资源;否则，可以列出所有相关资源。
 
* **类型**：描述搜索参数的数据类型。

* **表达式**：描述搜索参数时，必须包括表达式，即使规范在技术上不是规范所必需的。 这是因为，你需要表达式或 xpath 语法，Azure API for FHIR 会立即忽略 xpath 语法。 这说明了如何查找搜索的值。 

> [!NOTE]
> "Type" 受限于 Azure API for FHIR 的支持。 这意味着，不能定义类型为 "特殊" 或 "定义 [复合搜索参数](overview-of-search.md) " 的搜索参数，除非它是我们支持的类型。

添加搜索参数后，运行或计划重新索引作业，以便可以在 FHIR 服务器中使用搜索参数。

## <a name="update-a-search-parameter"></a>更新搜索参数

若要更新搜索参数，请使用 `PUT` 创建新版本的搜索参数。

`PUT {fhirurl}/SearchParameter/{SearchParameter ID}`

必须 `SearchParameter ID` 在请求正文的 ID 字段中和在调用中包含 `PUT` `PUT` 。

> [!NOTE]
> 如果你不知道搜索参数的 ID，则可以搜索该 ID。 使用 `GET {fhirurl}/SearchParameter` 将返回所有自定义搜索参数，你可以滚动搜索参数以查找所需的搜索参数。 你还可以按名称限制搜索。 在下面的示例中，可以使用搜索名称 `USCoreRace: GET {fhirurl}/SearchParameter?name=USCoreRace` 。

```json
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
> 更新已在数据库中建立索引的 SearchParameters 时，请务必小心。 更改现有 SearchParameter 的行为可能会影响预期的行为。 

## <a name="delete-a-search-parameter"></a>删除搜索参数

如果需要删除搜索参数，请使用以下内容：

`Delete {fhirurl}/SearchParameter/{SearchParameter ID}`

> [!Warning]
> 删除已在数据库中建立索引的 SearchParameters 时，请务必小心。 更改现有 SearchParameter 的行为可能会影响预期的行为。

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建搜索参数。 若要了解如何重新索引作业，请参阅

>[!div class="nextstepaction"]
>[如何运行索引编制作业](how-to-run-a-reindex.md)