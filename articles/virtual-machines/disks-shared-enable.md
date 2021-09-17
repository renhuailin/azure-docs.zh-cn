---
title: 为 Azure 托管磁盘启用共享磁盘
description: 为 Azure 托管磁盘配置共享磁盘，以便可以跨多个 VM 共享它
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/01/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: bdf012d1ee6c1230e458b7b40e3130d8fa25e4a1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424741"
---
# <a name="enable-shared-disk"></a>启用共享磁盘

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

本文介绍了如何为 Azure 托管磁盘启用共享磁盘功能。 Azure 共享磁盘是 Azure 托管磁盘的一项新功能，可同时将托管磁盘附加到多个虚拟机 (VM)。 通过将托管磁盘附加到多个 VM，可以向 Azure 部署新的群集应用程序或迁移现有的群集应用程序。 

如果你正在查找有关已启用共享磁盘的托管磁盘的概念信息，请参阅 [Azure 共享磁盘](disks-shared.md)。

## <a name="prerequisites"></a>先决条件

本文中的脚本和命令需要以下任一项：

- Azure PowerShell 模块 6.0.0 或更高版本。

或
- 最新版本的 Azure CLI。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

共享磁盘支持多个操作系统。 有关支持的操作系统，请参阅概念文章的 [Windows](./disks-shared.md#windows) 和 [Linux](./disks-shared.md#linux) 部分。

## <a name="disk-sizes"></a>磁盘大小

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>部署共享磁盘

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>将高级 SSD 部署为共享磁盘

若要部署启用了共享磁盘功能的托管磁盘，请使用新属性 `maxShares` 并定义大于 1 的值。 这会使该磁盘可在多个 VM 之间共享。

> [!IMPORTANT]
> 仅当从所有 VM 中卸载了某个磁盘时，才能设置或更改 `maxShares` 的值。 有关 `maxShares` 的允许值，请参阅[磁盘大小](#disk-sizes)。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 登录到 Azure 门户。 
1. 搜索并选择“磁盘”。
1. 选择“+ 创建”以创建新磁盘。
1. 填写详细信息并选择相应的区域，然后选择“更改大小”。

    :::image type="content" source="media/disks-shared-enable/create-shared-disk-basics-pane.png" alt-text="“创建托管磁盘”窗格的屏幕截图，其中“更改大小”已突出显示。" lightbox="media/disks-shared-enable/create-shared-disk-basics-pane.png":::

1. 选择所需的高级 SSD 大小和 SKU，然后选择“确定”。

    :::image type="content" source="media/disks-shared-enable/select-premium-shared-disk.png" alt-text="磁盘 SKU 的屏幕截图，其中已突出显示高级 LRS 和 ZRS SSD SKU。" lightbox="media/disks-shared-enable/select-premium-shared-disk.png":::

1. 继续部署，直到转到“高级”窗格。
1. 对于“启用共享磁盘”，请选择“是”，然后选择所需的“最大共享”的量  。

    :::image type="content" source="media/disks-shared-enable/enable-premium-shared-disk.png" alt-text="“高级”窗格的屏幕截图，其中“启用共享磁盘”已突出显示并设置为“是”。" lightbox="media/disks-shared-enable/enable-premium-shared-disk.png":::

1. 选择“查看 + 创建”  。


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

使用以下模板之前，请使用你自己的值替换 `[parameters('dataDiskName')]`、`[resourceGroup().location]`、`[parameters('dataDiskSizeGB')]` 和 `[parameters('maxShares')]`。

[高级 SSD 共享磁盘模板](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-a-standard-ssd-as-a-shared-disk"></a>将标准 SSD 部署为共享磁盘

若要部署启用了共享磁盘功能的托管磁盘，请使用新属性 `maxShares` 并定义大于 1 的值。 这会使该磁盘可在多个 VM 之间共享。

> [!IMPORTANT]
> 仅当从所有 VM 中卸载了某个磁盘时，才能设置或更改 `maxShares` 的值。 有关 `maxShares` 的允许值，请参阅[磁盘大小](#disk-sizes)。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 登录到 Azure 门户。 
1. 搜索并选择“磁盘”。
1. 选择“+ 创建”以创建新磁盘。
1. 填写详细信息并选择相应的区域，然后选择“更改大小”。

    :::image type="content" source="media/disks-shared-enable/create-shared-disk-basics-pane.png" alt-text="“创建托管磁盘”窗格的屏幕截图，其中“更改大小”已突出显示。" lightbox="media/disks-shared-enable/create-shared-disk-basics-pane.png":::

1. 选择所需的标准 SSD 大小和 SKU，然后选择“确定”。

    :::image type="content" source="media/disks-shared-enable/select-standard-ssd-shared-disk.png" alt-text="磁盘 SKU 的屏幕截图，其中已突出显示标准 SSD LRS 和 ZRS SKU。" lightbox="media/disks-shared-enable/select-premium-shared-disk.png":::

1. 继续部署，直到转到“高级”窗格。
1. 对于“启用共享磁盘”，请选择“是”，然后选择所需的“最大共享”的量  。

    :::image type="content" source="media/disks-shared-enable/enable-premium-shared-disk.png" alt-text="“高级”窗格的屏幕截图，其中“启用共享磁盘”已突出显示并设置为“是”。" lightbox="media/disks-shared-enable/enable-premium-shared-disk.png":::

1. 选择“查看 + 创建”  。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku StandardSSD_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType StandardSSD_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

在使用此 Azure 资源管理器模板之前，请将其中的值替换为你自己的值：

```rest
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "StandardSSD_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>将超级磁盘部署为共享磁盘

若要部署启用了共享磁盘功能的托管磁盘，请将 `maxShares` 参数更改为大于 1 的值。 这会使该磁盘可在多个 VM 之间共享。

> [!IMPORTANT]
> 仅当从所有 VM 中卸载了某个磁盘时，才能设置或更改 `maxShares` 的值。 有关 `maxShares` 的允许值，请参阅[磁盘大小](#disk-sizes)。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 登录到 Azure 门户。 
1. 搜索并选择“磁盘”。
1. 选择“+ 创建”以创建新磁盘。
1. 填写详细信息，然后选择“更改大小”。
1. 对于“磁盘 SKU”，请选择“超级磁盘”。

    :::image type="content" source="media/disks-shared-enable/select-ultra-shared-disk.png" alt-text="磁盘 SKU 的屏幕截图，其中“超级磁盘”已突出显示。" lightbox="media/disks-shared-enable/select-ultra-shared-disk.png":::

1. 选择所需的磁盘大小，然后选择“确定”。
1. 继续部署，直到转到“高级”窗格。
1. 对于“启用共享磁盘”，请选择“是”，然后选择所需的“最大共享”的量。
1. 选择“查看 + 创建”  。

    :::image type="content" source="media/disks-shared-enable/enable-ultra-shared-disk.png" alt-text="“高级”窗格的屏幕截图，其中“启用共享磁盘”已突出显示。" lightbox="media/disks-shared-enable/enable-ultra-shared-disk.png":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>地区性磁盘示例

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>区域性磁盘示例

此示例与上一个示例基本相同，只不过它在可用性区域 1 中创建了一个磁盘。

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>地区性磁盘示例

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>区域性磁盘示例

此示例与上一个示例基本相同，只不过它在可用性区域 1 中创建了一个磁盘。

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>地区性磁盘示例

在使用以下模板之前，请先用你自己的值替换 `[parameters('dataDiskName')]`、`[resourceGroup().location]`、`[parameters('dataDiskSizeGB')]`、`[parameters('maxShares')]`、`[parameters('diskIOPSReadWrite')]`、`[parameters('diskMBpsReadWrite')]`、`[parameters('diskIOPSReadOnly')]` 和 `[parameters('diskMBpsReadOnly')]`。

[地区性共享超级磁盘模板](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>区域性磁盘示例

在使用以下模板之前，请先用你自己的值替换 `[parameters('dataDiskName')]`、`[resourceGroup().location]`、`[parameters('dataDiskSizeGB')]`、`[parameters('maxShares')]`、`[parameters('diskIOPSReadWrite')]`、`[parameters('diskMBpsReadWrite')]`、`[parameters('diskIOPSReadOnly')]` 和 `[parameters('diskMBpsReadOnly')]`。

[区域性共享超级磁盘模板](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>将 Azure 共享磁盘与 VM 配合使用

使用 `maxShares>1` 部署共享磁盘后，可以将该磁盘装载到一个或多个 VM。

> [!NOTE]
> 如果要部署超级磁盘，请确保它符合必要的要求。 有关详细信息，请参阅[使用 Azure 超级磁盘](disks-enable-ultra-ssd.md)。

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>支持的 SCSI PR 命令

将共享磁盘装载到群集中的 VM 后，可以使用 SCSI PR 建立仲裁以及在磁盘中进行读取/写入操作。 使用 Azure 共享磁盘时，可以使用以下 PR 命令：

若要与磁盘进行交互，请从 persistent-reservation-action 列表开始：

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

使用 PR_RESERVE、PR_PREEMPT_RESERVATION 或 PR_RELEASE_RESERVATION 时，请提供下列 persistent-reservation-type 之一：

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

使用 PR_RESERVE、PR_REGISTER_AND_IGNORE、PR_REGISTER_KEY、PR_PREEMPT_RESERVATION、PR_CLEAR_RESERVATION 或 PR_RELEASE-RESERVATION 时，还需要提供 persistent-reservation-key。


## <a name="next-steps"></a>后续步骤

如果希望使用 Azure 资源管理器模板来部署磁盘，可使用以下示例模板：
- [高级·SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [地区性超级磁盘](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [区域性超级磁盘](https://aka.ms/SharedUltraDiskARMtemplateZonal)
