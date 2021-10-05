---
title: 创建虚拟硬盘的 Azure 快照
description: 了解如何使用门户、PowerShell 或 CLI 创建 Azure VM 的副本，以将其用作备份或用于排查问题。
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: b2dadea22326f8b4bf2af6a55d90e392feea8c7a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624759"
---
# <a name="create-a-snapshot-of-a-virtual-hard-disk"></a>创建虚拟硬盘的快照

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集

快照是虚拟硬盘 (VHD) 的完整只读副本。 可以使用快照作为时间点备份，或帮助排查虚拟机 (VM) 问题。 可以创建操作系统 (OS) 或数据磁盘 VHD 的快照。

## <a name="create-a-snapshot-of-a-vhd"></a>创建 VHD 的快照

若要使用快照创建新的 VM，请确保先彻底关闭 VM。 此操作将清除正在进行的所有进程。

# <a name="portal"></a>[门户](#tab/portal)

若要使用 Azure 门户创建快照，请完成以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”。
1. 搜索并选择“快照”。
1. 在“快照”窗口，选择“创建” 。 此时将显示“创建快照”窗口。
1. 对于“资源组”，请选择现有的[资源组](/azure/azure-resource-manager/management/overview#resource-groups)，或输入新资源组的名称。
1. 输入名称，然后为新快照选择区域和快照类型  。 如果你要将快照存储在具有区域复原能力的存储中，需要选择支持[可用性区域](/azure/availability-zones/az-overview)的区域。 有关支持的区域列表，请参阅[具有可用性区域的 Azure 区域](/azure/availability-zones/az-region#azure-regions-with-availability-zones)。
1. 对于“源订阅”，请选择包含要备份的托管磁盘的订阅。
1. 对于 **源磁盘**，选择要获取其快照的托管磁盘。
1. 对于“存储类型”，除非你需要为快照使用区域冗余存储或高性能存储，否则请选择“标准 HDD” 。
1. 如果需要，请在“加密”、“网络”和“标记”选项卡上配置设置  。 否则将为快照使用默认设置。
1. 选择“查看 + 创建”。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

此示例要求使用 [Cloud Shell](https://shell.azure.com/bash) 或已安装 [Azure CLI](/cli/azure/)。

按照以下步骤使用 `New-AzSnapshotConfig` 和 `New-AzSnapshot` cmdlet 创建快照。 此示例假设 myResourceGroup 资源组中具有名为 myVM 的 VM 。 提供的代码示例将创建一个快照，该快照位于源 VM 所在的资源组中，并且位于源 VM 所在的区域中。

首先，使用 [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) cmdlet 创建可配置的快照对象。 然后可以使用 [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) cmdlet 创建磁盘的快照。

1. 设置所需的参数。 请更新值以反映你的环境。

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

1. 使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet 获取包含要复制的 VHD 的 VM。

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

1. 创建快照配置。 该示例中的快照是 OS 磁盘的快照。 默认情况下，快照使用本地冗余的标准存储。 不管父磁盘或目标磁盘的存储类型是什么，我们都建议将快照存储在标准存储而不是高级存储中。 高级快照会产生额外的费用。

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```

   如果要将快照存储在具有区域复原能力的存储中，则必须在支持 [可用性区域](/azure/availability-zones/az-overview 的区域中创建快照，并包含 `-SkuName Standard_ZRS` 参数。 有关支持可用性区域的区域列表，请参阅[具有可用性区域的 Azure 区域](/azure/availability-zones/az-region#azure-regions-with-availability-zones)。

1. 创建快照。

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```

1. 使用 [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) cmdlet 验证你的快照是否存在。

    ```azurepowershell-interactive
    Get-AzSnapshot `
        -ResourceGroupName $resourceGroupName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

此示例要求使用 [Cloud Shell](https://shell.azure.com/bash) 或已安装 [Azure CLI](/cli/azure/)。

按照以下步骤使用 `az snapshot create` 命令和 `--source-disk` 参数创建快照。 此示例假设 myResourceGroup 资源组中具有名为 myVM 的 VM 。 提供的代码示例将创建一个快照，该快照位于源 VM 所在的资源组中，并且位于源 VM 所在的区域中。

1. 使用 [az vm show](/cli/azure/vm#az_vm_show) 获取磁盘 ID。

    ```azurecli-interactive
    osDiskId=$(az vm show \
       -g myResourceGroup \
       -n myVM \
       --query "storageProfile.osDisk.managedDisk.id" \
       -o tsv)
    ```

1. 使用 [az snapshot create](/cli/azure/snapshot#az_snapshot_create) 创建名为 *osDisk-backup* 的快照。 该示例中的快照是 OS 磁盘的快照。 默认情况下，快照使用本地冗余的标准存储。 不管父磁盘或目标磁盘的存储类型是什么，我们都建议将快照存储在标准存储而不是高级存储中。 高级快照会产生额外的费用。

    ```azurecli-interactive
    az snapshot create \
        -g myResourceGroup \
        --source "$osDiskId" \
        --name osDisk-backup
    ```

    如果要将快照存储在具有区域复原能力的存储中，则需要在支持[可用性区域](/azure/availability-zones/az-overview)的区域中创建该快照，并包含可选的 `--sku Standard_ZRS` 参数。 可在此处找到[可用区域](/azure/availability-zones/az-region#azure-regions-with-availability-zones)列表。
    
1. 使用 [az snapshot list](/cli/azure/snapshot#az_snapshot_list) 验证你的快照是否存在。
    
    ```azurecli-interactive
    az snapshot list \
       -g myResourceGroup \
       - table
    ```

---

## <a name="next-steps"></a>后续步骤

从快照部署虚拟机。 从快照创建托管磁盘，然后附加新的托管磁盘作为 OS 磁盘。

# <a name="portal"></a>[门户](#tab/portal)

有关详细信息，请参阅[使用 Azure 门户从 VHD 创建 VM](windows/create-vm-specialized-portal.md) 中的示例。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

有关详细信息，请参阅[使用 PowerShell 从专用磁盘创建 Windows VM](windows/create-vm-specialized.md) 中的示例。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

有关详细信息，请参阅[使用 Azure CLI 创建完整的 Linux 虚拟机](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json)中的示例。

---
