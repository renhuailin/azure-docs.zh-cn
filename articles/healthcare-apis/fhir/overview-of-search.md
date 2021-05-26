---
title: Azure API for FHIR 中的搜索概述
description: 本文介绍 Azure API for FHIR 中实现的 FHIR 搜索的概述
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/17/2021
ms.author: cavoeg
ms.openlocfilehash: 2d1bccbd40689a06a13b7a2283ad02a0ac43ce84
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110477820"
---
# <a name="overview-of-fhir-search"></a>FHIR 搜索概述

FHIR 规范定义了 FHIR 资源的搜索基础。 本文将指导你完成在 FHIR 中搜索资源的一些关键方面。 有关搜索 FHIR 资源的完整详细信息，请参阅 HL7 FHIR 规范中的 [搜索](https://www.hl7.org/fhir/search.html) 。 在本文中，我们将为搜索语法举例说明。 每个搜索都将针对你的 FHIR 服务器，通常具有的 URL `https://<FHIRSERVERNAME>.azurewebsites.net` 。 在示例中，我们将对此 URL 使用占位符 {{FHIR_URL}}。 

FHIR 搜索可以针对特定的资源类型、指定的 [隔离舱](https://www.hl7.org/fhir/compartmentdefinition.html)或所有资源。 在 FHIR 中执行搜索的最简单方法是使用 `GET` 请求。 例如，如果要请求数据库中的所有患者，则可以使用以下请求： 

```rest
GET {{FHIR_URL}}/Patient
```

你还可以使用 `POST` 进行搜索，这在查询字符串过长时非常有用。 若要使用进行搜索 `POST` ，可以提交搜索参数作为窗体正文。 这允许更长、更复杂的查询参数，这些参数在查询字符串中可能难以查看和理解。

如果搜索请求成功，将收到类型为的 FHIR 绑定响应 `searchset` 。 如果搜索失败，你将在中找到错误详细信息， `OperationOutcome` 以帮助你了解搜索失败的原因。

在以下部分中，我们将介绍搜索涉及的各个方面。 查看这些详细信息后，请参阅 [示例页](search-samples.md) ，其中包含可在用于 FHIR 的 Azure API 中进行搜索的示例。

## <a name="search-parameters"></a>搜索参数

执行搜索时，将基于资源的各种属性进行搜索。 这些属性称为搜索参数。 每个资源都有一组定义的搜索参数。 必须在数据库中定义搜索参数并编制索引，以成功搜索该参数。

每个搜索参数都有一个 [定义的数据类型](https://www.hl7.org/fhir/search.html#ptypes)。 下面概述了对各种数据类型的支持：


| **搜索参数类型**  | **支持 - PaaS** | **支持 - OSS (SQL)** | **支持 - OSS (Cosmos DB)** | **注释**|
| -------------------------  | -------------------- | ------------------------- | ------------------------------- |------------|
|  number                    | 是                  | 是                       | 是                             |
|  date                      | 是                  | 是                       | 是                             |
|  字符串                    | 是                  | 是                       | 是                             |
|  令牌                     | 是                  | 是                       | 是                             |
|  reference                 | 是                  | 是                       | 是                             |
|  复合                 | 部分              | 部分                   | 部分                         | 本文稍后将介绍支持的复合类型列表 |
|  quantity                  | 是                  | 是                       | 是                             |
|  uri                       | 是                  | 是                       | 是                             |
|  特殊                   | 否                   | 否                        | 否                              |

### <a name="common-search-parameters"></a>常用搜索参数

有 [一些适用于所有](https://www.hl7.org/fhir/search.html#all) 资源的常用搜索参数。 下面列出了这些示例及其在Azure API for FHIR：

| **常用搜索参数** | **支持 - PaaS** | **支持 - OSS (SQL)** | **支持 - OSS (Cosmos DB)** | **注释** |
| --------------------------  | -------------------- | ------------------------- | ------------------------------- | ------------|
| _id                         | 是                  | 是                       | 是                             |             |
| _lastUpdated                | 是                  | 是                       | 是                             |             |
| _tag                        | 是                  | 是                       | 是                             |             | 
| _type                       | 是                  | 是                       | 是                             |             |
| _security                   | 是                  | 是                       | 是                             |             |
| _profile                    | 是                  | 是                       | 是                             | 如果在2021年2月20日之前创建了 R4 数据库，则需要运行 [索引编制作业](how-to-run-a-reindex.md) 来启用 **_profile**。|
| _has                        | 部分              | 是                       | 部分                         | 支持 _has 在用于 FHIR 的 Azure API 和 Cosmos DB 支持的 OSS 版本中使用 MVP。 下面的链接部分提供了更多详细信息。 |
| _query                      | 否                   | 否                        | 否                              |             |
| _filter                     | 否                   | 否                        | 否                              |             |
| _list                       | 否                   | 否                        | 否                              |             |
| _text                       | 否                   | 否                        | 否                              |             |
| _content                    | 否                   | 否                        | 否                              |             |

### <a name="resource-specific-parameters"></a>特定于资源的参数

使用用于 FHIR 的 Azure API，我们支持几乎所有由 FHIR 规范定义的 [特定于资源的搜索参数](https://www.hl7.org/fhir/searchparameter-registry.html) 。 下面的链接中提供了我们不支持的唯一搜索参数：

* [STU3 不支持的搜索参数](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 不支持的搜索参数](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

你还可以在 [FHIR 功能语句](https://www.hl7.org/fhir/capabilitystatement.html) 中查看搜索参数的当前支持，并提供以下请求：

```rest
GET {{FHIR_URL}}/metadata
```

若要查看功能语句中的搜索参数，请导航到 `CapabilityStatement.rest.resource.searchParam` 以查看每个资源的搜索参数，并 `CapabilityStatement.rest.searchParam` 查找所有资源的搜索参数。

> [!NOTE]
> Azure API for FHIR 不会自动创建或索引任何未由 FHIR 规范定义的搜索参数。 但是，我们为你提供了对的支持，以定义你自己的 [搜索参数](how-to-do-custom-search.md)。

### <a name="composite-search-parameters"></a>复合搜索参数
使用复合搜索可以针对值对进行搜索。 例如，如果要搜索人员为 60 英寸的高度观察值，需要确保观察值的单个分量包含高度代码和值 60。  你不希望获得存储权重为 60 且高度为 48 的观察值，即使观察结果将具有限定值 60 的条目和高度代码，只是在不同的组件部分中。 

借助Azure API for FHIR，我们支持以下搜索参数类型配对：

* 引用、令牌
* 令牌、日期
* 标记、数字、数字
* 令牌、数量
* 令牌、字符串
* 令牌、令牌

有关详细信息，请参阅 HL7 [复合搜索参数](https://www.hl7.org/fhir/search.html#composite)。 

> [!NOTE]
> 复合搜索参数不支持 FHIR 规范中的修饰符。

 ### <a name="modifiers--prefixes"></a>修饰&前缀

[修饰](https://www.hl7.org/fhir/search.html#modifiers) 符允许修改搜索参数。 下面是所有 FHIR 修饰符的概述，以及该Azure API for FHIR。 

| **修饰符** | **支持 - PaaS** | **支持 - OSS (SQL)** | **支持 - OSS (Cosmos DB)** |
| ------------- | -------------------- | ------------------------- | ------------------------------- |
|  ：missing     | 是                  | 是                       | 是                             |
|  ：exact       | 是                  | 是                       | 是                             | 
|  ：包含    | 是                  | 是                       | 是                             | 
|  ：文本        | 是                  | 是                       | 是                             | 
|  ：键入 (引用)  | 是             | 是                       | 是                             | 
|  ： not         | 是                  | 是                       | 是                             | 
|  ：以下 (uri)  | 是                  | 是                       | 是                             |  
|  ：上面 (uri)  | 是                  | 是                       | 是                             | 
|  ： in (令牌)   | 否                   | 否                        | 否                              | 
|  ：以下 (令牌)  | 否                 | 否                        | 否                              | 
|  ：上面 (标记)  | 否                 | 否                        | 否                              | 
|  ：不 (标记)  | 否                | 否                        | 否                              | 

对于具有特定顺序的搜索参数 (的数字、日期和数量) ，可以对参数使用 [前缀](https://www.hl7.org/fhir/search.html#prefix) 以帮助查找匹配项。 适用于 FHIR 的 Azure API 支持所有前缀。

 ### <a name="search-result-parameters"></a>搜索结果参数
为了帮助管理返回的资源，有一些搜索结果参数可以在搜索中使用。 有关如何使用每个搜索结果参数的详细信息，请参阅 [HL7](https://www.hl7.org/fhir/search.html#return) 网站。 

| **搜索结果参数**  | **支持-PaaS** | **支持-OSS (SQL)** | **支持-OSS (Cosmos DB)** | **注释**                 |
| ----------------------------  | -------------------- | ------------------------- | ------------------------------- | -----------------------------|
| _elements                     | 是                  | 是                       | 是                             |                                |
| _count                        | 是                  | 是                       | 是                             | _count限制为 1000 个资源。 如果设置为大于 1000，则仅返回 1000，捆绑包中将返回警告。                               |
| _include                      | 是                  | 是                       | 是                             | 包含的项限制为 100。 _include PaaS 和 OSS 上的Cosmos DB不包括 ：iterate support [ (#1313) 。 ](https://github.com/microsoft/fhir-server/issues/1313)                               |
| _revinclude                   | 是                  | 是                       | 是                             |  包含的项限制为 100。 _revinclude PaaS 和 OSS 上的Cosmos DB不包括 ：iterate support [ (#1313) 。 ](https://github.com/microsoft/fhir-server/issues/1313)  问题 [#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | 是             | 是                   | 是                        |                               |
| _total                        | 部分              | 部分                   | 部分                         | _total=none，_total=准确                               |
| _sort                         | 部分              | 部分                   | 部分                         | sort=_lastUpdated支持。 对于Azure API for FHIR 2021 Cosmos DB 2021 年 4 月 20 日之后创建的数据库和 OSS 数据库，还支持对名字、姓氏和临床日期进行排序。                               |
| _contained                    | 否                   | 否                        | 否                              |                                |
| _containedType                | 否                   | 否                        | 否                              |                                |
| _score                        | 否                   | 否                        | 否                              |                                |

默认情况下，Azure API for FHIR设置为宽松处理。 这意味着服务器将忽略任何未知或不受支持的参数。 如果要使用严格处理，则可以使用 **首选** 的标头和集 `handling=strict` 。

 ## <a name="chained--reverse-chained-searching"></a>链接 & 反向链接搜索

使用 [链式搜索](https://www.hl7.org/fhir/search.html#chaining) ，可以使用搜索参数搜索其他资源引用的资源。 例如，如果要查找患者名称为 Jane 的情况，请使用：

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

同样，可以执行反向链接搜索。 这样，你便可以获取一些资源，你可以在其中对其他引用这些资源的资源指定条件。 有关链接和反向链接搜索的更多示例，请参阅 [FHIR 搜索示例](search-samples.md) 页。 

> [!NOTE]
> 在用于 FHIR 的 Azure API 和 Cosmos DB 提供支持的开源中，有一个限制：链式和反向链接搜索所需的每个子查询仅返回100个项目。 如果找到了100多个项目，则会收到以下错误消息： "链接的表达式中的子查询无法返回超过100个结果，请使用更具选择性的条件"。 若要成功查询，需要更具体地了解所需内容。

## <a name="pagination"></a>分页

如上所述，搜索结果将是分页绑定。 默认情况下，搜索将每页返回10个结果，但这可以通过指定 (或减小) 增加 `_count` 。 在绑定中，会有一个包含搜索当前结果的自链接。 如果有其他匹配项，则该捆绑包将包含 "下一步" 链接。 你可以继续使用 "下一步" 链接来获取后续的结果页面。 `_count` 仅限1000项或更少。 

目前，适用于 FHIR 的 Azure API 仅支持捆绑包中的下一个链接，并且它不支持第一个、最后一个或以前的链接。

## <a name="next-steps"></a>后续步骤

现在，你已了解有关搜索的基本知识，请参阅搜索示例页，详细了解如何使用不同的搜索参数、修饰符和其他 FHIR 搜索方案搜索。

>[!div class="nextstepaction"]
>[FHIR 搜索示例](search-samples.md)
