---
title: Azure 应用配置 REST API - 键
description: 使用 Azure 应用配置 REST API 处理键的参考页
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932534"
---
# <a name="keys"></a>键

api-version：1.0

以下语法表示键资源：

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>操作

键资源支持以下操作：

- 列出

对于所有操作，`name` 为可选筛选器参数。 如果省略，则暗指任何键。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>列出密钥

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>分页

如果返回的项数超过响应限制，则会对结果进行分页。 按照可选的 `Link` 响应标头进行操作并使用 `rel="next"` 进行导航。 也可让内容以 `@nextLink` 属性的形式提供下一个链接。 下一个链接包含 `api-version` 参数。

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

支持按 ```name``` 筛选。

```http
GET /keys?name={key-name}&api-version={api-version}
```

支持以下筛选器：

|键筛选器|效果|
|--|--|
|省略 `name` 或 `name=*`|匹配任何键|
|`name=abc`|匹配名为“abc”的键|
|`name=abc*`|匹配以 abc 开头的键名称|
|`name=abc,xyz`|匹配键名称 abc 或 xyz（限制为 5 个 CSV） |

保留以下字符：`*`、`\`、`,`

如果保留字符是值的一部分，则必须使用 `\{Reserved Character}` 对其进行转义。 非保留字符也可以进行转义。

## <a name="filter-validation"></a>筛选器验证

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

## <a name="examples"></a>示例

- 全部

    ```http
    GET /keys?api-version={api-version}
    ```

- 键名称以“abc”开头

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- 键名称为“abc”或“xyz”

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>请求特定字段

使用可选的 `$select` 查询字符串参数，并提供所请求字段的逗号分隔列表。 如果省略 `$select` 参数，则响应会包含默认集。

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>基于时间的访问

获得结果在过去的表现形式。 请参阅 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) 部分

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
