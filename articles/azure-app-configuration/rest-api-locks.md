---
title: Azure 应用配置 REST API-锁定
description: 使用 Azure 应用配置处理键值锁的参考页面 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7e63b48f2119c48cd43717acee7b13b1701e0032
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241261"
---
# <a name="locks"></a>锁

此 API (版本 1.0) 为键-值资源提供锁定和解除锁定语义。 它支持以下操作：

- 锁定位置
- 删除锁定

如果存在，则 `label` 必须是 (不是通配符) 的显式标签值。 对于所有操作，它是一个可选参数。 如果省略，则表示没有标签。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>锁定键-值

- 必需： ``{key}`` 、 ``{api-version}``  
- 可选：``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**响应**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

如果键-值不存在，将返回以下响应：

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>解锁键-值

- 必需： ``{key}`` 、 ``{api-version}``  
- 可选：``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**响应**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

如果键-值不存在，将返回以下响应：

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>条件锁定和解锁

若要防止出现争用情况，请使用 `If-Match` 或 `If-None-Match` 请求标头。 `etag`参数是密钥表示形式的一部分。 如果 `If-Match` `If-None-Match` 省略或，则操作为无条件。

仅当当前键值表示形式与指定匹配时，以下请求才应用操作 `etag` ：

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

仅当当前键值表示形式存在但与指定的不匹配时，以下请求才应用操作 `etag` ：

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
