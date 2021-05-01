---
title: Azure API for FHIR 中的搜索概述
description: 本文介绍 Azure API for FHIR 中实现的 FHIR 搜索的概述
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/21/2021
ms.author: cavoeg
ms.openlocfilehash: dce155da36579f94012a8dc9f6275a43d99519d3
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108322526"
---
# <a name="overview-of-fhir-search"></a>FHIR 搜索概述

FHIR 规范定义了 FHIR 资源的搜索基础。 本文将指导你完成在 FHIR 中搜索资源的一些关键方面。 有关搜索 FHIR 资源的完整详细信息，请参阅 HL7 FHIR 规范中的 [搜索](https://www.hl7.org/fhir/search.html) 。

FHIR 搜索可以针对特定的资源类型、指定的 [隔离舱](https://www.hl7.org/fhir/compartmentdefinition.html)或所有资源。 在 FHIR 中执行搜索的最简单方法是使用 `GET` 请求。 例如，如果要请求数据库中的所有患者，则可以使用以下请求： 

`GET {{FHIR URL}}/Patient`

你还可以使用 `POST` 进行搜索，这在查询字符串过长时非常有用。 若要使用进行搜索 `POST` ，可以提交搜索参数作为窗体正文。 这允许更长、更复杂的查询参数，这些参数在查询字符串中可能难以查看和理解。

如果搜索请求成功，将收到类型为的 FHIR 绑定响应 `searchset` 。 如果搜索失败，你可以在中找到这些详细信息， `OperationOutcome` 以帮助你了解搜索失败的原因。

在以下部分中，我们将介绍搜索涉及的各个方面。 查看这些详细信息后，请参阅 **示例页** ，其中包含可在用于 FHIR 的 Azure API 中进行搜索的示例。

## <a name="search-parameters"></a>搜索参数

执行搜索时，请考虑根据资源的各种属性进行搜索。  这些属性称为搜索参数。 每个资源都有一组已定义的搜索参数。 必须在数据库中定义和索引搜索参数，才能对其成功搜索。

每个搜索参数都有一个定义的数据类型。 适用于 FHIR 的 Azure API 支持 **除 type 之外** 的所有 [数据类型](https://www.hl7.org/fhir/search.html#ptypes)：


| **搜索参数类型**  | **支持-PaaS** | **支持-OSS (SQL)** | **支持-OSS (Cosmos DB)** |
| -------------------------  | -------------------- | ------------------------- | ------------------------------- |
|  number                    | 是                  | 是                       | 是                             |
|  date                      | 是                  | 是                       | 是                             |
|  string                    | 是                  | 是                       | 是                             |
|  令牌                     | 是                  | 是                       | 是                             |
|  reference                 | 是                  | 是                       | 是                             |
|  复合                 | 是                  | 是                       | 是                             |
|  quantity                  | 是                  | 是                       | 是                             |
|  uri                       | 是                  | 是                       | 是                             |
|  特殊                   | 否                   | 否                        | 否                              |

### <a name="common-search-parameters"></a>常用搜索参数

存在适用于所有资源的 [常用搜索参数](https://www.hl7.org/fhir/search.html#all) 。 下面列出了这些内容以及其在用于 FHIR 的 Azure API 中的支持：

| **常用搜索参数** | **支持-PaaS** | **支持-OSS (SQL)** | **支持-OSS (Cosmos DB)** | **注释**                    |
| --------------------------  | -------------------- | ------------------------- | ------------------------------- | ------------------------------ |
| _id                         | 是                  | 是                       | 是                             |                                |
| _lastUpdated                | 是                  | 是                       | 是                             |                                |
| _tag                        | 是                  | 是                       | 是                             |                                | 
| _type                       | 是                  | 是                       | 是                             |                                |
| _security                   | 是                  | 是                       | 是                             |                                |
| _profile                    | 是                  | 是                       | 是                             | **注意**：如果在2021年2月20日之前创建了 R4 数据库，则需要运行索引编制作业来启用 **_profile**。                                                      |
| _text                       | 否                   | 否                        | 否                              |                                |
| _content                    | 否                   | 否                        | 否                              |                                |
| _has                        | 部分                   | 部分                        | 是                              |                                |
| _query                      | 否                   | 否                        | 否                              |                                |
| _filter                     | 否                   | 否                        | 否                              |                                |
| _list                       | 否                   | 否                        | 否                              |                                |

### <a name="resource-specific-parameters"></a>资源特定参数

使用用于 FHIR 的 Azure API，我们支持 FHIR 规范定义的几乎所有资源特定的搜索参数。 下面的链接中提供了我们不支持的唯一搜索参数：

* [STU3 不支持的搜索参数](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 不支持的搜索参数](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

你还可以在 [FHIR 功能语句](https://www.hl7.org/fhir/capabilitystatement.html) 中查看搜索参数的当前支持，并提供以下请求：

`GET {{FHIR URL}}/metadata`

若要查看功能语句中的搜索参数，请导航到 `CapabilityStatement.rest.resource.searchParam` 以查看每个资源的搜索参数，并 `CapabilityStatement.rest.searchParam` 查找所有资源的搜索参数。

> [!NOTE]
> Azure API for FHIR 不会自动创建或索引任何不是由 FHIR 规范定义的支持搜索参数。 但是，我们为你提供了对的支持，以定义你自己的搜索参数。

### <a name="composite-search-parameters"></a>复合搜索参数

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
|  ：包含    | 是                  | 是                       | 是                             | 
|  ：文本        | 是                  | 是                       | 是                             | 
|  ：键入 (引用)  | 是             | 是                       | 是                             | 
|  ： not         | 是                  | 是                       | 是                             | 
|  ：以下 (uri)  | 是                  | 是                       | 是                             |  
|  ：上面 (uri)  | 否                   | 是                        | 否                              | 
|  ： in (令牌)   | 否                   | 是                        | 否                              | 
|  ：以下 (令牌)  | 否                 | 是                        | 否                              | 
|  ：上面 (标记)  | 否                 | 是                        | 否                              | 
|  ：不 (标记)  | 否                | 是                        | 否                              | 

对于具有特定顺序的搜索参数 (的数字、日期和数量) ，可以对参数使用 [前缀](https://www.hl7.org/fhir/search.html#prefix) 以帮助查找匹配项。 适用于 FHIR 的 Azure API 支持所有前缀。

 ### <a name="search-result-parameters"></a>搜索结果参数



为了帮助管理返回的资源，还可以在搜索中使用其他搜索结果参数。 有关如何使用每个搜索结果参数的详细信息，请参阅 [HL7](https://www.hl7.org/fhir/search.html#return) 网站。 

| **搜索结果参数**  | **支持-PaaS** | **支持-OSS (SQL)** | **支持-OSS (Cosmos DB)** | **注释**                 |
| ----------------------------  | -------------------- | ------------------------- | ------------------------------- | -----------------------------|
| _elements                     | 是                  | 是                       | 是                             |  问题 [1256](https://github.com/microsoft/fhir-server/issues/1256)                              |
| _count                        | 是                  | 是                       | 是                             | _count 限制为1000个资源。 如果将其设置为高于1000，则仅返回1000，并在捆绑包中返回警告。                               |
| _include                      | 是                  | 是                       | 是                             | 包含的项限制为100。 PaaS 上的 _include 和 Cosmos DB 上的 OSS 不包括：循环访问支持 [ (#1313) ](https://github.com/microsoft/fhir-server/issues/1313)。                               |
| _revinclude                   | 是                  | 是                       | 是                             |  包含的项限制为100。 PaaS 上的 _revinclude 和 Cosmos DB 上的 OSS 不包括：循环访问支持 [ (#1313) ](https://github.com/microsoft/fhir-server/issues/1313)。  问题 [#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | 是             | 是                   | 是                        |                               |
| _total                        | 部分              | 部分                   | 部分                         | _total = none 且 _total = 准确                               |
| _sort                         | 部分              | 部分                   | 部分                         | 支持 sort = _lastUpdated                               |
| _contained                    | 否                   | 否                        | 否                              |                                |
| _containedType                | 否                   | 否                        | 否                              |                                |
| _score                        | 否                   | 否                        | 否                              |                                |

默认情况下，用于 FHIR 的 Azure API 设置为宽松处理。 这意味着服务器将忽略任何未知或不支持的参数。 如果要使用严格处理，则可以使用 **首选** 的标头和集 `handling=strict` 。


 ## <a name="chained--reverse-chained-searching"></a>链接 & 反向链接搜索

使用 [链式搜索](https://www.hl7.org/fhir/search.html#chaining) ，可以使用搜索参数搜索其他资源引用的资源。 例如，如果要查找患者名称为 Jane 的情况，请使用：

`GET {{FHIR URL}}/Encounter?subject:Patient.name=Jane`

同样，可以执行反向链接搜索。 这样，你便可以获取一些资源，你可以在其中对其他引用这些资源的资源指定条件。 有关链式和反向链接的更多示例，请参阅 [FHIR 搜索示例](search-samples.md) 页。 

**请注意**：在用于 FHIR 的 Azure API 和 Cosmos DB 的开源中，有一个限制：链式和反向链接搜索所需的每个子查询仅返回100个项目。 如果找到的项数超过100，则将收到以下错误消息：

"链接表达式中的子查询返回的结果不能超过100，请使用更具选择性的条件。" 

若要成功查询，需要更具体地了解所需内容。

## <a name="pagination"></a>分页

如上所述，搜索结果将是分页绑定。 默认情况下，搜索将每页返回10个结果，但这可以通过指定 (或减小) 增加 `_count` 。 在绑定中，会有一个包含搜索当前结果的自链接。 如果有其他匹配项，则该捆绑包将包含 "下一步" 链接。 你可以继续使用 "下一步" 链接来获取后续的结果页面。 

目前，适用于 FHIR 的 Azure API 仅支持捆绑包中的下一个链接，并且它不支持第一个、最后一个或以前的链接。

## <a name="next-steps"></a>后续步骤

现在，你已了解有关搜索的基本知识，请参阅搜索示例页，详细了解如何使用不同的搜索参数、修饰符和其他 FHIR 搜索工具进行搜索。

>[!div class="nextstepaction"]
>[FHIR 搜索示例](search-samples.md)
