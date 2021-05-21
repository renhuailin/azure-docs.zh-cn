---
title: 将自定义资源添加到 Azure REST API
description: 了解如何向 Azure REST API 添加自定义资源。 本文将详细介绍那些需要实现自定义资源的终结点的要求和最佳做法。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650521"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>将自定义资源添加到 Azure REST API

本文将介绍创建可实现自定义资源的 Azure 自定义资源提供程序终结点的要求和最佳做法。 如果不熟悉 Azure 自定义资源提供程序，请参阅[自定义资源提供程序概述](overview.md)。

## <a name="how-to-define-a-resource-endpoint"></a>如何定义资源终结点

终结点是一个指向服务的 URL，用于实现它与 Azure 之间的基础协定。 终结点在自定义资源提供程序中定义，可以是任何可公开访问的 URL。 以下示例具有一个由 `endpointURL` 实现的名为 `myCustomResource` 的 resourceType。

示例 ResourceProvider：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>构建资源终结点

实现 resourceType 的终结点必须处理 Azure 中新 API 的请求和响应。 创建具有 resourceType 的自定义资源提供程序时，它会在 Azure 中生成一组新的 API。 在本例中，resourceType 将为 `PUT`、`GET` 和 `DELETE` 生成一个新的 Azure 资源 API，以对单个资源执行 CRUD，并执行 `GET` 以检索所有现有资源：

操作单个资源（`PUT`、`GET` 和 `DELETE`）：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

检索所有资源 (`GET`)：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

对于自定义资源，自定义资源提供程序提供两种类型的 routingTypes：“`Proxy`”和“`Proxy, Cache`”。

### <a name="proxy-routing-type"></a>代理路由类型

“`Proxy`”routingType 将所有请求方法代理到自定义资源提供程序中指定的终结点 。 何时使用“`Proxy`”：

- 需要完全控制响应。
- 将系统与现有资源集成。

若要详细了解“`Proxy`”资源，请参阅[自定义资源代理参考](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>代理缓存路由类型

“`Proxy, Cache`”routingType 仅将 **和** 请求方法代理到自定义资源提供程序中指定的终结点`PUT``DELETE`。 自定义资源提供程序将根据它在其缓存中存储的内容自动返回 `GET` 请求。 如果将自定义资源标记为缓存，则自定义资源提供程序还会在响应中添加/覆盖字段，使 API 符合 Azure。 何时使用“`Proxy, Cache`”：

- 创建没有现有资源的新系统。
- 使用现有的 Azure 生态系统。

若要详细了解“`Proxy, Cache`”资源，请参阅[自定义资源缓存参考](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>创建自定义资源

可以通过两种主要方法来独立于自定义资源提供程序创建自定义资源：

- Azure CLI
- Azure 资源管理器模板

### <a name="azure-cli"></a>Azure CLI

创建自定义资源：

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

参数 | 必需 | 说明
---|---|---
is-full-object | 是 | 指示属性对象包含其他选项，例如位置、标记、sku 和/或计划。
id | 是 | 自定义资源的资源 ID。 此 ID 应独立于 ResourceProvider 存在
properties | *是* | 将要发送到终结点的请求正文。

删除 Azure 自定义资源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

参数 | 必需 | 说明
---|---|---
id | 是 | 自定义资源的资源 ID。 此 ID 应独立于 ResourceProvider 存在。

检索 Azure 自定义资源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

参数 | 必需 | 说明
---|---|---
id | 是 | 自定义资源的资源 ID。 此 ID 应独立于 ResourceProvider 存在

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板

> [!NOTE]
> 资源要求响应包含终结点中的相应 `id`、 `name` 和 `type`。

Azure 资源管理器模板要求从下游终结点正确返回 `id`、`name` 和 `type`。 返回的资源响应应采用以下格式：

示例终结点响应：

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

示例 Azure 资源管理器模板：

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

参数 | 必需 | 说明
---|---|---
resourceTypeName | 是 | 在自定义提供程序中定义的 resourceType 的名称 。
resourceProviderName | 是 | 自定义资源提供程序实例名称。
customResourceName | 是 | 自定义资源名称。

## <a name="next-steps"></a>后续步骤

- [Azure 自定义资源提供程序概述](overview.md)
- [快速入门：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [操作方法：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [参考：自定义资源代理参考](proxy-resource-endpoint-reference.md)
- [参考：自定义资源缓存参考](proxy-cache-resource-endpoint-reference.md)
