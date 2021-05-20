---
title: 自定义提供程序概述
description: 了解 Azure 自定义资源提供程序，以及如何扩展 Azure API 平面来适应工作流。
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "80398462"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 自定义资源提供程序概述

Azure 自定义资源提供程序是 Azure 的扩展性平台。 通过它，可以定义自定义 API 来扩充默认 Azure 体验。 本文档描述了：

- 如何生成和部署 Azure 自定义资源提供程序。
- 如何利用 Azure 自定义资源提供程序来扩展现有工作流。
- 在哪里可以找到入门指南和代码示例。

![自定义提供程序概述](./media/overview/overview.png)

> [!IMPORTANT]
> 自定义提供程序目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-custom-resource-providers-do"></a>自定义资源提供程序有何用途

下面是一些使用 Azure 自定义资源提供程序可以实现的示例：

- 扩展 Azure 资源管理器 REST API，以包括内部和外部服务。
- 在现有 Azure 工作流之上启用自定义方案。
- 自定义 Azure 资源管理器模板控件和效果。

## <a name="what-is-a-custom-resource-provider"></a>什么是自定义资源提供程序

Azure 自定义资源提供程序是通过在 Azure 与终结点之间创建合同来实现的。 此合同通过新资源 Microsoft.CustomProviders/resourceProviders 定义新资源和操作的列表。 然后，自定义资源提供程序会在 Azure 中公开这些新 API。 Azure 自定义资源提供程序由三部分组成：自定义资源提供程序、终结点和自定义资源。

## <a name="how-to-build-custom-resource-providers"></a>如何生成自定义资源提供程序

自定义资源提供程序是 Azure 与终结点之间的合同列表。 此合同描述了 Azure 应如何与终结点交互。 资源提供程序充当代理，将向/从指定的终结点转发请求和响应。 资源提供程序可以指定两种类型的合同：[resourceTypes](./custom-providers-resources-endpoint-how-to.md) 和 [actions](./custom-providers-action-endpoint-how-to.md)。 通过终结点定义启用这些合同。 终结点定义由三个字段组成：name、routingType 和 endpoint。

示例终结点：

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

属性 | 必选 | 说明
---|---|---
name | 是 | 终结点定义的名称。 Azure 会通过其 API 在“/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}”下公开此名称
routingType | 否 | 确定与终结点的合同类型。 如果未指定，则会默认为 "Proxy"。
endpoint | 是 | 终结点，可向其路由请求。 此项将处理响应以及请求的任何附带作用。

### <a name="building-custom-resources"></a>生成自定义资源

ResourceTypes 描述了添加到 Azure 的新自定义资源。 这些资源公开基本的 RESTful CRUD 方法。 [详细了解如何创建自定义资源](./custom-providers-resources-endpoint-how-to.md)

包含 resourceTypes 的示例自定义资源提供程序：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

为上面的示例添加到 Azure 的 API：

HttpMethod | 示例 URI | 说明
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 用于新建资源的 Azure REST API 调用。
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 用于删除现有资源的 Azure REST API 调用。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 用于检索现有资源的 Azure REST API 调用。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | 用于检索现有资源列表的 Azure REST API 调用。

### <a name="building-custom-actions"></a>生成自定义操作

Actions 描述了添加到 Azure 的新操作。 这些操作可以在资源提供程序的基础之上公开，也可以嵌套在 resourceType下。 [详细了解如何创建自定义操作](./custom-providers-action-endpoint-how-to.md)

包含 actions 的示例自定义资源提供程序：

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
  "location": "eastus"
}
```

为上面的示例添加到 Azure 的 API：

HttpMethod | 示例 URI | 说明
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | 用于激活操作的 Azure REST API 调用。

## <a name="looking-for-help"></a>寻求帮助

如果你对 Azure 自定义资源提供程序开发有任何疑问，请尝试在 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) 上提问。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

本文介绍了自定义提供程序。 若要创建自定义提供程序，请继续学习下一篇文章。

- [快速入门：创建 Azure 自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [操作说明：将自定义操作添加到 Azure REST API 中](./custom-providers-action-endpoint-how-to.md)
- [操作说明：将自定义资源添加到 Azure REST API 中](./custom-providers-resources-endpoint-how-to.md)
