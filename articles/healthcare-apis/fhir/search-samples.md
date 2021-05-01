---
title: 用于 FHIR 的 Azure API 的搜索示例
description: 如何使用不同的搜索参数、修饰符和其他 FHIR 搜索工具进行搜索
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 04/20/2021
ms.author: ginle
ms.openlocfilehash: edbbfe81b4926689e0a431a28ac91e9f07e8e944
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108322509"
---
# <a name="fhir-search-examples"></a>FHIR 搜索示例

下面是使用 FHIR 搜索操作的一些示例，包括搜索参数和修饰符、链和反向链搜索、复合搜索、查看搜索结果的下一项集以及使用 POST 请求搜索。 有关搜索的详细信息，请参阅 [FHIR 搜索概述](overview-of-search.md)。
   
## <a name="search-result-parameters"></a>搜索结果参数

### <a name="_include"></a>_include

> [!NOTE]
> **_include** 和 **_revinclude** 限制为100项。

`_include` 跨资源搜索包含资源的指定参数的资源。 例如，你可以跨 `MedicationRequest` 资源进行搜索，只查找包含特定患者处方的相关信息的 `reference` 参数 `patient` ：

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

### <a name="_revinclude"></a>_revinclude

`_revinclude` 是中的一个额外搜索 `_include` ，它搜索引用中搜索结果的资源 `_include` 。 例如，可搜索 `MedicationRequest` 资源。 对于返回的每个资源，搜索 `DetectedIssue` 显示临床问题的资源 `patient` ：

```rest
GET [your-fhir-server]/MedicationRequest?_revinclude=DetectedIssue:patient

```
### <a name="_elements"></a>_elements

`_elements` 通过省略不必要的数据，将搜索结果缩小为字段的子集以减小响应大小。 参数接受以逗号分隔的基本元素列表：

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

在此请求中，你将获得一束患者，但每个资源只包含标识符 () 和患者的活动状态。 此返回响应中的资源将包含 `meta.tag` 值 `SUBSETTED` ，以指示它们是一组不完整的结果。

## <a name="search-modifiers"></a>搜索修饰符

### <a name="not"></a>： not

`:not` 允许查找属性不为 true 的资源。 例如，你可以搜索性别不是女性的患者：

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

作为返回值，你将获得所有患者条目，其中性别不是女性，包括空值 (未使用性别) 指定的条目。 这不同于搜索患者，其中性别是男，因为它不包括没有特定性别的条目。

### <a name="missing"></a>：缺少

`:missing` 返回值为时没有指定元素值的所有资源 `true` ，并返回值为时包含指定元素的所有资源 `false` 。 对于简单的数据类型元素， `:missing=true` 将在存在具有扩展名但包含空值的元素的所有资源上匹配。 例如，如果要查找有关 `Patient` 出生日期缺少信息的所有资源，可以执行以下操作：

```rest
GET [your-fhir-server]/Patient?birthDate:missing=true

```

### <a name="exact"></a>：精确
`:exact` 用于 `string` 参数，并返回与参数精确匹配的结果，如在大小写和字符连接中。

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

此请求返回 `Patient` 名称与完全相同的资源 `Jon` 。 如果资源具有名称（如 `Jonathan` 或）的患者 `joN` ，则搜索将忽略并跳过资源，因为它与指定的值并不完全匹配。

### <a name="contains"></a>：包含
`:contains` 用于 `string` 参数，并在被搜索字段内的字符串中的任意位置搜索具有部分匹配指定值的资源。 `contains` 不区分大小写，允许字符串联。 例如：

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

此请求将返回 `Patient` 具有 `address` 包含字符串 "Meadow" 的值的所有资源。 这意味着，可以将包含值（如 "Meadowers" 或 "59 Meadow ST"）作为搜索结果返回的地址。

## <a name="chained-search"></a>链式搜索 

若要执行一系列涵盖多个引用参数的搜索操作，可以 "链接" 一系列引用参数，只需要使用句点将这些参数追加到服务器请求即可 `.` 。 例如，如果想要查看所有 `DiagnosticReport` 资源 `subject` 引用 `Patient` 包含特定资源的资源，请 `name` 执行以下操作：  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

此请求将返回患者使用者为 "Sarah" 的所有资源。 字段之后的时间段在 `.` `Patient` 参数的 reference 参数上执行链式搜索 `subject` 。

链式搜索的另一种常见用途是查找特定患者的全部情况。 `Patient`通常会有一个或多个 `Encounter` 具有主题。 搜索 `Encounter` `Patient` 具有所提供的的所有资源 `id` ：

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

使用链式搜索，可以找到 `Encounter` 与特定信息片段匹配的所有资源 `Patient` ，例如 `birthdate` ：

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthDate=1987-02-20

```

这将允许不只搜索 `Encounter` 单个患者的资源，而是在具有指定的出生日期值的所有患者上搜索资源。 

此外，通过使用符号，可以在一个请求中完成多个链式搜索，这样 `&` 就可以在一个请求中搜索多个条件。 在这种情况下，链式搜索 "独立" 搜索每个参数，而不是搜索一次只满足所有条件的条件。 它是或操作，而不是和操作。 例如，如果想要获取具有特定名称或特定状态的实践者的所有患者：

```rest
GET [your-fhir-server]/Patient?general-practitioner.name=Sarah&general-practitioner.address-state=WA

```

这会返回所有 `Patient` "Sarah" 为的资源 `generalPractitioner` ，以及所有具有具有 `Patient` `generalPractitioner` 状态 WA 的地址的资源。 换句话说，您可以从状态 NY 获得 Sarah，并从状态 WA 中按返回的结果进行计费。 链式搜索不要求满足所有条件，并按参数进行单独计算。

对于搜索必须是作为一个组的所有条件的 AND 操作的情况，请参阅下面的 **复合搜索** 示例。

## <a name="reverse-chain-search"></a>反向链搜索

通过链接搜索，你可以根据资源所引用的资源的属性来搜索资源。 使用反向链搜索，可以通过其他方式进行操作。 可以使用参数根据引用资源的资源的属性搜索资源 `_has` 。 例如， `Observation` 资源具有 `patient` 引用患者资源的搜索参数。 若要查找由引用的所有患者资源，请 `Observation` `code` 执行以下操作：

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

此请求返回与代码一起引用的患者资源 `Observation` `527` 。 

此外，反向链搜索还可以有一个递归结构。 例如，如果要搜索的所有患者都具有 `Observation` 特定用户的审核事件 `janedoe` ，则可以执行以下操作：

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:user=janedoe

``` 

> [!NOTE]
> 在用于 FHIR 的 Azure API 和 Cosmos 支持的开源 FHIR 服务器中，链式搜索和反向链接搜索是 MVP 实现。 若要在 Cosmos DB 上完成链式搜索，实现会遍历搜索表达式，并发出子查询来解析匹配的资源。 这适用于表达式的每个级别。 如果任何查询返回的结果超过100，则将引发错误。 默认情况下，链式搜索位于功能标志之后。 若要在 Cosmos DB 上使用链式搜索，请使用标头 x-启用-链式搜索： true。

## <a name="composite-search"></a>复合搜索

若要同时搜索同时满足多个条件的资源，请使用复合搜索，将单个参数值序列与符号联接在一起 `$` 。 返回的结果是与联接的搜索参数所指定的所有条件相匹配的资源的交集。 此类搜索参数称为复合搜索参数，它们定义了一个在嵌套结构中组合多个参数的新参数。 例如，如果要查找 `DiagnosticReport` 包含的 `Observation` potassium 值小于或等于9.2 的所有资源：

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

此请求指定包含代码的组件 `2823-3` ，在本例中为 potassium。 在 `$` 符号后，它使用 `lt` "小于或等于" 和 potassium 值范围指定组件的值范围 `9.2` 。 

## <a name="search-the-next-entry-set"></a>搜索下一个条目集

可按单个搜索查询返回的最大条目数为1000。 但是，你可能有超过1000个条目与搜索查询匹配，你可能想要在返回的前1000项后查看下一组条目。 在这种情况下，你将使用中的继续标记 `url` 值， `searchset` 如下所示 `Bundle` ：

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

您可以在字段下为提供的 URL 执行 GET 请求 `relation: next` ：

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

这会返回搜索结果的下一组条目。 `searchset`是一组完整的搜索结果项，而继续标记 `url` 是由服务器提供的链接，可用于检索未显示在第一组上的项，因为针对搜索查询返回的最大项数限制。

## <a name="search-using-post"></a>使用 POST 搜索

上面提到的所有搜索示例均已使用 `GET` 请求。 你还可以使用 `POST` 以下请求执行搜索操作 `_search` ：

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

此请求将返回 `Patient` `id` 值为45的所有资源。 就像在 GET 请求中那样，服务器会确定哪一组资源符合)  (的条件，并在 HTTP 响应中返回绑定资源。

使用 POST 进行搜索的另一个示例是：

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[FHIR 搜索概述](overview-of-search.md)