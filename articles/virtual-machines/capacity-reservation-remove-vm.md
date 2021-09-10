---
title: 从容量预留组中删除虚拟机关联（预览版）
description: 了解如何从容量预留组中删除虚拟机。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 2f5537ec3ad34e3f0ad7eff32d32762ed6fedef3
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273283"
---
# <a name="remove-a-vm-association-from-a-capacity-reservation-group-preview"></a>从容量预留组中删除 VM 关联（预览版）

本文将指导你完成删除与容量预留组的 VM 关联的步骤。 要了解有关容量预留的详细信息，请参阅[概述](capacity-reservation-overview.md)一文。 

由于 VM 和基础容量预留在逻辑上会占用容量，因此 Azure 对此进程施加了一些约束，以避免模糊的分配状态和意外的错误。  

有两种方法可更改关联： 
- 选项 1：解除分配虚拟机，更改“容量预留组”属性，并选择性地重启虚拟机
- 选项 2：将预留数量更新为零，然后更改“容量预留组”属性

> [!IMPORTANT]
> 容量预留功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-for-capacity-reservation"></a>注册容量预留 

必须先[注册预览版订阅](capacity-reservation-overview.md#register-for-capacity-reservation)，然后才能使用容量预留功能。 注册可能需要几分钟才能完成。 可使用 Azure CLI 或 PowerShell 完成功能注册。


## <a name="deallocate-the-vm"></a>解除分配 VM

第一个选项是解除分配虚拟机，更改“容量预留组”属性，并选择性地重启 VM。 

### <a name="api"></a>[API](#tab/api1)

1. 解除分配 VM

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. 更新 VM 以删除与容量预留组的关联
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/update?api-version=2021-04-01
    ```
    在请求正文中，将 `capacityReservationGroup` 属性设置为空以删除与组的 VM 关联：

    ```json
     {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":""
            }
        }
    }
    }
    ```

### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. 打开 [Azure 门户](https://portal.azure.com)
1. 转到虚拟机，然后选择“概述”
1. 选择“停止” 
    1. 当状态更改为“已停止(已解除分配)”时，你会知道 VM 已解除分配
    1. 此时，VM 仍与容量预留组关联，这反映在容量预留的 `virtualMachinesAssociated` 属性中 
1. 选择“配置”
1. 将“容量预留组”的值设置为“无”
    - VM 不再与容量预留组关联 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 解除分配虚拟机

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

    当状态更改为“已停止(已解除分配)”时，虚拟机已解除分配。

1. 通过将 `CapacityReservationGroupId` 属性设置为空，更新 VM 以删除与容量预留组的关联：

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId " "
    ```

若要了解详细信息，请转到 Azure PowerShell 命令 [Stop-AzVM](/powershell/module/az.compute/stop-azvm)、[Get-AzVM](/powershell/module/az.compute/get-azvm) 和 [Update-AzVM](/powershell/module/az.compute/update-azvm)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>将预留数量更新为零 

第二个选项涉及将预留数量更新为零，然后更改“容量预留组”属性。

当无法解除分配虚拟机以及不再需要预留时，此选项非常适用。 例如，可以创建容量预留，以临时确保大规模部署期间的容量。 完成后，就可以不再需要该预留。 

### <a name="api"></a>[API](#tab/api2)

1. 将预留数量更新为零 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/CapacityReservations/{CapacityReservationName}?api-version=2021-04-01
    ```

    在请求正文中，添加以下内容：
    
    ```json
    {
    "sku":
        {
        "capacity": 0
        }
    }
    ```
    
    请注意，`capacity` 属性在上面被设置为 0。

1. 更新 VM 以删除与容量预留组的关联

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/update?api-version=2021-04-01
    ```

    在请求正文中，将 `capacityReservationGroup` 属性设置为空以删除关联：
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":""
            }
        }
    }
    } 
    ```

### <a name="portal"></a>[Portal](#tab/portal2)

<!-- no images necessary if steps are straightforward --> 

1. 打开 [Azure 门户](https://portal.azure.com)
1. 转到你的容量预留组，然后选择“概述”
1. 选择“预留” 
1. 选择页面顶部的“管理预留” 
1. 在“管理预留”边栏选项卡上：
    1. 在“实例”字段中输入 `0`
    1. 选择“保存” 
1. 转到虚拟机，然后选择“配置”
1. 将“容量预留组”的值设置为“无”
    - 请注意，VM 不再与容量预留组关联

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

>[!NOTE]
> 命令 `Update-AzCapacityReservation` 在预览期间不可用。 使用 `New-AzCapacityReservation` 修改现有容量预留。

1. 将预留数量更新为零

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 0
    ```

1. 通过将 `CapacityReservationGroupId` 属性设置为空，更新 VM 以删除与容量预留组的关联：

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId " "
    ```

要了解详细信息，请转到 Azure PowerShell 命令 [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation)、[Get-AzVM](/powershell/module/az.compute/get-azvm) 和 [Update-AzVM](/powershell/module/az.compute/update-azvm)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何将规模集关联到容量预留组](capacity-reservation-associate-virtual-machine-scale-set.md)