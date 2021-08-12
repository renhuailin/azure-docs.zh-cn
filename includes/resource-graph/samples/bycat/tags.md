---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 07/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e00431dc428e62850a105920810fdc2f2d3806d6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442150"
---
### <a name="find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group"></a>在资源组上查找具有不区分大小写的特定标记的存储帐户

类似于“在资源组上查找具有区分大小写的特定标记的存储帐户”查询，但是当必须要查找不区分大小写的标记名称和标记值时，请将 `mv-expand` 与 bagexpansion 参数一起使用。 此查询使用的配额高于原始查询，因此只在必要的时候使用 `mv-expand`。

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | mv-expand bagexpansion=array tags
    | where isnotempty(tags)
    | where tags[0] =~ 'key1' and tags[1] =~ 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20mv-expand%20bagexpansion%3darray%20tags%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20where%20tags%5b0%5d%20%3d%7e%20%27key1%27%20and%20tags%5b1%5d%20%3d%7e%20%27value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20mv-expand%20bagexpansion%3darray%20tags%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20where%20tags%5b0%5d%20%3d%7e%20%27key1%27%20and%20tags%5b1%5d%20%3d%7e%20%27value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20mv-expand%20bagexpansion%3darray%20tags%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20where%20tags%5b0%5d%20%3d%7e%20%27key1%27%20and%20tags%5b1%5d%20%3d%7e%20%27value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.cn</a>

---

### <a name="find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group"></a>在资源组上查找具有区分大小写的特定标记的存储帐户

以下查询使用 inner `join` 将存储帐户连接到具有指定标记名称和标记值（区分大小写）的资源组。

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['Key1'] =~ 'Value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20where%20tags%5b%27Key1%27%5d%20%3d%7e%20%27Value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20where%20tags%5b%27Key1%27%5d%20%3d%7e%20%27Value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20where%20tags%5b%27Key1%27%5d%20%3d%7e%20%27Value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-tag-names"></a>列出所有标记名称

此查询以标记开头，并生成一个 JSON 对象，列出所有唯一标记名称及其对应的类型。

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20tags%0a%7c%20summarize%20buildschema(tags)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20tags%0a%7c%20summarize%20buildschema(tags)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20tags%0a%7c%20summarize%20buildschema(tags)" target="_blank">portal.azure.cn</a>

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

### <a name="list-resources-with-a-specific-tag-value"></a>列出具有特定标记值的资源

我们可通过 Azure 资源类型以外的属性（如标记）来限制结果。 在此示例中，我们正在筛选 Azure 资源，其标记名为“环境”，其值为 Internal 。 如果还要提供资源具有的标记及其值，请将属性“标记”添加到 `project` 关键字。

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20tags.environment%3d%7e%27internal%27%0a%7c%20project%20name%2c%20tags" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20tags.environment%3d%7e%27internal%27%0a%7c%20project%20name%2c%20tags" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20tags.environment%3d%7e%27internal%27%0a%7c%20project%20name%2c%20tags" target="_blank">portal.azure.cn</a>

---

