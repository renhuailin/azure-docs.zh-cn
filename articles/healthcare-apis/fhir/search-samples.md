---
title: 搜索 Azure API for FHIR 的示例
description: 如何使用不同的搜索参数、修饰符和其他 FHIR 搜索工具进行搜索
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 6e3a074c24305209047fbd3e741fdb81256374e5
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460096"
---
# <a name="fhir-search-examples"></a>FHIR 搜索示例

下面是使用 FHIR 搜索操作的一些示例，包括搜索参数和修饰符、链和反向链搜索、复合搜索、查看搜索结果的下一项集以及使用 `POST` 请求搜索。 有关搜索的详细信息，请参阅 [FHIR 搜索概述](overview-of-search.md)。
   
## <a name="search-result-parameters"></a>搜索结果参数

### <a name="_include"></a>_include

`_include` 跨资源搜索包含资源的指定参数的资源。 例如，你可以跨 `MedicationRequest` 资源进行搜索，只查找包含特定患者处方的相关信息的 `reference` 参数 `patient` 。 在下面的示例中，此 `MedicationRequests` 操作将从中提取引用的所有和所有患者 `MedicationRequests` ：

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** 和 **_revinclude** 限制为100项。

### <a name="_revinclude"></a>_revinclude

`_revinclude` 允许您按相反的方向搜索 `_include` 。 例如，您可以搜索患者，然后反向包括引用患者的所有遇到的内容：

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` 通过省略不必要的数据，将搜索结果缩小为字段的子集以减小响应大小。 参数接受以逗号分隔的基本元素列表：

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

在此请求中，你将获得一束患者，但每个资源只包含标识符 () 和患者的活动状态。 此返回响应中的资源将包含 `meta.tag` 值 `SUBSETTED` ，以指示它们是一组不完整的结果。

## <a name="search-modifiers"></a>搜索修饰符

### <a name="not"></a>： not

`:not` 允许你查找属性不为 true 的资源。 例如，可以搜索性别不是女性的患者：

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

作为返回值，将获取性别不是女性的所有患者条目，包括不带性别 (指定的条目的空) 。 这不同于搜索性别为男性的患者，因为不包括没有特定性别的条目。

### <a name="missing"></a>：missing

`:missing` 当值为 时，返回没有指定元素值的所有资源，当值为 时，返回包含指定元素 `true` 的所有资源 `false` 。 对于简单的数据类型元素， 将在元素具有扩展名但具有空值的所有 `:missing=true` 资源上匹配。 例如，如果要查找缺少出生日期信息的所有资源， `Patient` 可以执行：

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>：exact
`:exact` 用于参数，并返回与参数精确匹配的结果，例如大小写串联和字符 `string` 串联。

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

此请求 `Patient` 返回名称与 完全相同的资源 `Jon` 。 如果资源具有名称（如 或 ）的 Patients，则搜索将忽略并跳过资源，因为它与指定的值 `Jonathan` `joN` 不完全匹配。

### <a name="contains"></a>：contains
`:contains` 用于参数，并搜索与所搜索字段中字符串中指定值部分匹配 `string` 的资源。 `contains` 不区分大小写，并允许字符串联。 例如：

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

此请求将返回包含 `Patient` 字段的所有 `address` 资源，这些字段的值包含字符串"一个"。 这意味着，可以具有包含值（如"用户"或"59 位中国 ST"）的地址作为搜索结果返回。

## <a name="chained-search"></a>链接搜索 

若要执行涵盖多个引用参数的一系列搜索操作，可以使用一个时间段将引用参数一个一个地追加到服务器请求中，以"链接"一系列引用参数 `.` 。 例如，如果想要查看所有 `DiagnosticReport` 资源 `subject` 引用 `Patient` 包含特定资源的资源，请 `name` 执行以下操作：  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

此请求将返回患者使用者为 "Sarah" 的所有资源。 字段之后的时间段在 `.` `Patient` 参数的 reference 参数上执行链式搜索 `subject` 。

常规搜索的另一种常见用法 (不是链式搜索) 查找特定患者的全部情况。 `Patient`通常会有一个或多个 `Encounter` 具有主题。 搜索 `Encounter` `Patient` 具有所提供的的所有资源 `id` ：

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

使用链式搜索，可以找到 `Encounter` 与特定信息片段匹配的所有资源 `Patient` ，例如 `birthdate` ：

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

这将允许不只搜索 `Encounter` 单个患者的资源，而是在具有指定的出生日期值的所有患者上搜索资源。 

此外，通过使用符号，可以在一个请求中完成多个链式搜索，这样 `&` 就可以在一个请求中搜索多个条件。 在这种情况下，链式搜索 "独立" 搜索每个参数，而不是搜索一次只满足所有条件的条件。 它是或操作，而不是和操作。 例如，如果想要获取具有特定名称或特定状态的实践者的所有患者：

```rest
GET [your-fhir-server]/Patient?general-practitioner.name=Sarah&general-practitioner.address-state=WA

```

这会返回所有 `Patient` "Sarah" 为的资源 `generalPractitioner` ，以及所有具有具有 `Patient` `generalPractitioner` 状态 WA 的地址的资源。 换句话说，您可以从状态 NY 获得 Sarah，并从状态 WA 中按返回的结果进行计费。 链式搜索不要求满足所有条件，并按参数进行单独计算。

对于搜索必须是作为一个组的所有条件的 AND 操作的情况，请参阅下面的 **复合搜索** 示例。

## <a name="reverse-chain-search"></a>反向链搜索

通过链接搜索，你可以根据资源所引用的资源的属性来搜索资源。 使用反向链搜索，可以反向执行。 可以使用 参数基于引用资源的资源的属性搜索 `_has` 资源。 例如， `Observation` 资源具有引用 `patient` 患者资源的搜索参数。 查找使用特定 引用的所有 `Observation` 患者资源 `code` ：

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

此请求返回通过代码 引用 `Observation` 的患者资源 `527` 。 

此外，反向链搜索可以具有递归结构。 例如，如果要搜索观察结果具有特定用户审核事件的所有患者， `Observation` `janedoe` 可以执行以下操作：

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:user=janedoe

``` 

> [!NOTE]
> 在Azure API for FHIR Cosmos 支持的开源 FHIR 服务器中，链接搜索和反向链接搜索是 MVP 实现。 若要在Cosmos DB上完成链接搜索，该实现会逐步执行搜索表达式，并问题子查询以解析匹配的资源。 此操作针对表达式的每个级别完成。 如果任何查询返回的结果超过 100 个，将引发错误。 默认情况下，链接搜索位于功能标志后面。 若要在 Cosmos DB 上使用链接搜索，请使用标头 x-ms-enable-chained-search： true。

## <a name="composite-search"></a>复合搜索

若要一次搜索满足多个条件的资源，请使用复合搜索，将单个参数值序列与符号 联接在一起 `$` 。 返回的结果将是与联接搜索参数指定的所有条件匹配的资源的交集。 此类搜索参数称为复合搜索参数，它们定义了一个新参数，该参数将多个参数组合在一个嵌套结构中。 例如，如果要查找包含值小于或等于 `DiagnosticReport` `Observation` 9.2 的所有资源：

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

此请求指定包含 的代码的组件， `2823-3` 在这种情况下，该代码为 "小程序"。 在 `$` 符号后，它使用 `lt` "小于或等于" 和 potassium 值范围指定组件的值范围 `9.2` 。 

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