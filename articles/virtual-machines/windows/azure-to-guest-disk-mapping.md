---
title: 如何将 Azure 磁盘映射到 Windows VM 来宾磁盘
description: 如何确定构成 Windows VM 来宾磁盘基础的 Azure 磁盘。
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 68941b2253e1b4655a9e0e61da3b0a4f0db27bdf
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697583"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>如何将 Azure 磁盘映射到 Windows VM 来宾磁盘

**适用于：** :heavy_check_mark: Windows VM 


你需要确定支持 VM 来宾磁盘的 Azure 磁盘。 在某些方案中，可以将磁盘或卷大小与附加的 Azure 磁盘的大小进行比较。 如果有多个相同大小的 Azure 磁盘附加到 VM，则需要使用数据磁盘的逻辑单元号 (LUN)。 

## <a name="what-is-a-lun"></a>什么是 LUN？

逻辑单元号 (LUN) 是用于标识特定存储设备的数字。 会为每个存储设备分配一个唯一的数字标识符（从零开始）。 设备的完整路径由总线编号、目标 ID 号和逻辑单元号 (LUN) 表示。 

例如：总线编号 0，目标 ID 0，LUN 3

在我们的练习中，只需要使用 LUN。

## <a name="finding-the-lun"></a>查找 LUN

有两种方法可查找 LUN，选择哪一种方法取决于你是否使用[存储空间](/windows-server/storage/storage-spaces/overview)。

### <a name="disk-management"></a>磁盘管理

如果使用的不是存储池，则可以使用[磁盘管理](/windows-server/storage/disk-management/overview-of-disk-management)查找 LUN。

1. 连接到 VM 并打开“磁盘管理”。 右键单击“开始”按钮并选择“磁盘管理”。 还可以在“开始搜索”框中键入 `diskmgmt.msc`
1. 在下面的窗格中，右键单击任一磁盘，然后选择“属性”
1. LUN 列在“常规”选项卡上的“位置”属性中

### <a name="storage-pools"></a>存储池

1. 连接到 VM 并打开“服务器管理器”
1. 选择“文件和存储服务”、“卷”、“存储池”
1. 在“服务器管理器”右下角，会出现一个“物理磁盘”部分。 此处列出了构成存储池的磁盘，以及每个磁盘的 LUN。

## <a name="finding-the-lun-for-the-azure-disks"></a>查找 Azure 磁盘的 LUN

可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 查找 LUN

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>在 Azure 门户中查找 Azure 磁盘的 LUN

1. 在 Azure 门户中，选择“虚拟机”以显示虚拟机的列表
1. 选择虚拟机
1. 选择“磁盘”
1. 从附加的磁盘列表中选择一个数据磁盘。
1. 磁盘的 LUN 显示在“磁盘详细信息”窗格中。 此处显示的 LUN 与使用设备管理器或服务器管理器在来宾中查找的 LUN 相关联。

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
