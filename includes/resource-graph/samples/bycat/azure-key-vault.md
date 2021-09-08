---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 74451e0c2e969d458676edc236325207449948f9
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536187"
---
### <a name="count-key-vault-resources"></a>密钥保管库资源计数

此查询使用 `count` 而不是 `summarize` 来计算返回的记录数。 只有密钥保管库才包括在计数中。

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.keyvault%2fvaults%27%0a%7c%20count" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.keyvault%2fvaults%27%0a%7c%20count" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.keyvault%2fvaults%27%0a%7c%20count" target="_blank">portal.azure.cn</a>

---

### <a name="key-vaults-with-subscription-name"></a>具有订阅名称的密钥保管库

以下查询演示了 `join`（“类型”为“leftouter”）的复杂用法。 查询将联接表限制为订阅资源并具有 `project`，以仅包括原始字段 _SubscriptionId_ 和重命名为 _SubName_ 的 _name_ 字段。 字段重命名避免了 `join` 将其添加为 _name1_，因为该字段已存在于 _资源_ 中。 原始表使用 `where` 进行筛选，以下 `project` 包括两个表中的列。 查询结果是所有密钥保管库，其中显示密钥保管库的类型、名称以及其所在订阅的名称。

```kusto
Resources
| join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20join%20kind%3dleftouter%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20where%20type%20%3d%3d%20%27microsoft.keyvault%2fvaults%27%0a%7c%20project%20type%2c%20name%2c%20SubName" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20join%20kind%3dleftouter%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20where%20type%20%3d%3d%20%27microsoft.keyvault%2fvaults%27%0a%7c%20project%20type%2c%20name%2c%20SubName" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20join%20kind%3dleftouter%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20where%20type%20%3d%3d%20%27microsoft.keyvault%2fvaults%27%0a%7c%20project%20type%2c%20name%2c%20SubName" target="_blank">portal.azure.cn</a>

---

