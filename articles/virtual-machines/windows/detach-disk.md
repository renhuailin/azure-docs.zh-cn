---
title: 从 Windows VM 中分离数据磁盘
description: 在 Azure 中从使用资源管理器部署模型的虚拟机分离磁盘。
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49b58a46b4abc65089970982c26424a2f615bad4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693414"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>如何从 Windows 虚拟机分离数据磁盘

**适用于：** :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 

不再需要附加到虚拟机的数据磁盘时，可以轻松地分离它。 这会从虚拟机中删除磁盘，但不会从存储中删除它。

> [!WARNING]
> 如果分离磁盘，它将不会自动删除。 如果用户订阅了高级存储，则将继续承担该磁盘的存储费用。 有关详细信息，请参阅[使用高级存储时的定价和计费方式](../disks-types.md#billing)。

如果希望再次使用磁盘上的现有数据，可以将其重新附加到相同的虚拟机或另一个虚拟机。

 

## <a name="detach-a-data-disk-using-powershell"></a>使用 PowerShell 分离数据磁盘

可以使用 PowerShell 热删除数据磁盘，但在从 VM 中分离磁盘之前，请确保没有任何设备正在使用该磁盘。

在此示例中，我们从 **myResourceGroup** 资源组的 VM **myVM** 中删除名为 **myDisk** 的磁盘。 首先，使用 [Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk) cmdlet 删除磁盘。 然后，使用 [Update-AzVM](/powershell/module/az.compute/update-azvm) cmdlet 更新虚拟机的状态，完成数据磁盘删除过程。

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

磁盘保留在存储中，但不再附加到虚拟机。

## <a name="detach-a-data-disk-using-the-portal"></a>使用门户分离数据磁盘

可以热删除数据磁盘，但在从 VM 中分离该磁盘之前，请确保没有任何设备正在活跃地使用该磁盘。

1. 在左侧菜单中，选择“虚拟机”。
1. 选择具有要分离的数据磁盘的虚拟机。
1. 在“设置”下，选择“磁盘” 。
1. 在“磁盘”窗格中，转到要拆离的数据磁盘最右侧，选择“X”按钮以拆离 。
1. 选择页面顶部的“保存”以保存更改。

磁盘保留在存储中，但不再附加到虚拟机。 磁盘未被删除。

## <a name="next-steps"></a>后续步骤

要重新使用数据磁盘，只需[将其附加到其他 VM](attach-managed-disk-portal.md) 即可。

如果要删除磁盘，以便不再产生存储费用，请参阅[查找并删除未附加的 Azure 托管和非托管磁盘 - Azure 门户](../disks-find-unattached-portal.md)。
