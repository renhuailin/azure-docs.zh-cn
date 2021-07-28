---
title: SKU 不可用错误
description: 介绍在使用 Azure 资源管理器部署资源时如何解决 SKU 不可用错误。
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2ed04cd3839b8146d7e988b571113a737f38801f
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108318722"
---
# <a name="resolve-errors-for-sku-not-available"></a>解决 SKU 不可用的错误

本文介绍如何解决 SkuNotAvailable 错误  。 如果在该区域或满足业务需求的备用区域中找不到合适的 SKU，请将 [SKU 请求](/troubleshoot/azure/general/region-access-request-process)提交到 Azure 支持。

## <a name="symptom"></a>症状

在部署资源（通常为虚拟机）时，收到以下错误代码和错误消息：

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>原因

当所选的资源 SKU（如 VM 大小）不可用于所选的位置时，会收到此错误。

如果要部署 Azure 现成 VM 或现成规模集实例，那么此位置没有 Azure 现成虚拟机的任何容量。 有关详细信息，请参阅[现成虚拟机错误消息](../../virtual-machines/error-codes-spot.md)。

## <a name="solution-1---powershell"></a>解决方案 1 - PowerShell

要确定区域中可用 SKU，请使用 [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 命令。 按位置筛选结果。 必须拥有最新版本 PowerShell 才能运行此命令。

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

结果包括位置的 SKU 列表以及针对该 SKU 的任何限制。 请注意，SKU 可能被列为 `NotAvailableForSubscription`。

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

若要按位置和 SKU 进行筛选，请使用：

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

该命令将返回类似下面的结果：

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>解决方案 2 - Azure CLI

要确定区域中可用的 SKU，请使用 [az vm list-skus](/cli/azure/vm#az_vm_list_skus) 命令。 使用 `--location` 参数可按位置筛选输出。 使用 `--size` 参数按部分大小名称搜索。 使用 `--all` 参数显示所有信息，包括对当前订阅不可用的大小。

必须具备 Azure CLI 2.15.0 或更高版本。 要检查版本，请使用 `az --version`。 如果需要，请[更新安装](/cli/azure/update-azure-cli)。

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

该命令将返回类似下面的结果：

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>解决方案 3 - Azure 门户

要确定区域中可用的 SKU，请使用[门户](https://portal.azure.com)。 登录到门户，并通过接口添加资源。 设置值时，可看到该资源的可用 SKU。 不需要完成部署。

例如，开始创建虚拟机的过程。 若要查看其他可用的大小，请选择“更改大小”  。

![创建 VM](./media/error-sku-not-available/create-vm.png)

可以筛选和滚动到可用的大小。

![可用的 SKU](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>解决方案 4 - REST

要确定区域中可用的 SKU，请使用[资源 Sku - 列表](/rest/api/compute/resourceskus/list)操作。

它会以下列格式返回可用 SKU 和区域：

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```
