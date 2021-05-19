---
title: 资源载入
description: 了解如何使用 Azure 自定义提供程序执行资源加入，以将管理或配置应用于其他 Azure 资源类型。
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650404"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 自定义提供程序资源加入概述

Azure 自定义提供程序资源加入是适用于 Azure 资源类型的扩展性模型。 使用此模型可以跨现有的 Azure 资源大规模应用操作或管理。 有关详细信息，请参阅 [Azure 自定义提供程序如何扩展 Azure](overview.md)。 本文介绍：

- 资源加入的作用。
- 资源加入的基础知识及其用法。
- 在哪里可以找到入门指南和代码示例。

> [!IMPORTANT]
> 自定义提供程序目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-resource-onboarding-do"></a>资源加入有哪些作用？

类似于 [Azure 自定义提供程序自定义资源](./custom-providers-resources-endpoint-how-to.md)，资源加入定义了一种协定，可以通过某个终结点来代理“加入”请求。 与自定义资源不同，资源加入不会创建新的资源类型， 而是允许扩展现有的资源类型。 此外，资源加入可与 Azure Policy 配合工作，因此可以大规模进行资源管理和配置。 资源加入工作流的一些示例：

- 安装到虚拟机器扩展并在其中进行管理。
- 在 Azure 存储帐户中上传和配置默认值。
- 大规模启用基线诊断设置。

## <a name="resource-onboarding-basics"></a>有关资源加入的基础知识

可以使用 Microsoft.CustomProviders/resourceProviders 和 Microsoft.CustomProviders/associations 资源类型通过 Azure 自定义提供程序配置资源加入。 若要为自定义提供程序启用资源加入，请在配置过程中创建一个名为“association”的 resourceType，以及一个包含“Extension”的 routingType 。 Microsoft.CustomProviders/associations 和 Microsoft.CustomProviders/resourceProviders 不需要属于同一资源组。

下面是一个示例 Azure 自定义提供程序：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

属性 | 必需？ | 说明
---|---|---
name | 是 | 终结点定义的名称。 对于资源加入，该名称必须是“associations”。
routingType | 是 | 确定终结点的协定类型。 对于资源加入，有效的 routingTypes 为“Proxy,Cache,Extension”和“Webhook,Cache,Extension”。
endpoint | 是 | 终结点，可向其路由请求。 此属性将处理响应以及请求的任何副作用。

使用 associations 资源类型创建自定义提供程序后，可以使用 Microsoft.CustomProviders/associations 确定目标。 Microsoft.CustomProviders/associations 是可以扩展任何其他 Azure 资源的扩展资源。 创建 Microsoft.CustomProviders/associations 的实例后，它将采用 targetResourceId 属性，该属性应是有效的 Microsoft.CustomProviders/resourceProviders 或 Microsoft.Solutions/applications 资源 ID。 在这种情况下，请求将转发到创建的 Microsoft.CustomProviders/resourceProviders 实例上的 associations 资源类型。

> [!NOTE]
> 如果将 Microsoft.Solutions/applications 资源 ID 作为 targetResourceId 提供，则必须在名为“public”的受管理资源组中部署 Microsoft.CustomProviders/resourceProviders。

示例 Azure 自定义提供程序关联：

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

属性 | 必需？ | 说明
---|---|---
targetResourceId | 是 | Microsoft.CustomProviders/resourceProviders 或 Microsoft.Solutions/applications 的资源 ID。

## <a name="how-to-use-resource-onboarding"></a>如何使用资源加入

资源加入的工作原理是使用 Microsoft.CustomProviders/association 扩展资源来扩展其他资源。 在以下示例中，请求是对虚拟机发出的，但任何资源都可扩展。

首先，需要使用 associations 资源类型创建一个自定义提供程序资源。 这将声明在创建以自定义提供程序为目标的相应 Microsoft.CustomProviders/associations 资源时要使用的回调 URL。

示例 Microsoft.CustomProviders/resourceProviders 创建请求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

创建自定义提供程序后，可以指定其他资源作为目标，并在这些资源中应用自定义提供程序的副作用。

示例 Microsoft.CustomProviders/associations 创建请求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

然后，此请求将转发到在你创建的自定义提供程序中指定的终结点，该终结点是通过 targetResourceId 按以下格式引用的：

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

该终结点应使用 application/json `Content-Type` 和有效的 JSON 响应正文做出响应。 在 JSON 的 properties 对象下返回的字段将添加到关联返回响应中。

## <a name="getting-help"></a>获取帮助

如果你遇到了 Azure 自定义资源提供程序开发方面的问题，请尝试在 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) 上提问。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

本文介绍了自定义提供程序。 请查看以下文章了解详细信息：

- [教程：使用自定义提供程序进行资源加入](./tutorial-resource-onboarding.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [快速入门：创建自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [操作说明：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [操作说明：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
