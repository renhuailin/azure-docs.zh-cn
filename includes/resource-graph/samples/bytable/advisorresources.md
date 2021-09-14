---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c7bf4e3fb3f5584a72435ed7ef23f76afdebde6e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535846"
---
### <a name="get-cost-savings-summary-from-azure-advisor"></a>从 Azure 顾问获取成本节约情况摘要

此查询汇总了每个 [Azure 顾问](../../../../articles/advisor/advisor-overview.md)建议的成本节约情况。

```kusto
AdvisorResources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'Cost'
| extend
    resources = tostring(properties.resourceMetadata.resourceId),
    savings = todouble(properties.extendedProperties.savingsAmount),
    solution = tostring(properties.shortDescription.solution),
    currency = tostring(properties.extendedProperties.savingsCurrency)
| summarize
    dcount(resources),
    bin(sum(savings), 0.01)
    by solution, currency
| project solution, dcount_resources, sum_savings, currency
| order by sum_savings desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "AdvisorResources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "AdvisorResources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27Cost%27%0a%7c%20extend%0a%09resources%20%3d%20tostring(properties.resourceMetadata.resourceId)%2c%0a%09savings%20%3d%20todouble(properties.extendedProperties.savingsAmount)%2c%0a%09solution%20%3d%20tostring(properties.shortDescription.solution)%2c%0a%09currency%20%3d%20tostring(properties.extendedProperties.savingsCurrency)%0a%7c%20summarize%0a%09dcount(resources)%2c%0a%09bin(sum(savings)%2c%200.01)%0a%09by%20solution%2c%20currency%0a%7c%20project%20solution%2c%20dcount_resources%2c%20sum_savings%2c%20currency%0a%7c%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27Cost%27%0a%7c%20extend%0a%09resources%20%3d%20tostring(properties.resourceMetadata.resourceId)%2c%0a%09savings%20%3d%20todouble(properties.extendedProperties.savingsAmount)%2c%0a%09solution%20%3d%20tostring(properties.shortDescription.solution)%2c%0a%09currency%20%3d%20tostring(properties.extendedProperties.savingsCurrency)%0a%7c%20summarize%0a%09dcount(resources)%2c%0a%09bin(sum(savings)%2c%200.01)%0a%09by%20solution%2c%20currency%0a%7c%20project%20solution%2c%20dcount_resources%2c%20sum_savings%2c%20currency%0a%7c%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27Cost%27%0a%7c%20extend%0a%09resources%20%3d%20tostring(properties.resourceMetadata.resourceId)%2c%0a%09savings%20%3d%20todouble(properties.extendedProperties.savingsAmount)%2c%0a%09solution%20%3d%20tostring(properties.shortDescription.solution)%2c%0a%09currency%20%3d%20tostring(properties.extendedProperties.savingsCurrency)%0a%7c%20summarize%0a%09dcount(resources)%2c%0a%09bin(sum(savings)%2c%200.01)%0a%09by%20solution%2c%20currency%0a%7c%20project%20solution%2c%20dcount_resources%2c%20sum_savings%2c%20currency%0a%7c%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="list-arc-enabled-servers-not-running-latest-released-agent-version"></a>列出未运行代理的最新发布版且已启用 Arc 的服务器

此查询返回所有运行 Connected Machine 代理过时版本且已启用 Arc 的服务器。 结果中不包含状态为“已过期”的代理。 该查询使用 leftouter `join` 将顾问提出的关于任何已确定为过时的 Connected Machine 代理的建议和混合计算机组合在一起，以筛选出一段时间内未与 Azure 通信的任何代理。

```kusto
AdvisorResources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'HighAvailability'
| where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent'
| project
        id,
        JoinId = toupper(properties.resourceMetadata.resourceId),
        machineName = tostring(properties.impactedValue),
        agentVersion = tostring(properties.extendedProperties.installedVersion),
        expectedVersion = tostring(properties.extendedProperties.latestVersion)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.hybridcompute/machines'
    | project
        machineId = toupper(id),
        status = tostring (properties.status)
    ) on $left.JoinId == $right.machineId
| where status != 'Expired'
| summarize by id, machineName, agentVersion, expectedVersion
| order by tolower(machineName) asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "AdvisorResources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'HighAvailability' | where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent' | project  id,  JoinId = toupper(properties.resourceMetadata.resourceId),  machineName = tostring(properties.impactedValue),  agentVersion = tostring(properties.extendedProperties.installedVersion),  expectedVersion = tostring(properties.extendedProperties.latestVersion) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines' | project  machineId = toupper(id),  status = tostring (properties.status) ) on $left.JoinId == $right.machineId | where status != 'Expired' | summarize by id, machineName, agentVersion, expectedVersion | order by tolower(machineName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "AdvisorResources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'HighAvailability' | where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent' | project  id,  JoinId = toupper(properties.resourceMetadata.resourceId),  machineName = tostring(properties.impactedValue),  agentVersion = tostring(properties.extendedProperties.installedVersion),  expectedVersion = tostring(properties.extendedProperties.latestVersion) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines' | project  machineId = toupper(id),  status = tostring (properties.status) ) on $left.JoinId == $right.machineId | where status != 'Expired' | summarize by id, machineName, agentVersion, expectedVersion | order by tolower(machineName) asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.cn</a>

---

