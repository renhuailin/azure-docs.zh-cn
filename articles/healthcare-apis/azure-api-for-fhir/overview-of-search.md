---
title: 在 Azure API for FHIR 中进行搜索的概述
description: 本文概述了在 Azure API for FHIR 中实施的 FHIR 搜索
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/17/2021
ms.author: cavoeg
ms.openlocfilehash: f974cb0a5099ce23f9b7ecaf719c09239c21eca8
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824522"
---
# <a name="overview-of-search-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中进行搜索的概述

FHIR 规范定义了搜索 FHIR 资源的基础知识。 本文将指导你完成在 FHIR 中搜索资源的部分关键内容。 有关搜索 FHIR 资源的完整详细信息，请参阅 HL7 FHIR 规范中的[搜索](https://www.hl7.org/fhir/search.html)。 ，我们将在本文中提供语法搜索示例。 每次搜索均面向 FHIR 服务器，通常而言，该服务器的 URL 为 `https://<FHIRSERVERNAME>.azurewebsites.net`。 我们将在示例中使用占位符 {{FHIR_URL}} 表示此 URL。 

FHIR 搜索可以面向特定资源类型、指定的[隔离舱](https://www.hl7.org/fhir/compartmentdefinition.html)或所有资源。 在 FHIR 中执行搜索的最简单方法是使用 `GET` 请求。 例如，如需拉取数据库中的所有患者，可以使用以下请求： 

```rest
GET {{FHIR_URL}}/Patient
```

还可使用 `POST` 进行搜索，该请求在查询字符串过长时十分有用。 若要使用 `POST` 进行搜索，可以表单正文形式提交搜索参数。 这允许使用更长、更复杂的查询参数序列，而这些参数在查询字符串中可能难以查看和理解。

如果搜索请求成功，你将收到 `searchset` 类型的 FHIR 绑定响应。 如果搜索失败，你将在 `OperationOutcome` 中找到错误详细信息，助你了解搜索失败的原因。

以下部分将介绍搜索涉及的各个方面。 查看这些详细信息后，请参阅[示例页](search-samples.md)，该页包含可在 Azure API for FHIR 中进行的搜索示例。

## <a name="search-parameters"></a>搜索参数

执行搜索时，将基于资源的各种属性进行搜索。 这些属性称为搜索参数。 每种资源都有一组定义的搜索参数。 必须在数据库中定义搜索参数并编制索引，方可成功搜索该参数。

每个搜索参数都有一个定义的[数据类型](https://www.hl7.org/fhir/search.html#ptypes)。 下方概述了对各种数据类型的支持：


| 搜索参数类型  | **适用于 FHIR 的 Azure API** | Azure Healthcare APIs 中的 FHIR 服务 | **注释**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  number                    | 是                  | 是                       |
|  date                      | 是                  | 是                       |
|  string                    | 是                  | 是                       |
|  令牌                     | 是                  | 是                       |
|  reference                 | 是                  | 是                       |
|  复合                 | 部分              | 部分                   | 本文稍后将介绍支持的组合类型列表 |
|  quantity                  | 是                  | 是                       |
|  uri                       | 是                  | 是                       |
|  特殊                   | 否                   | 否                        |

### <a name="common-search-parameters"></a>通用搜索参数

存在适用于所有资源的[通用搜索参数](https://www.hl7.org/fhir/search.html#all)。 下面列出了这些通用搜索参数，以及 Azure API for FHIR 中的支持：

| 通用搜索参数 | **适用于 FHIR 的 Azure API** | Azure Healthcare APIs 中的 FHIR 服务 | **注释**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | 是                  | 是                       
| _lastUpdated                | 是                  | 是                       |
| _tag                        | 是                  | 是                       |
| _type                       | 是                  | 是                       |
| _security                   | 是                  | 是                       |
| _profile                    | 是                  | 是                       |
| _has                        | 部分              | 是                       | _has 支持位于 Azure API for FHIR 的 MVP 中和 Cosmos DB 支持的 OSS 版本中。 更多详细信息位于下方的链接部分。 |
| _query                      | 否                   | 否                        |
| _filter                     | 否                   | 否                        |
| _list                       | 否                   | 否                        |
| _text                       | 否                   | 否                        |
| _content                    | 否                   | 否                        |

### <a name="resource-specific-parameters"></a>特定于资源的参数

借助 Azure API for FHIR，我们可为 FHIR 规范定义的几乎所有[特定于资源的搜索参数](https://www.hl7.org/fhir/searchparameter-registry.html)提供支持。 我们不支持的搜索参数只有下方链接所列参数：

* [STU3 不支持的搜索参数](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 不支持的搜索参数](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

还可以在包含以下请求的 [FHIR 功能语句](https://www.hl7.org/fhir/capabilitystatement.html) 中看到搜索参数的当前支持：

```rest
GET {{FHIR_URL}}/metadata
```

若要查看功能语句中的搜索参数，请导航至 `CapabilityStatement.rest.resource.searchParam` 查看每种资源的搜索参数，另请导航至 `CapabilityStatement.rest.searchParam` 查找所有资源的搜索参数。

> [!NOTE]
> Azure API for FHIR 不会自动创建任何非 FHIR 规范定义的搜索参数并编制其索引。 但是，我们的确提供支持，以便定义专属[搜索参数](how-to-do-custom-search.md)。

### <a name="composite-search-parameters"></a>组合搜索参数
组合搜索允许搜索值对。 例如，如要搜索用户身高 60 英寸的身高观测值，则需要确保单一观测分量包含身高代码和值 60。 你不想得到重量 60、身高 48 的观测值，即使观测值有符合值为 60 和高度代码的项，只是在不同的组分部分也不行。 

使用 Azure API for FHIR，我们可以支持以下搜索参数类型对：

* 引用、令牌
* 令牌、日期
* 令牌、数字、数字
* 令牌、数量
* 数字、字符串
* 令牌、令牌

有关详细信息，请参阅 HL7 [组合搜索参数](https://www.hl7.org/fhir/search.html#composite)。 

> [!NOTE]
> 根据 FHIR 规范，组合搜索参数不支持修饰符。

 ### <a name="modifiers--prefixes"></a>修饰符和前缀

[修饰符](https://www.hl7.org/fhir/search.html#modifiers)允许修改搜索参数。 下面概述了所有 FHIR 修饰符以及 Azure API for FHIR 提供的支持。 

| **修饰符** | **适用于 FHIR 的 Azure API** | Azure Healthcare APIs 中的 FHIR 服务 | **注释**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  :missing     | 是                  | 是                       |
|  :exact       | 是                  | 是                       |
|  :contains    | 是                  | 是                       |
|  :text        | 是                  | 是                       |
|  :type (reference) | 是             | 是                       |
|  :not         | 是                  | 是                       |
|  :below (uri) | 是                  | 是                       |
|  :above (uri) | 是                  | 是                       |
|  :in (token)  | 否                   | 否                        |
|  :below (token) | 否                 | 否                        |
|  :above (token) | 否                 | 否                        |
|  :not-in (token) | 否                | 否                        |

对于具有特定顺序的搜索参数（数字、日期和数量），可以使用参数上的[前缀](https://www.hl7.org/fhir/search.html#prefix)来帮助查找匹配项。 Azure API for FHIR 支持所有前缀。

 ### <a name="search-result-parameters"></a>搜索结果参数
为了帮助管理返回的资源，可以在搜索中使用一些搜索结果参数。 有关如何使用每个搜索结果参数的详细信息，请参阅 [HL7](https://www.hl7.org/fhir/search.html#return) 网站。 

| 搜索结果参数  | **适用于 FHIR 的 Azure API** | Azure Healthcare APIs 中的 FHIR 服务 | **注释**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | 是                  | 是                       |
| _count                        | 是                  | 是                       | _count 仅限 1000 个资源。 如果将其设置为高于 1000，则仅返回 1000 个资源，并且捆绑包中会返回一则警告。                               |
| _include                      | 是                  | 是                       | 包含的项仅限 100 个。 PaaS 上的 _include 和 Cosmos DB 上的 OSS 不包括 :iterate 支持 [(#2137)](https://github.com/microsoft/fhir-server/issues/2137)。                               |
| _revinclude                   | 是                  | 是                       |包含的项仅限 100 个。 PaaS 上的 _revinclude 和 Cosmos DB 上的 OSS 不包括 :iterate 支持 [(#2137)](https://github.com/microsoft/fhir-server/issues/2137)。  错误的请求 [#1319](https://github.com/microsoft/fhir-server/issues/1319) 还包含错误的状态代码                            |
| _summary                      | 是             | 是                   |
| _total                        | 部分              | 部分                   | _total=none 和 _total=accurate                               |
| _sort                         | 部分              | 部分                   | 支持 sort=_lastUpdated。 默认情况下，按升序对记录进行排序。 可以使用前缀"-"按降序排序。 对于在 2021 年 4 月 20 日之后创建的 Azure API for FHIR 和 OSS Cosmos DB 数据库，支持对名字、姓氏和临床日期进行排序。          |
| _contained                    | 否                   | 否                        |
| _containedType                | 否                   | 否                        |
| _score                        | 否                   | 否                        |

默认情况下，Azure API for FHIR 设置为宽松处理。 这意味着服务器将忽略任何未知或不受支持的参数。 如想使用严格处理，可以使用首选标头并设置 `handling=strict`。

 ## <a name="chained--reverse-chained-searching"></a>链接搜索和反向链接搜索

[链接搜索](https://www.hl7.org/fhir/search.html#chaining)允许使用搜索参数在其他资源引用的资源上执行搜索。 例如，如果要查找患者名为 Jane 的情况，请使用：

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

同样地，可以执行反向链接搜索。 如此一来便可获取一些资源，你可以在其中对其他引用这些资源的资源指定条件。 有关链接搜索和反向链接搜索的更多示例，请参阅 [FHIR 搜索示例](search-samples.md)页。 

> [!NOTE]
> Cosmos DB 支持的 Azure API for FHIR 和开源中有一个限制，即链接搜索和反向链接搜索所需的每个子查询仅返回 100 个项目。 如果找到的项目超过 100 个，则会收到以下错误消息：“链接表达式中的子查询返回的结果不得超过 100 个，请使用更严格的条件”。 若要成功查询，则需要更具体地了解所需内容。

## <a name="pagination"></a>分页

如上所述，搜索结果将分页捆绑包。 默认情况下，每页的搜索结果为 10 个，但此值可以通过指定 `_count` 增大（或减小）。 捆绑包中存在一个包含当前搜索结果的自链接。 如有其他匹配项，该捆绑包将包含“下一页”链接。 你可以继续使用“下一页”链接来获取后续的结果页面。 `_count` 仅限 1000 项或更少。 

目前，Azure API for FHIR 仅支持捆绑包中有“下一页”链接，不支持“第一页”、“最后一页”或“上一页”链接。

## <a name="next-steps"></a>后续步骤

现在，你已了解有关搜索的基本知识，请参阅搜索示例页，详细了解如何使用不同的搜索参数、修饰符和其他 FHIR 搜索方案进行搜索。

>[!div class="nextstepaction"]
>[FHIR 搜索示例](search-samples.md)
