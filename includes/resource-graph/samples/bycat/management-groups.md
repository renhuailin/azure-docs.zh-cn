---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90fa4736246be2fabc8797a0e77481647ea7c013
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536239"
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

