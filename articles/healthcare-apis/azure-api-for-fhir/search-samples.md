---
title: 搜索 Azure API for FHIR 的示例
description: 如何使用不同的搜索参数、修饰符和其他 FHIR 搜索工具进行搜索
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 731b85d0486c3d9fa7ba9e3af4cc2f047912fcdc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778660"
---
# <a name="fhir-search-examples-for-azure-api-for-fhir"></a>Azure API for FHIR 的 FHIR 搜索示例

下面是一些使用 FHIR 搜索操作的示例，其中包括搜索参数和修饰符、链搜索和反向链搜索、复合搜索、查看搜索结果的下一个条目集，以及使用 `POST` 请求进行搜索。 有关搜索的详细信息，请参阅 [FHIR 搜索概述](overview-of-search.md)。
   
## <a name="search-result-parameters"></a>搜索结果参数

### <a name="_include"></a>_include

`_include` 跨资源搜索包含资源的指定参数的资源。 例如，可以跨 `MedicationRequest` 资源进行搜索，以仅查找包含特定患者的处方信息（即 `reference` 参数 `patient`）的资源。 在下面的示例中，这将拉取所有 `MedicationRequests` 以及从 `MedicationRequests` 引用的所有患者：

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> “include”和“revinclude”限制为 100 项 。

### <a name="_revinclude"></a>_revinclude

`_revinclude` 允许从与 `_include` 相反的方向进行搜索。 例如，可以搜索患者，然后反向包含引用该患者的所有出现情况：

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` 将搜索结果缩小为字段子集，通过省略不必要的数据来减小响应大小。 参数接受以逗号分隔的基元素列表：

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

在此请求中，你将获得一组患者，但每个资源将仅包含标识符以及患者的活动状态。 此返回的响应中的资源将包含 `SUBSETTED` 的 `meta.tag`值，以指示它们是不完整的结果集。

## <a name="search-modifiers"></a>搜索修饰符

### <a name="not"></a>:not

`:not` 允许查找属性不为 true 的资源。 例如，可以搜索性别不是女性的患者：

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

作为返回值，你将获得性别不是女性的所有患者条目，包括空值（未指定性别的条目）。 这与搜索性别为男性的患者不同，因为这样不会包括没有特定性别的条目。

### <a name="missing"></a>:missing

当值为 `true` 时，`:missing` 会返回没有指定元素值的所有资源，并且当值为 `false` 时，它会返回包含指定元素 的所有资源。 对于简单的数据类型元素，`:missing=true` 会对元素具有扩展名但具有空值的所有资源进行匹配。 例如，如果要查找缺少出生日期信息的所有 `Patient` 资源，可以执行以下操作：

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>:exact
`:exact` 用于 `string` 参数，并返回与参数精确匹配的结果，例如大小和字符串联。

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

此请求返回名称与 `Jon` 完全相同的 `Patient` 资源。 如果资源具有诸如 `Jonathan` 或 `joN` 之类的名称的患者，则搜索将忽略并跳过资源，因为它与指定的值不完全匹配。

### <a name="contains"></a>:contains
`:contains` 用于 `string` 参数，并搜索与所搜索字段的字符串中指定值部分匹配的资源。 `contains` 不区分大小写，并允许字符串联。 例如：

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

此请求将返回包含 `Patient` 字段的所有 `address` 资源，这些字段的值包含字符串“Meadow”。 这意味着，可以将包含“Meadowers”或“59 Meadow ST”等值的地址作为搜索结果返回。

## <a name="chained-search"></a>链式搜索 

若要执行涵盖多个引用参数的一系列搜索操作，可以使用一个句点 `.` 将引用参数一个一个地追加到服务器请求中，从而“链接”一系列引用参数。 例如，如果要查看所有 `DiagnosticReport` 资源，其中这些资源具有对包含特定 `name` 的 `Patient` 资源的 `subject` 引用：  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

此请求将返回患者主体名称为“Sarah”的所有 `DiagnosticReport` 资源。 字段 `Patient` 之后的句点 `.` 对 `subject` 参数的引用参数执行链式搜索。

常规搜索（不是链式搜索）的另一种常见用法是查找特定患者的所有出现情况。 `Patient` 通常与某个主体具有一次或多次 `Encounter`。 使用提供的 `id` 搜索 `Patient` 的所有 `Encounter` 资源：

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

使用链式搜索，可以找到与特定 `Patient` 信息段匹配的所有 `Encounter` 资源，例如`birthdate`：

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

这样不仅允许搜索单个患者的 `Encounter` 资源，还允许搜索具有指定出生日期值的所有患者。 

此外，可以使用符号 `&` 在一个请求中多次执行链式搜索，从而在一个请求中搜索多个条件。 在这种情况下，链式搜索会“独立”搜索每个参数，而不是搜索仅同时满足所有条件的条件：

```rest
GET [your-fhir-server]/Patient?general-practitioner:Practitioner.name=Sarah&general-practitioner:Practitioner.address-state=WA

```

这会返回所有以“Sarah”作为 `generalPractitioner` 的 `Patient` 资源，并返回地址为 WA 州的 `generalPractitioner`。 换而言之，如果引用为患者的 `generalPractitioner` 包含来自 NY 州的 Sarah 和 WA 州的 Bill，则将返回结果。

对于搜索必须作为一个组涵盖所有条件的 AND 操作的情况，请参阅下面的“复合搜索”示例。

## <a name="reverse-chain-search"></a>反向链式搜索

通过链式搜索，可以根据资源引用的属性搜索资源。 使用反向链式搜索可以从相反的方向进行搜索。 可以使用 `_has` 参数，根据引用资源的资源属性搜索资源。 例如，`Observation` 资源具有引用患者资源的搜索参数 `patient`。 若要查找由 `Observation` 通过特定 `code` 引用的所有患者资源：

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

此请求返回由 `Observation` 通过代码 `527` 引用的患者资源。 

此外，反向链式搜索可以具有递归结构。 例如，如果要搜索具有 `Observation` 的所有患者，其中观察结果具有来自特定用户 `janedoe` 的审核事件，可以执行以下操作：

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:agent:Practitioner.name=janedoe

``` 

> [!NOTE]
> 在 Azure API for FHIR 和由 Cosmos 提供支持的开放源代码 FHIR 服务器中，链式搜索和反向链式搜索是 MVP 实现。 为了在 Cosmos DB 上完成链式搜索，实现将遍历搜索表达式并发出子查询来解析匹配的资源。 此操作针对表达式的每个级别完成。 如果任何查询返回的结果超过 100 个，将引发错误。

## <a name="composite-search"></a>复合搜索

若要一次搜索满足多个条件的资源，请使用复合搜索，这种搜索使用符号 `$` 将单个参数值的序列联接在一起。 返回的结果将是与联接的搜索参数指定的所有条件匹配的资源的交集。 此类搜索参数称为复合搜索参数，它们定义了一个新参数，该参数将多个参数组合在一个嵌套结构中。 例如，如果要查找包含钾离子值小于或等于 9.2 的 `Observation` 的所有 `DiagnosticReport` 资源，请执行以下操作：

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

此请求指定包含 `2823-3` 代码的组件，在本例中中，该代码为钾离子。 位于 `$` 符号之后，它会指定组件的值范围，使用 `lt` 表示“小于或等于”，并指定 `9.2` 作为钾离子值范围。 

## <a name="search-the-next-entry-set"></a>搜索下一个条目集

每个单一搜索查询可返回的最大条目数为 1000。 但是，你可能有超过 1000 个与搜索查询匹配的条目，并且你可能想要在返回的前 1000 个条目之后看到下一组条目。 在这种情况下，可以使用 `searchset` 中的延续令牌 `url`，如下方的 `Bundle` 结果所示：

```json
    "resourceType": "Bundle",
    "id": "98731cb7-3a39-46f3-8a72-afe945741bd9",
    "meta": {
        "lastUpdated": "2021-04-22T09:58:16.7823171+00:00"
    },
    "type": "searchset",
    "link": [
        {
            "relation": "next",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd"
        },
        {
            "relation": "self",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated"
        }
    ],

```

然后，在字段 `relation: next` 下对提供的 URL 执行 GET 请求：

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

这将返回搜索结果的下一组条目。 `searchset` 是完整的搜索结果条目集，延续令牌 `url` 是服务器提供的链接，用于检索第一个集上未显示的条目，因为搜索查询返回的最大条目数有限制。

## <a name="search-using-post"></a>使用 POST 进行搜索

上面提到的所有搜索示例都使用了 `GET` 请求。 还可以通过 `_search` 使用 `POST` 请求执行搜索操作：

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

此请求将返回 `id` 值为 45 的所有 `Patient` 资源。 就像在 GET 请求中一样，服务器确定哪组资源满足条件，并以 HTTP 响应的形式返回捆绑资源。

使用 POST 进行搜索的另一个示例是查询参数作为表单正文提交：

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[FHIR 搜索概述](overview-of-search.md)