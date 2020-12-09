---
title: Azure 应用配置 REST API-键值
description: 使用 Azure 应用配置处理键值的参考页面 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 95081d6f8c2770d01f7836e08b6851860bf47ba8
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932551"
---
# <a name="key-values"></a>键-值

键-值是由的唯一组合标识的资源 `key`  +  `label` 。 `label` 是可选项。 若要显式引用不带标签的键值，请使用 "\ 0" (URL 编码为 ``%00``) 。 请查看每个操作的详细信息。

本文适用于 API 版本1.0。

## <a name="operations"></a>操作

- 获取
- 列出多个
- 设置
- 删除

## <a name="prerequisites"></a>必备条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>语法

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>获取键-值

必需： ``{key}`` 、 ``{api-version}``  
可选： ``label`` 如果省略此参数，则表示不带标签的键值 (。 ) 

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

响应：

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

如果键不存在，将返回以下响应：

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>按条件获取 () 

若要改善客户端缓存，请使用 `If-Match` 或 `If-None-Match` 请求标头。 `etag` 参数是键表示形式的一部分。 有关详细信息，请参阅 [14.24 和14.26 部分](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。

仅在当前表示形式与指定的 `etag` 不匹配时，以下请求才检索键值：

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

响应：

```http
HTTP/1.1 304 NotModified
```

或

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>列出键-值

可选： ``key`` 如果未指定，则 (，它表示任何键。 ) 可选： ``label`` (如果未指定，则表示任意标签。 ) 

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

有关其他选项，请参阅本文后面的 "筛选" 一节。

## <a name="pagination"></a>分页

如果返回的项数超过响应限制，则会对结果进行分页。 遵循可选的 `Link` 响应标头，并用于 `rel="next"` 导航。
也可让内容以 `@nextLink` 属性的形式提供下一个链接。 链接 URI 包含 `api-version` 参数。

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
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

支持组合使用 `key` 和 `label` 筛选。
使用可选的 `key` 和 `label` 查询字符串参数。

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>支持的筛选器

|密钥筛选器|效果|
|--|--|
|省略 `key` 或 `key=*`|匹配任何键|
|`key=abc`|匹配名为“abc”的键|
|`key=abc*`|匹配以“abc”开头的键名称|
|`key=abc,xyz`|匹配键名称“abc”或“xyz”（限制为 5 个 CSV） |

|标签筛选器|效果|
|--|--|
|省略 `label` 或 `label=*`|匹配任何标签|
|`label=%00`|匹配不带标签的 KV|
|`label=prod`|匹配标签“prod”|
|`label=prod*`|匹配以“prod”开头的标签|
|`label=prod,test`|匹配标签“prod”或“test”（限制为 5 个 CSV）|

保留字符*_

`_`, `\`, `,`

如果保留字符是值的一部分，则必须使用对其进行转义 `\{Reserved Character}` 。 非保留字符也可以进行转义。

***筛选器验证** _

如果出现筛选器验证错误，则响应为“HTTP `400`”，其错误详细信息如下：

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

_*示例

- 全部

    ```http
    GET /kv?api-version={api-version}
    ```

- 项名称以 **abc** 开头，并包括所有标签

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- 键名称以“abc”开头，并且标签等于“v1”或“v2”

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>请求特定的字段

使用可选的 `$select` 查询字符串参数，并提供以逗号分隔的请求字段列表。 如果省略 `$select` 参数，则响应会包含默认集。

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>基于时间的访问

获得结果在过去的表现形式。 有关详细信息，请参阅 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)部分。 如本文前面所述，仍支持分页。

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
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

## <a name="set-key"></a>设置键

- 必需：``{key}``
- 可选： ``label`` 如果未指定 (或标签 = %00，则它表示不带标签的键值。 ) 

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

响应：

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

如果该项已锁定，你将收到以下响应：

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>设置键 (有条件地) 

若要防止出现争用情况，请使用 `If-Match` 或 `If-None-Match` 请求标头。 `etag` 参数是键表示形式的一部分。
如果 `If-Match` `If-None-Match` 省略或，则操作为无条件。

仅当当前表示形式与指定的匹配时，以下响应才会更新值 `etag` ：

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

仅当当前表示形式与指定的不匹配时，以下响应才会更新值 `etag` ：

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

仅当表示形式已存在时，以下请求才会添加值：

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

仅当表示形式不存在时，以下请求才会添加值：

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**响应**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

或

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>删除

- 必需： `{key}` 、 `{api-version}`
- 可选： `{label}` 如果未指定 (或标签 = %00，则它表示不带标签的键值。 ) 

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**响应：** 返回已删除的键值；如果键值不存在，则不返回任何内容。

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

或

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a> (有条件地删除密钥) 

这类似于本文前面的 "设置密钥 (有条件地) " 一节。
