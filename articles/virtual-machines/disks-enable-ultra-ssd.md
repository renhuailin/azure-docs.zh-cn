---
title: VM 的超级磁盘 - Azure 托管磁盘
description: 了解适用于 Azure VM 的超级磁盘
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/17/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: fb6180754be5e94091e5ff1ca21823b85c8a6e6d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322010"
---
# <a name="using-azure-ultra-disks"></a>使用 Azure 超级磁盘

本文介绍如何部署和使用超级磁盘，有关超级磁盘的概念性信息，请参阅 [Azure 中有哪些可用的磁盘类型？](disks-types.md#ultra-disk)。

Azure 超级磁盘为 Azure IaaS 虚拟机 (VM) 提供高吞吐量、高 IOPS 和一致且低延迟的磁盘存储。 此新产品提供出类拔萃的性能，其可用性级别与我们的现有磁盘产品相同。 超级磁盘的一个主要优点是能够动态更改 SSD 的性能和工作负载，而无需重启 VM。 超级磁盘适用于 SAP HANA、顶层数据库等数据密集型工作负荷，以及事务密集型工作负荷。

## <a name="ga-scope-and-limitations"></a>正式发布版的范围和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>确定 VM 大小和区域可用性

### <a name="vms-using-availability-zones"></a>使用可用性区域的 VM

若要利用超级磁盘，需要确定你位于哪个可用性区域中。 并非每个区域都支持每一种使用超级磁盘的 VM 大小。 若要确定地区、区域和 VM 大小是否支持超级磁盘，请运行以下任一命令，确保首先替换“region”、“vmSize”和“subscription”值  ：

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

响应将会类似于以下形式，其中 X 为用于在所选地区进行部署的区域。 X 可能是1、2 或 3。

预留“区域”值，它表示可用性区域，部署超级磁盘时将会需要该值。

|ResourceType  |名称  |位置  |区域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 如果命令没有响应，则说明所选区域中的超级磁盘不支持所选 VM 大小。

现在，你已了解要部署到哪个区域，请按照本文中的部署步骤，部署附加超级磁盘的 VM，或将超级磁盘附加到某个现有的 VM。

### <a name="vms-with-no-redundancy-options"></a>没有冗余选项的 VM

目前，在所选区域中部署的超级磁盘在部署时不可使用任何冗余选项。 但是，并不是每一种支持超级磁盘的磁盘大小都可以在这些区域中。 若要确定哪些磁盘大小支持超级磁盘，可以使用以下任一代码片段。 请确保先替换 `vmSize` 和 `subscription` 值：

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

响应将会类似于以下形式，`UltraSSDAvailable   True` 指示 VM 大小是否支持此区域中的超级磁盘。

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>使用 Azure 资源管理器部署超级磁盘

首先，确定要部署的 VM 大小。 有关受支持的 VM 大小的列表，请参阅[正式发布版的范围和限制](#ga-scope-and-limitations)部分。

如果需要创建具有多个超级磁盘的 VM，请参考示例[创建具有多个超级磁盘的 VM](https://aka.ms/ultradiskArmTemplate)。

如果要使用自己的模板，请确保将 `Microsoft.Compute/virtualMachines` 和 `Microsoft.Compute/Disks` 的“apiVersion”设置为“`2018-06-01`”（或更高版本）。

将磁盘 sku 设置为“UltraSSD_LRS”，然后设置磁盘容量、IOPS、可用性区域和吞吐量（以 MBps 为单位），以创建超级磁盘。

在预配 VM 后，可以对数据磁盘进行分区和格式设置并为工作负荷配置这些磁盘。


## <a name="deploy-an-ultra-disk"></a>部署超级磁盘

# <a name="portal"></a>[Portal](#tab/azure-portal)

本部分介绍如何部署配备超级磁盘作为数据磁盘的虚拟机。 假定你已熟悉如何部署虚拟机，如果你尚不熟悉，请参阅我们的[快速入门：在 Azure 门户中创建 Windows 虚拟机](./windows/quick-create-portal.md)。

1. 登录到 [Azure 门户](https://portal.azure.com/)并导航到部署虚拟机 (VM)。
1. 确保选择[受支持的 VM 大小和区域](#ga-scope-and-limitations)。
1. 选择“可用性选项”中的“可用性区域” 。
1. 按你的选择填写其余条目。
1. 选择“磁盘”。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png" alt-text="VM 创建流的屏幕截图，“基本信息”边栏选项卡。" lightbox="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png":::

1. 在“磁盘”边栏选项卡上，为“启用超级磁盘兼容性”选择“是” 。
1. 选择“创建并附加新磁盘”，以立即附加超级磁盘。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-disk-enable.png" alt-text="VM 创建流（“磁盘”边栏选项卡）的屏幕截图，其中突出显示了“启用超级磁盘兼容性”和“创建并附加新磁盘”。" :::

1. 在“创建新磁盘”边栏选项卡上，输入名称，然后选择“更改大小” 。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-create-disk.png" alt-text="“创建新磁盘”边栏选项卡的屏幕截图，其中突出显示了“更改大小”。":::


1. 将“磁盘 SKU”更改为“超级磁盘” 。
1. 将“自定义磁盘大小 (GiB)”、“磁盘 IOPS”和“磁盘吞吐量”的值更改为所选值  。
1. 在两个边栏选项卡中都选择“确定”。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-select-ultra-disk-size.png" alt-text="“选择磁盘大小”边栏选项卡的屏幕截图，为存储类型选择了超级磁盘，突出显示了其他值。":::

1. 继续执行 VM 部署，该过程与部署任何其他 VM 相同。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，确定要部署的 VM 大小。 请参阅[正式发布版的范围和限制](#ga-scope-and-limitations)部分，以获取受支持的 VM 大小的列表。

若要附加超级磁盘，必须创建能够使用超级磁盘的 VM。

用你自己的值替换或设置“$vmname”、“$rgname”、“$diskname”、“$location”、“$password”、“$user”变量     。 将“$zone”设置为从[本文开头](#determine-vm-size-and-region-availability)获取的可用性区域的值。 然后运行以下 CLI 命令，以创建启用了超级磁盘的 VM：

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，确定要部署的 VM 大小。 请参阅[正式发布版的范围和限制](#ga-scope-and-limitations)部分，以获取受支持的 VM 大小的列表。

若要使用超级磁盘，必须创建能够使用超级磁盘的 VM。 用你自己的值替换或设置“$resourcegroup”和“$vmName”变量 。 将“$zone”设置为从[本文开头](#determine-vm-size-and-region-availability)获取的可用性区域的值。 然后运行以下 [New-AzVm](/powershell/module/az.compute/new-azvm) 命令，以创建启用了超级磁盘的 VM：

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>创建和附加磁盘

在部署了 VM 之后，即可创建超级磁盘并将它附加到该 VM，请使用以下脚本：

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---

## <a name="deploy-an-ultra-disk---512-byte-sector-size"></a>部署超级磁盘 - 512 字节扇区大小

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)，然后搜索并选择“磁盘”。
1. 选择“+新建”以创建新磁盘。
1. 选择支持超级磁盘的地区并选择可用性区域，根据需要填写其余值。
1. 选择“更改大小”。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/create-managed-disk-basics-workflow.png" alt-text="“创建磁盘”边栏选项卡的屏幕截图，其中突出显示了地区、可用性区域和更改大小。":::

1. 对于“磁盘 SKU”，请选择“超级磁盘”，然后根据所需性能填写值并选择“确定”  。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-disk-size-ultra.png" alt-text="创建超级磁盘的屏幕截图。":::

1. 在“基本信息”边栏选项卡中，选择“高级”选项卡 。
1. 为“逻辑扇区大小”选择“512”，然后选择“查看 + 创建”  。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-different-sector-size-ultra.png" alt-text="用于将超级磁盘逻辑扇区大小更改为 512 的选择器的屏幕截图。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，确定要部署的 VM 大小。 请参阅[正式发布版的范围和限制](#ga-scope-and-limitations)部分，以获取受支持的 VM 大小的列表。

若要附加超级磁盘，必须创建能够使用超级磁盘的 VM。

用你自己的值替换或设置“$vmname”、“$rgname”、“$diskname”、“$location”、“$password”、“$user”变量     。 将“$zone”设置为从[本文开头](#determine-vm-size-and-region-availability)获取的可用性区域的值。 然后运行以下 CLI 命令，以创建具有扇区大小为 512 字节的超级磁盘的 VM：

```azurecli
#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，确定要部署的 VM 大小。 请参阅[正式发布版的范围和限制](#ga-scope-and-limitations)部分，以获取受支持的 VM 大小的列表。

若要使用超级磁盘，必须创建能够使用超级磁盘的 VM。 用你自己的值替换或设置“$resourcegroup”和“$vmName”变量 。 将“$zone”设置为从[本文开头](#determine-vm-size-and-region-availability)获取的可用性区域的值。 然后运行以下 [New-AzVm](/powershell/module/az.compute/new-azvm) 命令，以创建启用了超级磁盘的 VM：

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

若要创建并附加具有 512 字节扇区大小的超级磁盘，可以使用以下脚本：

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
---
## <a name="attach-an-ultra-disk"></a>附加超级磁盘

# <a name="portal"></a>[Portal](#tab/azure-portal)

或者，如果现有 VM 位于能够使用超级磁盘的地区/可用性区域中，则可以使用超级磁盘，而不必创建新 VM。 方法是：在现有 VM 上启用超级磁盘，然后将其附加为数据磁盘。 若要启用超级磁盘兼容性，则必须停止该 VM。 在停止该 VM 后，可以启用兼容性，然后重启该 VM。 在启用了兼容性后，即可附加超级磁盘：

1. 导航到 VM 并将其停止，等待它解除分配。
1. 在解除分配 VM 后，请选择“磁盘”。
1. 选择“其他设置”。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-disk-additional-settings.png" alt-text="磁盘边栏选项卡的屏幕截图，突出显示了其他设置。":::

1. 为“启用超级磁盘兼容性”选择“是” 。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="“启用超级磁盘兼容性”的屏幕截图。":::

1. 选择“保存”。
1. 选择“创建并附加新磁盘”，并填写新磁盘的名称。
1. 对于“存储类型”，请选择“超级磁盘” 。
1. 将“大小 (GiB)”、“最大 IOPS”和“最大吞吐量”的值更改为所选值  。
1. 在返回到磁盘的边栏选项卡后，选择“保存”。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-create-ultra-disk-existing-vm.png" alt-text="磁盘边栏选项卡的屏幕截图，正在添加新超级磁盘。":::

1. 再次启动 VM。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

或者，如果现有 VM 位于能够使用超级磁盘的地区/可用性区域中，则可以使用超级磁盘，而不必创建新 VM。

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>在现有 VM 上启用超级磁盘兼容性 - CLI

如果 VM 满足[正式发布的范围和限制](#ga-scope-and-limitations)中列出的要求并且位于[适用于你的帐户的区域](#determine-vm-size-and-region-availability)中，则可以在该 VM 上启用超级磁盘兼容性。

若要启用超级磁盘兼容性，则必须停止该 VM。 在停止该 VM 后，可以启用兼容性，然后重启该 VM。 在启用了兼容性后，即可附加超级磁盘：

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>创建超级磁盘 - CLI

现在，你有了能够附加超级磁盘的 VM，可以创建超级磁盘并将它附加到该 VM。

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>附加磁盘 - CLI

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

或者，如果现有 VM 位于能够使用超级磁盘的地区/可用性区域中，则可以使用超级磁盘，而不必创建新 VM。

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>在现有 VM 上启用超级磁盘兼容性 - PowerShell

如果 VM 满足[正式发布的范围和限制](#ga-scope-and-limitations)中列出的要求并且位于[适用于你的帐户的区域](#determine-vm-size-and-region-availability)中，则可以在该 VM 上启用超级磁盘兼容性。

若要启用超级磁盘兼容性，则必须停止该 VM。 在停止该 VM 后，可以启用兼容性，然后重启该 VM。 在启用了兼容性后，即可附加超级磁盘：

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>创建和附加超级磁盘 - PowerShell

现在，你有了能够使用超级磁盘的 VM，可以创建超级磁盘并将它附加到该 VM：

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>调整超级磁盘的性能

# <a name="portal"></a>[Portal](#tab/azure-portal)

超级磁盘具有一项可用于调整其性能的独特功能。 可以在磁盘本身上从 Azure 门户完成这些调整。

1. 导航到 VM 并选择“磁盘”。
1. 选择要修改其性能的超级磁盘。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-ultra-disk-to-modify.png" alt-text="VM 上磁盘边栏选项卡的屏幕截图，突出显示了超级磁盘。":::

1. 选择“大小 + 性能”，然后完成修改。
1. 选择“保存”。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/modify-ultra-disk-performance.png" alt-text="超级磁盘上配置边栏选项卡的屏幕截图，突出显示了磁盘大小、iops 和吞吐量，并且突出显示了“保存”。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

超级磁盘具有一项可用于调整其性能的独特功能，以下命令描述了如何使用此功能：

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>使用 PowerShell 调整超级磁盘的性能

超级磁盘具有一项可用于调整其性能的独特功能，以下命令是无需拆离磁盘即可调整性能的示例：

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>后续步骤

- [在 Azure Kubernetes 服务上使用 Azure 超级磁盘（预览版）](../aks/use-ultra-disks.md)。
- [将日志磁盘迁移到超级磁盘](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md)。
