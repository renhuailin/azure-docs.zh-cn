---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 08/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7042200d62337aeaa3de28189758bc3bb8f48ba6
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861852"
---
### <a name="list-sql-databases-and-their-elastic-pools"></a>列出 SQL 数据库及其弹性池

以下查询使用 leftouter `join` 将 SQL 数据库资源及其相关弹性池组合在一起（如果有）。

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
    on elasticPoolId
| project-away elasticPoolId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a>

---

