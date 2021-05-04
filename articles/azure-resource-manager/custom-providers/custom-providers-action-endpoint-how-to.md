---
title: 将自定义操作添加到 Azure REST API
description: 了解如何向 Azure REST API 添加自定义操作。 本文将详细介绍那些需要实现自定义操作的终结点的要求和最佳做法。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650391"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>将自定义操作添加到 Azure REST API

本文将介绍创建可实现自定义操作的 Azure 自定义资源提供程序终结点的要求和最佳做法。 如果不熟悉 Azure 自定义资源提供程序，请参阅[自定义资源提供程序概述](overview.md)。

## <a name="how-to-define-an-action-endpoint"></a>如何定义操作终结点

终结点是一个指向服务的 URL，用于实现它与 Azure 之间的基础协定。 终结点在自定义资源提供程序中定义，可以是任何可公开访问的 URL。 下面的示例有一个由 `endpointURL` 实现的名为 `myCustomAction` 的操作。

示例 ResourceProvider：

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
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

## <a name="building-an-action-endpoint"></a>构建操作终结点

实现操作的终结点必须处理 Azure 中新 API 的请求和响应。 创建具有操作的自定义资源提供程序时，它会在 Azure 中生成一组新的 API。 在本例中，操作将为 `POST` 调用生成新的 Azure 操作 API：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 传入请求：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

然后，此请求将以以下形式转发到终结点：

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

同样，终结点的响应随后将转发回客户。 终结点的响应应返回：

- 有效的 JSON 对象文档。 所有数组和字符串都应嵌套在顶级对象下。
- `Content-Type` 标头应设置为“application/json; charset=utf-8”。

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure 自定义资源提供程序响应：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>调用自定义操作

可以通过两种主要方法来调用自定义资源提供程序的自定义操作：

- Azure CLI
- Azure 资源管理器模板

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

参数 | 必需 | 说明
---|---|---
action | 是 | 在 ResourceProvider 中定义的操作的名称。
ids | 是 | ResourceProvider 的资源 ID。
request-body | 否 | 将要发送到终结点的请求正文。

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板

> [!NOTE]
> Azure 资源管理器模板对操作的支持有限。 若要使操作可在模板内调用，它的名称中必须包含 [`list`](../templates/template-functions-resource.md#list) 前缀。

包含 List 操作的示例 ResourceProvider：

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

示例 Azure 资源管理器模板：

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

参数 | 必需 | 说明
---|---|---
resourceIdentifier | 是 | ResourceProvider 的资源 ID。
apiVersion | 是 | 资源运行时的 API 版本。 此项应始终为“2018-09-01-preview”。
functionValues | 否 | 将要发送到终结点的请求正文。

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序概述](overview.md)
- [快速入门：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [操作说明：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
