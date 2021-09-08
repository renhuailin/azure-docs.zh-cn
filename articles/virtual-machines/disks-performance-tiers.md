---
title: 更改 Azure 托管磁盘的性能 - CLI/PowerShell
description: 了解如何使用 Azure PowerShell 模块或 Azure CLI 更改现有托管磁盘的性能层。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 783299359b1b7b9cbe75fd36f534ac18563c0fee
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102915"
---
# <a name="change-your-performance-tier-using-the-azure-powershell-module-or-the-azure-cli"></a>使用 Azure PowerShell 模块或 Azure CLI 更改性能层

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="prerequisites"></a>先决条件
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
安装最新的 [Azure CLI](/cli/azure/install-az-cli2) 并使用 [az login](/cli/azure/reference-index) 登录到 Azure 帐户。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
安装最新的 [Azure PowerShell 版本](/powershell/azure/install-az-ps)，并使用 `Connect-AzAccount` 登录到 Azure 帐户。

---

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>创建一个层级高于基线层的空数据磁盘
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>从 Azure 市场映像创建一个层级高于基线层的 OS 磁盘

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk-without-downtime"></a>在不停机的情况下更新磁盘的分层

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites"></a>先决条件

必须先为订阅启用该功能，然后才能在不停机的情况下更改磁盘的性能层。 以下步骤将为订阅启用该功能：

1.  执行以下命令，为订阅注册此功能

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  在试用该功能之前，请使用以下命令确认注册状态是否为“已注册”（可能需要几分钟）。

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

必须先为订阅启用该功能，然后才能在不停机的情况下更改磁盘的性能层。 以下步骤将为订阅启用该功能：

1.  执行以下命令，为订阅注册此功能

    ```azurepowershell
     Register-AzProviderFeature -FeatureName "LiveTierChange" -ProviderNamespace "Microsoft.Compute" 
    ```
 
1.  在试用该功能之前，请使用以下命令确认注册状态是否为“已注册”（可能需要几分钟）。

    ```azurepowershell
    Register-AzProviderFeature -FeatureName "LiveTierChange" -ProviderNamespace "Microsoft.Compute" 
    ```

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>显示磁盘层级

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="next-steps"></a>后续步骤

如果需要调整磁盘大小以利用更高的性能层，请参阅以下文章：

- [使用 Azure CLI 扩展 Linux VM 上的虚拟硬盘](linux/expand-disks.md)
- [展开附加到 Windows 虚拟机的托管磁盘](windows/expand-os-disk.md)
