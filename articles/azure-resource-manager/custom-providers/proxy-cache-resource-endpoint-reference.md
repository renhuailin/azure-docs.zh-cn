---
title: 自定义资源缓存参考
description: Azure 自定义资源提供程序的自定义资源缓存参考 本文将详细介绍实现缓存自定义资源的终结点的要求。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650378"
---
# <a name="custom-resource-cache-reference"></a>自定义资源缓存参考

本文将详细介绍实现缓存自定义资源的终结点的要求。 如果不熟悉 Azure 自定义资源提供程序，请参阅[自定义资源提供程序概述](overview.md)。

## <a name="how-to-define-a-cache-resource-endpoint"></a>如何定义缓存资源终结点

可以通过将 "routingType" 指定为 "Proxy, Cache" 来创建代理资源。

自定义资源提供程序示例：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>生成代理资源终结点

实现 "Proxy, Cache" 资源终结点的终结点必须处理 Azure 中新 API 的请求和响应。 在本例中，resourceType 将为 `PUT`、`GET` 和 `DELETE` 生成一个新的 Azure 资源 API，以对单个资源执行 CRUD，并执行 `GET` 以检索所有现有资源：

> [!NOTE]
> Azure API 会生成请求方法 `PUT`、`GET` 和 `DELETE`，但是缓存终结点只需处理 `PUT` 和 `DELETE`。
> 我们建议终结点也实现 `GET`。

### <a name="create-a-custom-resource"></a>创建自定义资源

Azure API 传入请求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

然后，此请求将以以下形式转发到终结点：

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

同样，终结点的响应随后将转发回客户。 终结点的响应应返回：

- 有效的 JSON 对象文档。 所有数组和字符串都应嵌套在顶级对象下。
- `Content-Type` 标头应设置为“application/json; charset=utf-8”。
- 自定义资源提供程序会覆盖请求的 `name`、`type` 和 `id` 字段。
- 自定义资源提供程序将仅返回缓存终结点的 `properties` 对象下的字段。

终结点响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

自定义资源提供程序会自动为自定义资源生成 `name`、`id` 和 `type` 字段。

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>删除自定义资源

Azure API 传入请求：

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

然后，此请求将以以下形式转发到终结点：

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

同样，终结点的响应随后将转发回客户。 终结点的响应应返回：

- 有效的 JSON 对象文档。 所有数组和字符串都应嵌套在顶级对象下。
- `Content-Type` 标头应设置为“application/json; charset=utf-8”。
- 仅当返回 200 级别的响应时，Azure 自定义资源提供程序才会从其缓存中删除该项。 即使资源不存在，终结点也应返回 204。

终结点响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>检索自定义资源

Azure API 传入请求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

此请求不会转发到终结点。

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>枚举所有自定义资源

Azure API 传入请求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

此请求不会转发到终结点。

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序概述](overview.md)
- [快速入门：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [操作方法：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [参考：自定义资源代理参考](proxy-resource-endpoint-reference.md)
