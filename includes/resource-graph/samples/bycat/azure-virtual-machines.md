---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 08/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 716ac7b7d1c3b6577b9eb7290e249b306a617254
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123304087"
---
### <a name="count-of-os-update-installation-done"></a>已完成的 OS 更新安装的计数

返回过去 7 天内对计算机完成的 OS 更新安装运行的状态列表。

```kusto
PatchAssessmentResources
| where type !has 'softwarepatches'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount)
| where lTime > ago(7d)
| project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type !has 'softwarepatches' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount) | where lTime > ago(7d) | project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type !has 'softwarepatches' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount) | where lTime > ago(7d) | project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.cn</a>

---

### <a name="count-of-virtual-machines-by-availability-state-and-subscription-id"></a>按可用性状态和订阅 Id 统计虚拟机

返回根据每个订阅的可用性状态聚合的虚拟机（类型 `Microsoft.Compute/virtualMachines`）计数。

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---

### <a name="count-of-virtual-machines-by-power-state"></a>按电源状态统计的虚拟机计数

返回根据虚拟机的电源状态分类的虚拟机（类型`Microsoft.Compute/virtualMachines`的计数。 有关电源状态的详细信息，请参阅[电源状态概述](../../../../articles/virtual-machines/states-billing.md)。

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.cn</a>

---

### <a name="count-virtual-machines-by-os-type"></a>按 OS 类型对虚拟机进行计数

基于前面的查询，我们仍受限于类型 `Microsoft.Compute/virtualMachines` 的 Azure 资源，但不再限制返回的记录数量。 相反，我们使用 `summarize` 和 `count()` 来定义如何按属性对值进行分组和聚合，在此示例中为 `properties.storageProfile.osDisk.osType`。 有关此字符串在完整对象中的外观示例，请参阅[浏览资源 - 虚拟机发现](../../../../articles/governance/resource-graph/concepts/explore-resources.md#virtual-machine-discovery)。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a>

---

### <a name="count-virtual-machines-by-os-type-with-extend"></a>使用 extend 按 OS 类型对虚拟机进行计数

编写“按 OS 类型对虚拟机进行计数”查询的另一种方法是 `extend` 某个属性，并赋予其临时名称，以供查询使用。在本例中该属性为 os。 然后，os 由 `summarize` 和 `count()` 使用，如参考示例所示。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a>

---

### <a name="get-virtual-machine-scale-set-capacity-and-size"></a>获取虚拟机规模集容量和大小

此查询将查找虚拟机规模集资源，并获取各种详细信息，包括规模集的虚拟机大小和容量。 此查询使用 `toint()` 函数将容量强制转换为数字以供排序。 最后会将列重命名为自定义命名属性。

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-extensions-installed-on-a-virtual-machine"></a>列出虚拟机上安装的所有扩展

首先，此查询针对虚拟机资源类型使用 `extend` 以获取大写的 (`toupper()`) ID、获取操作系统名称和类型，以及获取虚拟机大小。 获取大写的资源 ID 是准备联接另一个属性的好方法。 然后，此查询将 `join` 用于为 leftouter 的 kind，通过匹配扩展 ID 的大写 `substring` 来获取虚拟机扩展。 “/extensions/\<ExtensionName\>”之前的 ID 部分与虚拟机 ID 的格式相同，因此我们将此属性用于 `join`。 然后会针对虚拟机扩展的名称将 `summarize` 用于 `make_list` 以合并每个扩展的名称，其中 id、OSName、OSType和 VMSize 在单个数组属性中是相同的   。 最后，我们通过 asc 对小写的 OSName 进行 `order by` 操作。 默认情况下，`order by` 为降序。

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| extend
    JoinID = toupper(id),
    OSName = tostring(properties.osProfile.computerName),
    OSType = tostring(properties.storageProfile.osDisk.osType),
    VMSize = tostring(properties.hardwareProfile.vmSize)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.compute/virtualmachines/extensions'
    | extend 
        VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),
        ExtensionName = name
) on $left.JoinID == $right.VMId
| summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize
| order by tolower(OSName) asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend  VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend  VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.cn</a>

---

### <a name="list-available-os-updates-for-all-your-machines-grouped-by-update-category"></a>列出按更新类别分组的所有计算机的可用 OS 更新

返回计算机的挂起 OS 的列表。

```kusto
PatchAssessmentResources
| where type !has 'softwarepatches'
| extend prop = parse_json(properties)
| extend lastTime = properties.lastModifiedDateTime
| extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType
| project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type !has 'softwarepatches' | extend prop = parse_json(properties) | extend lastTime = properties.lastModifiedDateTime | extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType | project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type !has 'softwarepatches' | extend prop = parse_json(properties) | extend lastTime = properties.lastModifiedDateTime | extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType | project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-linux-os-update-installation-done"></a>已完成的 Linux OS 更新安装的列表

返回过去 7 天内对计算机完成的 Linux Server - OS 更新安装运行的状态列表。

```kusto
PatchAssessmentResources
| where type has 'softwarepatches' and properties has 'version'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications)
| where lTime > ago(7d)
| project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState
| sort by RunID
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type has 'softwarepatches' and properties has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState | sort by RunID"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type has 'softwarepatches' and properties has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState | sort by RunID"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-and-associated-availability-states-by-resource-ids"></a>按资源 Id 列出的虚拟机和关联的可用性状态

返回按可用性状态聚合的最新虚拟机（类型 `Microsoft.Compute/virtualMachines`）列表。 该查询还提供了基于 `properties.targetResourceId` 的关联资源 Id，便于调试和缓解。 可用性状态可以是以下四个值之一：“可用”、“不可用”、“已降级”和“未知”。 有关每种可用性状态的详细信息，请参阅 [Azure 资源运行状况概述](../../../../articles/service-health/resource-health-overview.md#health-status)。

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups"></a>按可用性状态和电源状态列出的虚拟机（具有资源 ID 和资源组）

根据虚拟机的电源状态和可用性状态返回虚拟机（类型为 `Microsoft.Compute/virtualMachines`）列表，从而为虚拟机提供一致的运行状况状态。 该查询还提供与每个条目关联的资源组和资源 ID 的详细信息，以供详细了解资源。

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code)
| join kind=leftouter (
    HealthResources
    | where type =~ 'microsoft.resourcehealth/availabilitystatuses'
    | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines'
    | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState))
    on $left.Id == $right.targetResourceId
| project-away targetResourceId
| where PowerState != 'PowerState/deallocated'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code) | join kind=leftouter ( HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines' | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)) on $left.Id == $right.targetResourceId | project-away targetResourceId | where PowerState != 'PowerState/deallocated'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code) | join kind=leftouter ( HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines' | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)) on $left.Id == $right.targetResourceId | project-away targetResourceId | where PowerState != 'PowerState/deallocated'"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-that-are-not-available-by-resource-ids"></a>资源 ID 不可用的虚拟机列表

返回按可用性状态聚合的最新虚拟机（类型为 `Microsoft.Compute/virtualMachines`）列表。 填充的列表仅突出显示可用性状态不是“可用”的虚拟机，以确保你了解虚拟机的所有有关状态。 当所有虚拟机都可用时，预期不会收到任何结果。

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| where tostring(properties.availabilityState) != 'Available'
| summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.availabilityState) != 'Available' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.availabilityState) != 'Available' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-windows-server-os-update-installation-done"></a>已完成的 Windows Server OS 更新安装的列表

返回过去 7 天内对计算机完成的 Windows Server - OS 更新安装运行的状态列表。

```kusto
PatchAssessmentResources
| where type has 'softwarepatches' and properties !has 'version'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications)
| where lTime > ago(7d)
| project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState
| sort by RunID
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type has 'softwarepatches' and properties !has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState | sort by RunID"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type has 'softwarepatches' and properties !has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState | sort by RunID"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.cn</a>

---

### <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a>列出具有其网络接口和公共 IP 的虚拟机

此查询使用两个 **leftouter** `join` 命令将使用资源管理器部署模型创建的虚拟机、其相关网络接口以及与这些网络接口相关的任何公共 IP 地址组合在一起。

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces)
| mv-expand nic=properties.networkProfile.networkInterfaces
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic)
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id)
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations)
    | mv-expand ipconfig=properties.ipConfigurations
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
    on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a>

---

### <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a>按降序显示按名称排序的所有虚拟机

若要只列出虚拟机（类型为 `Microsoft.Compute/virtualMachines`），我们可在结果中匹配属性“类型”。 与上一查询类似，`desc` 将 `order by` 更改为降序。 类型匹配中的 `=~` 告知资源图表不区分大小写。

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type | where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type | where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a>按名称及其 OS 类型显示前五个虚拟机

此查询使用 `top`，仅检索按名称排序的五条匹配记录。 Azure 资源的类型为 `Microsoft.Compute/virtualMachines`。 `project` 告诉 Azure 资源图表要包含哪些属性。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="summarize-virtual-machine-by-the-power-states-extended-property"></a>按电源状态扩展属性汇总虚拟机

此查询使用虚拟机上的[扩展属性](../../../../articles/governance/resource-graph/concepts/query-language.md#extended-properties)按电源状态进行汇总。

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.cn</a>

---

### <a name="virtual-machines-matched-by-regex"></a>由正则表达式匹配的虚拟机

此查询查找与某个 [正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)（称为 _regex_）匹配的虚拟机。 可以使用 **matches regex \@** 定义要匹配的正则表达式，即 `^Contoso(.*)[0-9]+$`。 该 regex 定义说明如下：

- `^` - 匹配项必须以该字符串的开头开头。
- `Contoso` - 区分大小写的字符串。
- `(.*)` - 子表达式匹配项：
  - `.` - 匹配任何单一字符（换行符除外）。
  - `*` - 匹配上一个元素零次或多次。
- `[0-9]` - 数字 0 到 9 的字符组匹配项。
- `+` - 匹配上一个元素一次或多次。
- `$` - 上一个元素的匹配项必须出现在该字符串的末尾。

按名称进行匹配后，查询将对名称进行投影并按名称升序排序。

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

