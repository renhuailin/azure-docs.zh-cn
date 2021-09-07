---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 08/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 698120a0609e1c070348e5b0a6cf3c447bd0b16c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123308756"
---
### <a name="combine-results-from-two-queries-into-a-single-result"></a>将两个查询的结果合并为单个结果

以下查询使用 `union` 从 _ResourceContainers_ 表中获取结果，并将它们添加到 _Resources_ 表中的结果。

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type | limit 5 | union (Resources | project name, type | limit 5)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type | limit 5 | union (Resources | project name, type | limit 5)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%20%7c%20project%20name%2c%20type%20%20%7c%20limit%205%0a%7c%20union%20%20(Resources%20%7c%20project%20name%2c%20type%20%7c%20limit%205)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%20%7c%20project%20name%2c%20type%20%20%7c%20limit%205%0a%7c%20union%20%20(Resources%20%7c%20project%20name%2c%20type%20%7c%20limit%205)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%20%7c%20project%20name%2c%20type%20%20%7c%20limit%205%0a%7c%20union%20%20(Resources%20%7c%20project%20name%2c%20type%20%7c%20limit%205)" target="_blank">portal.azure.cn</a>

---

### <a name="count-of-subscriptions-per-management-group"></a>每个管理组的订阅计数

汇总每个管理组中的订阅计数。

```kusto
ResourceContainers
| where type =~ 'microsoft.management/managementgroups'
| project mgname = name
| join kind=leftouter (resourcecontainers | where type=~ 'microsoft.resources/subscriptions'
| extend  mgParent = properties.managementGroupAncestorsChain | project id, mgname = tostring(mgParent[0].name)) on mgname
| summarize count() by mgname
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type =~ 'microsoft.management/managementgroups' | project mgname = name | join kind=leftouter (resourcecontainers | where type=~ 'microsoft.resources/subscriptions' | extend mgParent = properties.managementGroupAncestorsChain | project id, mgname = tostring(mgParent[0].name)) on mgname | summarize count() by mgname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type =~ 'microsoft.management/managementgroups' | project mgname = name | join kind=leftouter (resourcecontainers | where type=~ 'microsoft.resources/subscriptions' | extend mgParent = properties.managementGroupAncestorsChain | project id, mgname = tostring(mgParent[0].name)) on mgname | summarize count() by mgname"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.management%2fmanagementgroups%27%0a%7c%20project%20mgname%20%3d%20name%0a%7c%20join%20kind%3dleftouter%20(resourcecontainers%20%7c%20where%20type%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.managementGroupAncestorsChain%20%7c%20project%20id%2c%20mgname%20%3d%20tostring(mgParent%5b0%5d.name))%20on%20mgname%0a%7c%20summarize%20count()%20by%20mgname" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.management%2fmanagementgroups%27%0a%7c%20project%20mgname%20%3d%20name%0a%7c%20join%20kind%3dleftouter%20(resourcecontainers%20%7c%20where%20type%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.managementGroupAncestorsChain%20%7c%20project%20id%2c%20mgname%20%3d%20tostring(mgParent%5b0%5d.name))%20on%20mgname%0a%7c%20summarize%20count()%20by%20mgname" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.management%2fmanagementgroups%27%0a%7c%20project%20mgname%20%3d%20name%0a%7c%20join%20kind%3dleftouter%20(resourcecontainers%20%7c%20where%20type%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.managementGroupAncestorsChain%20%7c%20project%20id%2c%20mgname%20%3d%20tostring(mgParent%5b0%5d.name))%20on%20mgname%0a%7c%20summarize%20count()%20by%20mgname" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-management-group-ancestors-for-a-specified-management-group"></a>为指定管理组列出所有管理组的上级

为[查询范围](../../../../articles/governance/resource-graph/concepts/query-language.md#query-scope)内指定的管理组提供管理组层次结构详细信息。 在此示例中，管理组名为 Application。

```kusto
ResourceContainers
| where type =~ 'microsoft.management/managementgroups'
| extend  mgParent = properties.details.managementGroupAncestorsChain
| mv-expand with_itemindex=MGHierarchy mgParent
| project name, properties.displayName, mgParent, MGHierarchy, mgParent.name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type =~ 'microsoft.management/managementgroups' | extend mgParent = properties.details.managementGroupAncestorsChain | mv-expand with_itemindex=MGHierarchy mgParent | project name, properties.displayName, mgParent, MGHierarchy, mgParent.name" --management-groups Application
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type =~ 'microsoft.management/managementgroups' | extend mgParent = properties.details.managementGroupAncestorsChain | mv-expand with_itemindex=MGHierarchy mgParent | project name, properties.displayName, mgParent, MGHierarchy, mgParent.name" -ManagementGroup Application
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.management%2fmanagementgroups%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.details.managementGroupAncestorsChain%0a%7c%20mv-expand%20with_itemindex%3dMGHierarchy%20mgParent%0a%7c%20project%20name%2c%20properties.displayName%2c%20mgParent%2c%20MGHierarchy%2c%20mgParent.name" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.management%2fmanagementgroups%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.details.managementGroupAncestorsChain%0a%7c%20mv-expand%20with_itemindex%3dMGHierarchy%20mgParent%0a%7c%20project%20name%2c%20properties.displayName%2c%20mgParent%2c%20MGHierarchy%2c%20mgParent.name" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.management%2fmanagementgroups%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.details.managementGroupAncestorsChain%0a%7c%20mv-expand%20with_itemindex%3dMGHierarchy%20mgParent%0a%7c%20project%20name%2c%20properties.displayName%2c%20mgParent%2c%20MGHierarchy%2c%20mgParent.name" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-management-group-ancestors-for-a-specified-subscription"></a>为指定订阅列出所有管理组的上级

为[查询范围](../../../../articles/governance/resource-graph/concepts/query-language.md#query-scope)内指定的订阅提供管理组层次结构详细信息。 在此示例中，订阅 GUID 为 11111111-1111-1111-1111-111111111111。

```kusto
ResourceContainers
| where type =~ 'microsoft.resources/subscriptions'
| extend  mgParent = properties.managementGroupAncestorsChain
| mv-expand with_itemindex=MGHierarchy mgParent
| project subscriptionId, name, mgParent, MGHierarchy, mgParent.name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type =~ 'microsoft.resources/subscriptions' | extend mgParent = properties.managementGroupAncestorsChain | mv-expand with_itemindex=MGHierarchy mgParent | project subscriptionId, name, mgParent, MGHierarchy, mgParent.name" --subscriptions 11111111-1111-1111-1111-111111111111
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type =~ 'microsoft.resources/subscriptions' | extend mgParent = properties.managementGroupAncestorsChain | mv-expand with_itemindex=MGHierarchy mgParent | project subscriptionId, name, mgParent, MGHierarchy, mgParent.name" -Subscription 11111111-1111-1111-1111-111111111111
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.managementGroupAncestorsChain%0a%7c%20mv-expand%20with_itemindex%3dMGHierarchy%20mgParent%0a%7c%20project%20subscriptionId%2c%20name%2c%20mgParent%2c%20MGHierarchy%2c%20mgParent.name" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.managementGroupAncestorsChain%0a%7c%20mv-expand%20with_itemindex%3dMGHierarchy%20mgParent%0a%7c%20project%20subscriptionId%2c%20name%2c%20mgParent%2c%20MGHierarchy%2c%20mgParent.name" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20extend%20%20mgParent%20%3d%20properties.managementGroupAncestorsChain%0a%7c%20mv-expand%20with_itemindex%3dMGHierarchy%20mgParent%0a%7c%20project%20subscriptionId%2c%20name%2c%20mgParent%2c%20MGHierarchy%2c%20mgParent.name" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-subscriptions-under-a-specified-management-group"></a>列出指定管理组下的所有订阅

提供在[查询范围](../../../../articles/governance/resource-graph/concepts/query-language.md#query-scope)指定的管理组下的所有订阅的名称和订阅 ID。 在此示例中，管理组名为 Application。

```kusto
ResourceContainers
| where type =~ 'microsoft.resources/subscriptions'
| project subscriptionId, name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type =~ 'microsoft.resources/subscriptions' | project subscriptionId, name" --management-groups Application
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type =~ 'microsoft.resources/subscriptions' | project subscriptionId, name" -ManagementGroup Application
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20project%20subscriptionId%2c%20name" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20project%20subscriptionId%2c%20name" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%27%0a%7c%20project%20subscriptionId%2c%20name" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-tags-and-their-values"></a>列出所有标记及其值

此查询会列出管理组、订阅和资源上的标记及其值。 查询首先仅查找带有 `isnotempty()` 标记的资源，通过仅包含 `project`、`mvexpand` 和 `extend` 中的标记来限制所包含的字段，从而获取属性包中配对的数据。 然后，它使用 `union` 将 ResourceContainers 中的结果合并到资源中的相同结果，从而广泛覆盖要提取的标记 。 最后，它将结果限制为 `distinct` 配对数据，并排除系统隐藏的标记。

```kusto
ResourceContainers
| where isnotempty(tags)
| project tags
| mvexpand tags
| extend tagKey = tostring(bag_keys(tags)[0])
| extend tagValue = tostring(tags[tagKey])
| union (
    resources
    | where isnotempty(tags)
    | project tags
    | mvexpand tags
    | extend tagKey = tostring(bag_keys(tags)[0])
    | extend tagValue = tostring(tags[tagKey])
)
| distinct tagKey, tagValue
| where tagKey !startswith "hidden-"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union ( resources | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union ( resources | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20isnotempty(tags)%0a%7c%20project%20tags%0a%7c%20mvexpand%20tags%0a%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a%7c%20union%20(%0a%09resources%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20project%20tags%0a%09%7c%20mvexpand%20tags%0a%09%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%09%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a)%0a%7c%20distinct%20tagKey%2c%20tagValue%0a%7c%20where%20tagKey%20!startswith%20%22hidden-%22" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20isnotempty(tags)%0a%7c%20project%20tags%0a%7c%20mvexpand%20tags%0a%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a%7c%20union%20(%0a%09resources%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20project%20tags%0a%09%7c%20mvexpand%20tags%0a%09%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%09%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a)%0a%7c%20distinct%20tagKey%2c%20tagValue%0a%7c%20where%20tagKey%20!startswith%20%22hidden-%22" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20isnotempty(tags)%0a%7c%20project%20tags%0a%7c%20mvexpand%20tags%0a%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a%7c%20union%20(%0a%09resources%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20project%20tags%0a%09%7c%20mvexpand%20tags%0a%09%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%09%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a)%0a%7c%20distinct%20tagKey%2c%20tagValue%0a%7c%20where%20tagKey%20!startswith%20%22hidden-%22" target="_blank">portal.azure.cn</a>

---

### <a name="remove-columns-from-results"></a>删除结果中的列

以下查询使用 `summarize` 按订阅对资源进行计数，使用 `join` 将其与 _ResourceContainers_ 表中的订阅详细信息合并，然后使用 `project-away` 删除某些列。

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.cn</a>

---

### <a name="secure-score-per-management-group"></a>每个管理组的安全功能分数

返回每个管理组的安全功能分数。

```kusto
SecurityResources
| where type == 'microsoft.security/securescores'
| project subscriptionId,
    subscriptionTotal = iff(properties.score.max == 0, 0.00, round(tolong(properties.weight) * todouble(properties.score.current)/tolong(properties.score.max),2)),
    weight = tolong(iff(properties.weight == 0, 1, properties.weight))
| join kind=leftouter (
    ResourceContainers
    | where type == 'microsoft.resources/subscriptions' and properties.state == 'Enabled'
    | project subscriptionId, mgChain=properties.managementGroupAncestorsChain )
    on subscriptionId
| mv-expand mg=mgChain
| summarize sumSubs = sum(subscriptionTotal), sumWeight = sum(weight), resultsNum = count() by tostring(mg.displayName), mgId = tostring(mg.name)
| extend secureScore = iff(tolong(resultsNum) == 0, 404.00, round(sumSubs/sumWeight*100,2))
| project mgName=mg_displayName, mgId, sumSubs, sumWeight, resultsNum, secureScore
| order by mgName asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "SecurityResources | where type == 'microsoft.security/securescores' | project subscriptionId, subscriptionTotal = iff(properties.score.max == 0, 0.00, round(tolong(properties.weight) * todouble(properties.score.current)/tolong(properties.score.max),2)), weight = tolong(iff(properties.weight == 0, 1, properties.weight)) | join kind=leftouter ( ResourceContainers | where type == 'microsoft.resources/subscriptions' and properties.state == 'Enabled' | project subscriptionId, mgChain=properties.managementGroupAncestorsChain ) on subscriptionId | mv-expand mg=mgChain | summarize sumSubs = sum(subscriptionTotal), sumWeight = sum(weight), resultsNum = count() by tostring(mg.displayName), mgId = tostring(mg.name) | extend secureScore = iff(tolong(resultsNum) == 0, 404.00, round(sumSubs/sumWeight*100,2)) | project mgName=mg_displayName, mgId, sumSubs, sumWeight, resultsNum, secureScore | order by mgName asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "SecurityResources | where type == 'microsoft.security/securescores' | project subscriptionId, subscriptionTotal = iff(properties.score.max == 0, 0.00, round(tolong(properties.weight) * todouble(properties.score.current)/tolong(properties.score.max),2)), weight = tolong(iff(properties.weight == 0, 1, properties.weight)) | join kind=leftouter ( ResourceContainers | where type == 'microsoft.resources/subscriptions' and properties.state == 'Enabled' | project subscriptionId, mgChain=properties.managementGroupAncestorsChain ) on subscriptionId | mv-expand mg=mgChain | summarize sumSubs = sum(subscriptionTotal), sumWeight = sum(weight), resultsNum = count() by tostring(mg.displayName), mgId = tostring(mg.name) | extend secureScore = iff(tolong(resultsNum) == 0, 404.00, round(sumSubs/sumWeight*100,2)) | project mgName=mg_displayName, mgId, sumSubs, sumWeight, resultsNum, secureScore | order by mgName asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/SecurityResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fsecurescores%27%0a%7c%20project%20subscriptionId%2c%0a%09subscriptionTotal%20%3d%20iff(properties.score.max%20%3d%3d%200%2c%200.00%2c%20round(tolong(properties.weight)%20*%20todouble(properties.score.current)%2ftolong(properties.score.max)%2c2))%2c%0a%09weight%20%3d%20tolong(iff(properties.weight%20%3d%3d%200%2c%201%2c%20properties.weight))%0a%7c%20join%20kind%3dleftouter%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.resources%2fsubscriptions%27%20and%20properties.state%20%3d%3d%20%27Enabled%27%0a%09%7c%20project%20subscriptionId%2c%20mgChain%3dproperties.managementGroupAncestorsChain%20)%0a%09on%20subscriptionId%0a%7c%20mv-expand%20mg%3dmgChain%0a%7c%20summarize%20sumSubs%20%3d%20sum(subscriptionTotal)%2c%20sumWeight%20%3d%20sum(weight)%2c%20resultsNum%20%3d%20count()%20by%20tostring(mg.displayName)%2c%20mgId%20%3d%20tostring(mg.name)%0a%7c%20extend%20secureScore%20%3d%20iff(tolong(resultsNum)%20%3d%3d%200%2c%20404.00%2c%20round(sumSubs%2fsumWeight*100%2c2))%0a%7c%20project%20mgName%3dmg_displayName%2c%20mgId%2c%20sumSubs%2c%20sumWeight%2c%20resultsNum%2c%20secureScore%0a%7c%20order%20by%20mgName%20asc" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/SecurityResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fsecurescores%27%0a%7c%20project%20subscriptionId%2c%0a%09subscriptionTotal%20%3d%20iff(properties.score.max%20%3d%3d%200%2c%200.00%2c%20round(tolong(properties.weight)%20*%20todouble(properties.score.current)%2ftolong(properties.score.max)%2c2))%2c%0a%09weight%20%3d%20tolong(iff(properties.weight%20%3d%3d%200%2c%201%2c%20properties.weight))%0a%7c%20join%20kind%3dleftouter%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.resources%2fsubscriptions%27%20and%20properties.state%20%3d%3d%20%27Enabled%27%0a%09%7c%20project%20subscriptionId%2c%20mgChain%3dproperties.managementGroupAncestorsChain%20)%0a%09on%20subscriptionId%0a%7c%20mv-expand%20mg%3dmgChain%0a%7c%20summarize%20sumSubs%20%3d%20sum(subscriptionTotal)%2c%20sumWeight%20%3d%20sum(weight)%2c%20resultsNum%20%3d%20count()%20by%20tostring(mg.displayName)%2c%20mgId%20%3d%20tostring(mg.name)%0a%7c%20extend%20secureScore%20%3d%20iff(tolong(resultsNum)%20%3d%3d%200%2c%20404.00%2c%20round(sumSubs%2fsumWeight*100%2c2))%0a%7c%20project%20mgName%3dmg_displayName%2c%20mgId%2c%20sumSubs%2c%20sumWeight%2c%20resultsNum%2c%20secureScore%0a%7c%20order%20by%20mgName%20asc" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/SecurityResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fsecurescores%27%0a%7c%20project%20subscriptionId%2c%0a%09subscriptionTotal%20%3d%20iff(properties.score.max%20%3d%3d%200%2c%200.00%2c%20round(tolong(properties.weight)%20*%20todouble(properties.score.current)%2ftolong(properties.score.max)%2c2))%2c%0a%09weight%20%3d%20tolong(iff(properties.weight%20%3d%3d%200%2c%201%2c%20properties.weight))%0a%7c%20join%20kind%3dleftouter%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.resources%2fsubscriptions%27%20and%20properties.state%20%3d%3d%20%27Enabled%27%0a%09%7c%20project%20subscriptionId%2c%20mgChain%3dproperties.managementGroupAncestorsChain%20)%0a%09on%20subscriptionId%0a%7c%20mv-expand%20mg%3dmgChain%0a%7c%20summarize%20sumSubs%20%3d%20sum(subscriptionTotal)%2c%20sumWeight%20%3d%20sum(weight)%2c%20resultsNum%20%3d%20count()%20by%20tostring(mg.displayName)%2c%20mgId%20%3d%20tostring(mg.name)%0a%7c%20extend%20secureScore%20%3d%20iff(tolong(resultsNum)%20%3d%3d%200%2c%20404.00%2c%20round(sumSubs%2fsumWeight*100%2c2))%0a%7c%20project%20mgName%3dmg_displayName%2c%20mgId%2c%20sumSubs%2c%20sumWeight%2c%20resultsNum%2c%20secureScore%0a%7c%20order%20by%20mgName%20asc" target="_blank">portal.azure.cn</a>

---

