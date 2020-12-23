---
title: 如何将 Azure 磁盘映射到 Windows VM 来宾磁盘
description: 如何确定是 Windows VM 的来宾磁盘的 Azure 磁盘。
author: timbasham
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: f16e34f372016f284d4af79443e84d9d5cdea957
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523424"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>如何将 Azure 磁盘映射到 Windows VM 来宾磁盘

可能需要确定备份 VM 的来宾磁盘的 Azure 磁盘。 在某些情况下，可以将磁盘或卷大小与附加的 Azure 磁盘的大小进行比较。 如果有多个大小相同的 Azure 磁盘附加到 VM，则需要使用数据磁盘的逻辑单元号 (LUN) 。 

## <a name="what-is-a-lun"></a>什么是 LUN？

LUN)  (逻辑单元号是用于标识特定存储设备的数字。 为每个存储设备分配一个唯一的数字标识符（从零开始）。 设备的完整路径由总线编号、目标 ID 号和逻辑单元号表示， (LUN) 。 

例如： ***总线号0，目标 ID 0，LUN 3***

对于我们的练习，只需使用 LUN。

## <a name="finding-the-lun"></a>查找 LUN

有两种方法可查找 LUN，选择哪一种方法将取决于是否使用 [存储空间](https://docs.microsoft.com/windows-server/storage/storage-spaces/overview) 。

### <a name="disk-management"></a>磁盘管理

如果使用的不是存储池，则可以使用 " [磁盘管理](https://docs.microsoft.com/windows-server/storage/disk-management/overview-of-disk-management) " 来查找 LUN。

1. 连接到 VM，打开 "磁盘管理"。 右键单击 "开始" 按钮，然后选择 "磁盘管理"。 你还可以在 `diskmgmt.msc` "开始搜索" 框中键入
1. 在下面的窗格中，右键单击任一磁盘，然后选择 "属性"
1. LUN 将列在 "常规" 选项卡上的 "位置" 属性中。

### <a name="storage-pools"></a>存储池

1. 连接到 VM 并打开服务器管理器
1. 选择 "文件和存储服务"、"卷"、"存储池"
1. 在服务器管理器右下角，会出现一个 "物理磁盘" 部分。 此处列出了构成存储池的磁盘，以及每个磁盘的 LUN。

## <a name="finding-the-lun-for-the-azure-disks"></a>查找 Azure 磁盘的 LUN

可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 找到 Azure 磁盘的 LUN

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>在 Azure 门户中查找 Azure 磁盘的 LUN

1. 在 Azure 门户中，选择 "虚拟机" 以显示虚拟机的列表
1. 选择虚拟机
1. 选择 "磁盘"
1. 从附加的磁盘列表中选择一个数据磁盘。
1. 磁盘的 LUN 将显示在 "磁盘详细信息" 窗格中。 此处显示的 LUN 关联到使用设备管理器或服务器管理器在来宾中查找的 Lun。

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>使用 Azure CLI 或 Azure PowerShell 查找 Azure 磁盘的 LUN

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---
