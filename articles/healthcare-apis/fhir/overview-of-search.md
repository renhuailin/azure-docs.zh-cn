---
title: 搜索概述Azure API for FHIR
description: 本文概述了在 Azure API for FHIR 中实现的 FHIR 搜索
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/17/2021
ms.author: cavoeg
ms.openlocfilehash: 93468d2be1c9ab8fbdbce1e82a58f04f0676f260
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110071175"
---
# <a name="overview-of-fhir-search"></a>FHIR 搜索概述

FHIR 规范定义了搜索 FHIR 资源的基础。 本文将指导你完成在 FHIR 中搜索资源的一些关键方面。 有关搜索 FHIR 资源的完整详细信息，请参阅[](https://www.hl7.org/fhir/search.html)HL7 FHIR 规范中的搜索。 在整篇文章中，我们将提供搜索语法的示例。 每次搜索都将针对 FHIR 服务器，该服务器通常具有 `https://<FHIRSERVERNAME>.azurewebsites.net` URL。 在示例中，我们将为此 URL 使用占位符 {{FHIR_URL}}。 

FHIR 搜索可以针对特定资源类型、指定的隔离 [舱](https://www.hl7.org/fhir/compartmentdefinition.html)或所有资源。 在 FHIR 中执行搜索的最简单方法就是使用 `GET` 请求。 例如，如果要拉取数据库中的所有患者，可以使用以下请求： 

```rest
GET {{FHIR_URL}}/Patient
```

还可使用 进行搜索 `POST` ，如果查询字符串太长，这很有用。 若要使用 `POST` 进行搜索，可以将搜索参数作为表单正文提交。 这允许使用更长、更复杂的查询参数系列，这些参数在查询字符串中可能难以查看和理解。

如果搜索请求成功，将收到类型为 的 FHIR 捆绑包响应 `searchset` 。 如果搜索失败，你将在 中查找错误详细信息 `OperationOutcome` ，以帮助你了解搜索失败的原因。

以下部分将介绍搜索涉及的各个方面。 查看这些详细信息后，请参阅示例页，该页面包含[](search-samples.md)可在其中搜索的示例Azure API for FHIR。

## <a name="search-parameters"></a>搜索参数

执行搜索时，将基于资源的各种属性进行搜索。 这些属性称为搜索参数。 每个资源都有一组已定义的搜索参数。 必须在数据库中定义和索引搜索参数，才能对其成功搜索。

每个搜索参数都有一个定义的 [数据类型](https://www.hl7.org/fhir/search.html#ptypes)。 下面概述了对各种数据类型的支持：


| **搜索参数类型**  | **支持-PaaS** | **支持-OSS (SQL)** | **支持-OSS (Cosmos DB)** | **注释**|
| -------------------------  | -------------------- | ------------------------- | ------------------------------- |------------|
|  number                    | 是                  | 是                       | 是                             |
|  date                      | 是                  | 是                       | 是                             |
|  字符串                    | 是                  | 是                       | 是                             |
|  令牌                     | 是                  | 是                       | 是                             |
|  reference                 | 是                  | 是                       | 是                             |
|  复合                 | 部分              | 部分                   | 部分                         | 本文稍后将介绍受支持的复合类型列表 |
|  quantity                  | 是                  | 是                       | 是                             |
|  uri                       | 是                  | 是                       | 是                             |
|  特殊                   | 否                   | 否                        | 否                              |

### <a name="common-search-parameters"></a>常用搜索参数

存在适用于所有资源的 [常用搜索参数](https://www.hl7.org/fhir/search.html#all) 。 下面列出了这些内容以及其在用于 FHIR 的 Azure API 中的支持：

| **常用搜索参数** | **支持-PaaS** | **支持-OSS (SQL)** | **支持-OSS (Cosmos DB)** | **注释** |
| --------------------------  | -------------------- | ------------------------- | ------------------------------- | ------------|
| _id                         | 是                  | 是                       | 是                             |             |
| _lastUpdated                | 是                  | 是                       | 是                             |             |
| _tag                        | 是                  | 是                       | 是                             |             | 
| _type                       | 是                  | 是                       | 是                             |             |
| _security                   | 是                  | 是                       | 是                             |             |
| _profile                    | 是                  | 是                       | 是                             | 如果在 2021 年 2 月 20 日之前创建了 R4 数据库 [](how-to-run-a-reindex.md)，则需要运行重新索引作业以启用 **_profile。**|
| _has                        | 部分              | 是                       | 部分                         | 对 _has 的支持位于 Azure API for FHIR 和 COSMOS DB 支持的 OSS 版本中。 下面的链接部分提供了更多详细信息。 |
| _query                      | 否                   | 否                        | 否                              |             |
| _filter                     | 否                   | 否                        | 否                              |             |
| _list                       | 否                   | 否                        | 否                              |             |
| _text                       | 否                   | 否                        | 否                              |             |
| _content                    | 否                   | 否                        | 否                              |             |

### <a name="resource-specific-parameters"></a>特定于资源的参数

借助Azure API for FHIR，我们支持 FHIR 规范定义的几乎所有[](https://www.hl7.org/fhir/searchparameter-registry.html)特定于资源的搜索参数。 以下链接中提供了我们不支持的唯一搜索参数：

* [STU3 不支持的搜索参数](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 不支持的搜索参数](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

还可通过以下请求在 [FHIR](https://www.hl7.org/fhir/capabilitystatement.html) 功能语句中查看当前对搜索参数的支持：

```rest
GET {{FHIR_URL}}/metadata
```

若要在功能语句中查看搜索参数，请导航到 以查看每个资源的搜索参数，并查找 `CapabilityStatement.rest.resource.searchParam` `CapabilityStatement.rest.searchParam` 所有资源的搜索参数。

> [!NOTE]
> 此Azure API for FHIR不会自动创建 FHIR 规范未定义的任何搜索参数或为这些参数编制索引。 但是，我们支持你定义自己的 [搜索参数](how-to-do-custom-search.md)。

### <a name="composite-search-parameters"></a>复合搜索参数
复合搜索允许搜索值对。 例如，如果搜索的高度观察值的用户为60英寸，则您需要确保观察的单个组件包含高度的代码 **和** 值60。 您不希望获得一个观察值，其中的权重为60，高度为48，即使观察到的条目的值为60和代码的高度，在不同的组件部分中也是如此。 

借助适用于 FHIR 的 Azure API，我们支持以下搜索参数类型对：

* 引用、标记
* 标记，日期
* Token、Number、Number
* 令牌，数量
* Token、String
* 令牌，令牌

有关详细信息，请参阅 HL7 [复合搜索参数](https://www.hl7.org/fhir/search.html#composite)。 

> [!NOTE]
> 复合搜索参数不支持每个 FHIR 规范的修饰符。

 ### <a name="modifiers--prefixes"></a>修饰符 & 前缀

[修饰符](https://www.hl7.org/fhir/search.html#modifiers) 允许您修改搜索参数。 下面概述了所有 FHIR 修饰符以及 Azure API for FHIR 中的支持。 

| **修饰符** | **支持-PaaS** | **支持-OSS (SQL)** | **支持-OSS (Cosmos DB)** |
| ------------- | -------------------- | ------------------------- | ------------------------------- |
|  ：缺少     | 是                  | 是                       | 是                             |
|  ：精确       | 是                  | 是                       | 是                             | 
|  ：contains    | 是                  | 是                       | 是                             | 
|  ：text        | 是                  | 是                       | 是                             | 
|  ：type (reference)  | 是             | 是                       | 是                             | 
|  ：not         | 是                  | 是                       | 是                             | 
|  ： (uri)  | 是                  | 是                       | 是                             |  
|  ： (uri)  | 是                  | 是                       | 是                             | 
|  ：in (token)   | 否                   | 否                        | 否                              | 
|  ： (令牌)  | 否                 | 否                        | 否                              | 
|  ： (标记)  | 否                 | 否                        | 否                              | 
|  ：not-in (token)  | 否                | 否                        | 否                              | 

对于具有特定订单的搜索参数 (数字、日期和数量) ，可以使用 参数上的前缀来帮助查找匹配项。 [](https://www.hl7.org/fhir/search.html#prefix) 该Azure API for FHIR支持所有前缀。

 ### <a name="search-result-parameters"></a>搜索结果参数
为了帮助管理返回的资源，可以在搜索中使用搜索结果参数。 有关如何使用每个搜索结果参数的详细信息，请参阅 [HL7](https://www.hl7.org/fhir/search.html#return) 网站。 

| **搜索结果参数**  | **支持 - PaaS** | **支持 - OSS (SQL)** | **支持 - OSS (Cosmos DB)** | **注释**                 |
| ----------------------------  | -------------------- | ------------------------- | ------------------------------- | -----------------------------|
| _elements                     | 是                  | 是                       | 是                             |                                |
| _count                        | 是                  | 是                       | 是                             | _count 限制为1000个资源。 如果将其设置为高于1000，则仅返回1000，并在捆绑包中返回警告。                               |
| _include                      | 是                  | 是                       | 是                             | 包含的项限制为100。 PaaS 上的 _include，Cosmos DB 上的 OSS 不包括：循环访问支持 [ (#1313) ](https://github.com/microsoft/fhir-server/issues/1313)。                               |
| _revinclude                   | 是                  | 是                       | 是                             |  包含的项限制为100。 PaaS 上的 _revinclude，Cosmos DB 上的 OSS 不包括：循环访问支持 [ (#1313) ](https://github.com/microsoft/fhir-server/issues/1313)。  问题 [#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | 是             | 是                   | 是                        |                               |
| _total                        | 部分              | 部分                   | 部分                         | _total = none 且 _total = 准确                               |
| _sort                         | 部分              | 部分                   | 部分                         | 支持 sort = _lastUpdated。 对于在4月20日之后创建的 FHIR 和 OSS Cosmos DB 数据库，在 "名字"、"姓氏" 和 "临床日期" 上还支持2021排序。                               |
| _contained                    | 否                   | 否                        | 否                              |                                |
| _containedType                | 否                   | 否                        | 否                              |                                |
| _score                        | 否                   | 否                        | 否                              |                                |

默认情况下，用于 FHIR 的 Azure API 设置为宽松处理。 这意味着服务器将忽略任何未知或不支持的参数。 如果要使用严格处理，可以使用 **Prefer 标头** 并设置 `handling=strict` 。

 ## <a name="chained--reverse-chained-searching"></a>链接&反向链接搜索

[链接的搜索](https://www.hl7.org/fhir/search.html#chaining)允许对另一个资源引用的资源使用搜索参数进行搜索。 例如，如果要查找患者姓名为 Jane 的遇到情况，请使用：

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

同样，可以执行反向链接搜索。 这样，你可获取在引用这些资源的其他资源上指定条件的资源。 有关链接搜索和反向链接搜索的更多示例，请参阅 [FHIR 搜索示例](search-samples.md) 页。 

> [!NOTE]
> 在 Azure API for FHIR 和 Cosmos DB 支持的开放源代码中，存在一个限制，即链接搜索和反向链接搜索所需的每个子查询仅返回 100 个项。 如果找到的项目超过 100 个，将收到以下错误消息："链接表达式中的子查询不能返回超过 100 个结果，请使用更具选择性的条件。" 若要获取成功的查询，需要更具体地了解要查找的内容。

## <a name="pagination"></a>分页

如上所述，搜索的结果将是分页捆绑包。 默认情况下，搜索将每页返回 10 个结果，但可通过指定 (增加) 减少 `_count` 。 在捆绑包中，有一个包含当前搜索结果的自链接。 如果有其他匹配项，则捆绑包将包含下一个链接。 可以继续使用下一个链接获取后续结果页。 `_count` 限制为 1000 项或更少。 

目前，Azure API for FHIR仅支持捆绑中的下一个链接，并且不支持第一个、最后一个或以前的链接。

## <a name="next-steps"></a>后续步骤

了解搜索基础知识后，请参阅搜索示例页，详细了解如何使用不同的搜索参数、修饰符和其他 FHIR 搜索方案进行搜索。

>[!div class="nextstepaction"]
>[FHIR 搜索示例](search-samples.md)
