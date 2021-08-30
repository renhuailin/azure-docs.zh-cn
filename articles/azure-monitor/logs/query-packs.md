---
title: Azure Monitor 中的查询包
description: 使用 Azure Monitor 中的查询包可以在多个 Log Analytics 工作区中共享日志查询的集合。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: ef34ec8531242dfdb20e984c4c6d8f86d08b8fcf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724600"
---
# <a name="query-packs-in-azure-monitor-logs-preview"></a>Azure Monitor 日志中的查询包（预览版）
查询包是一个资源管理器对象，它充当 Azure Monitor 中日志查询的容器，该容器可用于保存日志查询，并在 Log Analytics 中的多个工作区和其他上下文之间共享这些查询。 

## <a name="view-query-packs"></a>查看查询包
可以在 Azure 门户中通过“Log Analytics 查询包”菜单查看和管理查询包。 选择一个查询包可查看和编辑其权限。 有关使用 API 创建查询包的详细信息，请参阅下文。

[![查看查询包](media/query-packs/view-query-pack.png)](media/query-packs/view-query-pack.png#lightbox)

## <a name="permissions"></a>权限
可以在 Azure 门户中查看查询包时设置其权限。 用户需要拥有以下权限才能使用查询包：

- 读取者 - 用户可以查看和运行查询包中的所有查询。
- **参与者** - 用户可以修改现有查询，以及在查询包中添加新查询。

## <a name="default-query-pack"></a>默认查询包
保存第一个查询时，将在名为 LogAnalyticsDefaultResources 的资源组中的每个订阅内自动创建一个名为 DefaultQueryPack 的查询包 。 可以在此查询包中创建查询，或者创建其他查询包，具体取决于你的需求。

## <a name="using-multiple-query-packs"></a>使用多个查询包
对于大多数用户而言，有一个默认查询包就足以能够保存和重复使用查询。 不过，出于一些原因（包括在不同的 Log Analytics 会话中加载不同的查询集，以及为不同的查询集合提供不同的权限），你可能需要为组织中的用户创建多个查询包。 

使用 API 创建新的查询包时，可以添加标记以便根据业务要求将查询分类。 例如，可以标记某个查询包，以将它关联到组织中的特定部门，或者关联到包含的查询所要解决的问题的严重性。 这样，便可以创建用于不同用户集和各种情况的不同查询集。

## <a name="query-pack-definition"></a>查询包定义
每个查询包在 JSON 文件中定义，该文件包含一个或多个查询的定义。 每个查询由一个块表示，如下所示：

```json
{
    "properties":
       {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        },
   }
}
```


## <a name="query-properties"></a>查询属性
查询包中的每个查询具有以下属性。


| 属性 | 说明 |
|:---|:---|
| displayName | Log Analytics 中为每个查询列出的显示名称。 | 
| description | Log Analytics 中为每个查询显示的查询说明。 |
| body        | 以 KQL 编写的查询。 |
| 相关     | 查询的相关类别、资源类型和解决方案。 供用户用于在 Log Analytics 中进行分组和筛选以帮助找到其查询。 每个查询中的每个类型最多可以有 10 个。 从 https://api.loganalytics.io/v1/metadata?select=resourceTypes,solutions,categories 中检索允许的值。 |
| 标记        | 供用户用于在 Log Analytics 中进行排序和筛选的附加标记。 [分组和筛选查询](queries.md#finding-and-filtering-queries)时，每个标记将添加到类别、资源类型和解决方案。 |




## <a name="create-a-query-pack"></a>创建查询包
目前只能使用 REST API 安装查询包。 

### <a name="create-token"></a>创建令牌
需使用一个令牌对 API 请求进行身份验证。 可通过多种方法获取令牌，包括使用 armclient。

首先使用以下命令登录到 Azure：

```
armclient login
```

然后使用以下命令创建令牌。 该令牌将自动复制到剪贴板，因此你可以将它粘贴到另一个工具中。

```
armclient token
```

### <a name="create-payload"></a>创建有效负载
请求的有效负载是用于定义一个或多个查询以及查询包要存储到的位置的 JSON。 查询包的名称在下一部分所述的 API 请求中指定。

```json
{
    "location": "eastus",
    "properties":
    {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        }
    }
}
```

### <a name="create-request"></a>创建请求
使用以下请求通过 REST API 创建新的查询包。 该请求应使用持有者令牌授权。 内容类型应是 application/json。

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack?api-version=2019-09-01-preview
```

通过一个可以提交 REST API 请求的工具（例如 Fiddler 或 Postman）使用上一部分所述的有效负载来提交请求。 将在有效负载中生成并返回查询 ID。 

## <a name="update-a-query-pack"></a>更新查询包
若要更新查询包，请使用更新的有效负载提交以下请求。 需在此命令中指定查询包 ID。

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack/queries/query-id/?api-version=2019-09-01-preview
```

## <a name="next-steps"></a>后续步骤

- 请参阅[在 Azure Monitor Log Analytics 中使用查询](queries.md)，了解用户如何在 Log Analytics 中与查询包交互。
