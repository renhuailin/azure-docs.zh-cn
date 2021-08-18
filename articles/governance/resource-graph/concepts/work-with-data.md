---
title: 处理大型数据集
description: 了解如何在使用 Azure Resource Graph 的同时，在大型数据集中获取、格式化、分页和跳过记录。
ms.date: 05/17/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 6bbb19bc11344c77707caf2df19d5e9369161c3a
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112320885"
---
# <a name="working-with-large-azure-resource-data-sets"></a>处理大型 Azure 资源数据集

Azure Resource Graph 旨在处理并获取 Azure 环境中资源的相关信息。 Resource Graph 加快了获取此类数据的速度，即使在查询数千条记录，也不例外。 Resource Graph 提供了多个大型数据集处理选项。

若要了解如何频繁地执行查询，请参阅[针对受限制请求的指南](./guidance-for-throttled-requests.md)。

## <a name="data-set-result-size"></a>数据集结果大小

默认情况下，Resource Graph 限制任何查询都只能返回 100 条记录。 这项控制措施可保护用户和服务不受会生成大型数据集的意外查询影响。 当客户尝试通过查询来按照能满足自己特定需求的方式查找和筛选资源时，这种情况最为常见。 这项控制措施不同于使用 [top](/azure/kusto/query/topoperator) 或 [limit](/azure/kusto/query/limitoperator) Azure 数据资源管理器语言运算符来限制结果。

> [!NOTE]
> 使用 First 时，建议用 `asc` 或 `desc` 按至少一个列对结果进行排序。 如果不排序，则返回的结果是随机的且不可重复。

通过与 Resource Graph 交互的所有方法，都可以替代默认限制。 下面的示例展示了如何将数据集大小限制更改为 200：

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

在 [REST API](/rest/api/azureresourcegraph/resourcegraph(2021-03-01)/resources/resources) 中，控制措施是 $top，它属于 QueryRequestOptions。

最具限制性的控制措施将胜出。 例如，如果查询使用 top 或 limit 运算符，并生成多于 First 的记录，那么返回的记录数上限等于 First。 同样，如果 top 或 limit 小于 First，那么返回的记录集小于 top 或 limit 配置的值。

First 当前允许的最大值为 5000，这是通过一次对 1000 条记录进行[结果分页](#paging-results)来实现的 。

> [!IMPORTANT]
> 当 First 配置为大于 1000 条记录时，查询必须对 ID 字段进行投射才能使分页生效 。 如果查询未进行投射，则响应不会进行[分页](#paging-results)，且结果将限制为 1000 条记录。

## <a name="skipping-records"></a>跳过记录

下一个大型数据集处理选项是 Skip 控制措施。 通过这项控制措施，查询可以在返回结果之前跳过或略过定义的记录数。 Skip 适用于以一种有意义的方式对结果进行排序的查询，查询意图是在结果集中间某位置处获取记录。 如果所需的结果位于返回数据集的末尾，更高效的做法是使用不同的排序配置，并从数据集顶部检索结果。

> [!NOTE]
> 使用 Skip 时，建议用 `asc` 或 `desc` 按至少一个列对结果进行排序。 如果不排序，则返回的结果是随机的且不可重复。 如果在查询中使用 `limit` 或 `take`，则忽略“跳过”。

下面的示例展示了如何跳过查询生成的前 10 条记录，改从第 11 条记录开始返回结果集：

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

在 [REST API](/rest/api/azureresourcegraph/resourcegraph(2021-03-01)/resources/resources) 中，控制措施是 $skip，它属于 QueryRequestOptions。

## <a name="paging-results"></a>分页结果

如果有必要将结果集拆分为更小的记录集进行处理，或者因为结果集会超过允许的最大返回记录数（即 1000 条），请使用分页。 [REST API](/rest/api/azureresourcegraph/resourcegraph(2021-03-01)/resources/resources)
**QueryResponse** 提供了指明结果集已被拆分的值：**resultTruncated** 和 **$skipToken**。 resultTruncated 是布尔值，用于告知使用者返回的响应中是否还有其他记录。 如果 count 属性小于 totalRecords 属性，也可以确定此条件。 totalRecords 定义匹配查询的记录数。

如果由于没有 `id` 列而禁用了分页或无法进行分页，或者可用资源少于查询请求的资源，则 resultTruncated 为 true。 如果 resultTruncated 为 true，则不会设置 $skipToken 属性。

以下示例演示了如何使用 Azure CLI 和 Azure PowerShell 跳过前 3,000 条记录，并返回跳过这些记录之后的前 1,000 条记录 ：

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> 查询必须投射 ID 字段，这样分页才能生效。 如果查询中缺少 ID 字段，则响应中不包含 $skipToken。

有关示例，请参阅 REST API 文档中的[下一页查询](/rest/api/azureresourcegraph/resourcegraph(2021-03-01)/resources/resources#next-page-query)。

## <a name="formatting-results"></a>设置结果的格式

Resource Graph 查询的结果以两种格式提供：Table 和 ObjectArray 。 可使用 resultFormat 参数配置格式，该参数是请求选项一部分。 Table 格式是 resultFormat 的默认值。

默认情况下，来自 Azure CLI 的结果以 JSON 的形式提供。 Azure PowerShell 中的结果是一个 PSResourceGraphResponse 对象，但可以根据 Data 属性使用 `ConvertTo-Json` cmdlet 将它们快速转换为 JSON。 对于其他 SDK，可将查询结果配置为输出 ObjectArray 格式。

### <a name="format---table"></a>格式 - Table

Table 是默认格式，它以 JSON 格式返回结果，旨在突出显示查询所返回的属性的列设计和行值。 该格式与结构化表或电子表格中定义的数据很类似，其中首先标识列，然后标识表示与这些列相对应的数据的行。

下面是一个使用 Table 格式设置的查询结果示例：

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>格式 - ObjectArray

ObjectArray 格式也以 JSON 格式返回结果。 但是，这种设计与 JSON 中常见的键/值对关系保持一致，其中列和行数据在数组组中匹配。

下面是使用 ObjectArray 格式设置的查询结果示例：

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

下面是一些设置 resultFormat 以使用 ObjectArray 格式的示例：

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>后续步骤

- 请参阅[初学者查询](../samples/starter.md)中使用的语言。
- 请参阅[高级查询](../samples/advanced.md)中的高级用法。
- 详细了解如何[浏览资源](explore-resources.md)。
