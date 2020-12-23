---
title: Azure 应用配置 REST API - 标签
description: 使用 Azure 应用配置 REST API 处理标签的参考页
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932500"
---
# <a name="labels"></a>标签

api-version：1.0

“标签”资源的定义如下：

```json
{
      "name": [string]             // Name of the label
}
```

支持以下操作：

- 列出

对于所有操作，``name`` 为可选筛选器参数。 如果省略，则暗指任何标签。

## <a name="prerequisites"></a>必备条件

- 所有 HTTP 请求都必须经过身份验证。 请参阅[身份验证](./rest-api-authentication-index.md)部分。
- 所有 HTTP 请求都必须提供显式的 `api-version`。 请参阅[版本控制](./rest-api-versioning.md)部分。

## <a name="list-labels"></a>列出标签

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>分页

如果返回的项数超过响应限制，则会对结果进行分页。 按照可选的 `Link` 响应标头进行操作并使用 `rel="next"` 进行导航。 也可让内容以 `@nextLink` 属性的形式提供下一个链接。 下一个链接包含 `api-version` 参数。

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>筛选

支持按 `name` 筛选。

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>支持的筛选器

|键筛选器|效果|
|--|--|
|省略 `name` 或 `name=*`|匹配任何标签|
|`name=abc`|匹配名为“abc”的标签|
|`name=abc*`|匹配以“abc”开头的标签名称|
|`name=abc,xyz`|匹配标签名称“abc”或“xyz”（限制为 5 个 CSV） |

### <a name="reserved-characters"></a>保留字符

`*`, `\`, `,`

如果保留字符是值的一部分，则必须使用 `\{Reserved Character}` 对其进行转义。 非保留字符也可以进行转义。

### <a name="filter-validation"></a>筛选器验证

如果出现筛选器验证错误，则响应为“HTTP `400`”，其中包含以下错误详细信息：

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>示例

- 全部

    ```http
    GET /labels?api-version={api-version}
    ```

- 标签名称以“abc”开头

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- 标签名称为“abc”或“xyz”

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>请求特定字段

使用可选的 `$select` 查询字符串参数，并提供所请求字段的逗号分隔列表。 如果省略 `$select` 参数，则响应会包含默认集。

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>基于时间的访问

获得结果在过去的表现形式。 请参阅 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) 部分

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
