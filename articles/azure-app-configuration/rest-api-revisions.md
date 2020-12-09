---
title: Azure 应用配置 REST API-键值修订
description: 使用 Azure 应用配置处理键值修订的参考页面 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cfa117d1ed017170c279b7c4e0a146ae4edac108
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932466"
---
# <a name="key-value-revisions"></a>键/值修订版

“键/值修订版”定义了键/值资源的历史表示形式。 对于免费层存储，修订版会在 7 天后过期；对于标准层存储，则在 30 天后过期。 修订版本支持该 `List` 操作。

对于所有操作，``key`` 为可选参数。 如果省略，则暗指任何键。

对于所有操作，``label`` 为可选参数。 如果省略，则暗指任何标签。

本文适用于 API 版本1.0。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>列出修订

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>分页

如果返回的项数超过响应限制，则会对结果进行分页。 按照可选 ``Link`` 响应标头进行操作并使用 ``rel="next"`` 进行导航。 或者，内容提供了属性形式的下一个链接 ``@nextLink`` 。

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>列出修订版的子集

使用 `Range` 请求标头。 响应包含一个 `Content-Range` 标头。 如果服务器无法满足所请求的范围，则它会通过 HTTP () 来做出响应 `416` `RangeNotSatisfiable` 。

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**响应**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>筛选

支持组合使用 `key` 和 `label` 筛选。
使用可选的 `key` 和 `label` 查询字符串参数。

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>支持的筛选器

|密钥筛选器|效果|
|--|--|
|省略 `key` 或 `key=*`|匹配任何键|
|`key=abc`|匹配名为“abc”的键|
|`key=abc*`|匹配以“abc”开头的键名称|
|`key=*abc`|匹配以“abc”结尾的键名称|
|`key=*abc*`|匹配包含“abc”的键名称|
|`key=abc,xyz`|匹配键名称“abc”或“xyz”（限制为 5 个 CSV） |

|标签筛选器|效果|
|--|--|
|省略 `label` 或 `label=`|匹配不带标签的条目|
|`label=*`|匹配任何标签|
|`label=prod`|匹配标签“prod”|
|`label=prod*`|匹配以“prod”开头的标签|
|`label=*prod`|匹配以“prod”结尾的标签|
|`label=*prod*`|匹配包含“prod”的标签|
|`label=prod,test`|匹配标签“prod”或“test”（限制为 5 个 CSV）|

### <a name="reserved-characters"></a>保留字符

保留字符为：

`*`, `\`, `,`

如果保留字符是值的一部分，则必须使用对其进行转义 `\{Reserved Character}` 。 非保留字符也可以进行转义。

### <a name="filter-validation"></a>筛选器验证

如果出现筛选器验证错误，则响应为“HTTP `400`”，其中包含以下错误详细信息：

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>示例

- 所有：

    ```http
    GET /revisions
    ```

- 项名称以 **abc** 开头的项：

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- 项名称为 **abc** 或 **xyz** 并且标签包含 **生产**：

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>请求特定字段

使用可选的 `$select` 查询字符串参数，并提供以逗号分隔的请求字段列表。 如果省略 `$select` 参数，则响应会包含默认集。

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>基于时间的访问

获得结果在过去的表现形式。 有关详细信息，请参阅用于 [Time-Based 访问资源状态的 HTTP Framework--名册](https://tools.ietf.org/html/rfc7089#section-2.1)，第2.1.1 部分。

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
