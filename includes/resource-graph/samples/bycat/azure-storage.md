---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 08/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bcef8c0aed68ed7169e650ab4a68567bc600e86f
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303157"
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

### <a name="list-all-storage-accounts-with-specific-tag-value"></a>列出具有特定标记值的所有存储帐户

组合前面示例的筛选功能，按“类型”属性筛选 Azure 资源类型。 此查询还使用特定的标记名称和值来限制对 Azure 资源特定类型的搜索。

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Storage%2fstorageAccounts%27%0a%7c%20where%20tags%5b%27tag%20with%20a%20space%27%5d%3d%3d%27Custom%20value%27" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Storage%2fstorageAccounts%27%0a%7c%20where%20tags%5b%27tag%20with%20a%20space%27%5d%3d%3d%27Custom%20value%27" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Storage%2fstorageAccounts%27%0a%7c%20where%20tags%5b%27tag%20with%20a%20space%27%5d%3d%3d%27Custom%20value%27" target="_blank">portal.azure.cn</a>

---

### <a name="show-resources-that-contain-storage"></a>显示包含存储的资源

此示例查询不显式定义要匹配的类型，而是查找 `contains` 单词“存储”的任何 Azure 资源。

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27storage%27%20%7c%20distinct%20type" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27storage%27%20%7c%20distinct%20type" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27storage%27%20%7c%20distinct%20type" target="_blank">portal.azure.cn</a>

---

