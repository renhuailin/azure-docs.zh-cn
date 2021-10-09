---
title: 从容量预留组中删除虚拟机规模集关联（预览版）
description: 了解如何从容量预留组中删除虚拟机规模集。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 03b89b1b8c0221795f58ff28addd4fdeaad5053e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532588"
---
# <a name="remove-a-virtual-machine-scale-set-association-from-a-capacity-reservation-group"></a>从容量预留组中删除虚拟机规模集关联 

本文将介绍从容量预留组中删除虚拟机规模集关联的步骤。 要了解有关容量预留的详细信息，请参阅[概述](capacity-reservation-overview.md)一文。 

由于 VM 和基础容量预留在逻辑上会占用容量，因此 Azure 对此进程施加了一些约束，以避免模糊的分配状态和意外的错误。  

有两种方法可更改关联： 
- 选项 1：解除分配虚拟机规模集，在规模集级别更改容量预留组属性，然后更新基础 VM
- 选项 2：将预留数量更新为零，然后更改“容量预留组”属性

> [!IMPORTANT]
> 容量预留功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="deallocate-the-virtual-machine-scale-set"></a>解除分配虚拟机规模集

第一个选项是解除分配虚拟机规模集，在规模集级别更改容量预留组属性，然后更新基础 VM。 

有关自动、滚动和手动升级的详细信息，请参阅[升级策略](#upgrade-policies)。 

### <a name="api"></a>[API](#tab/api1)

1. 解除分配虚拟机规模集

    ```rest
    POST  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. 更新虚拟机规模集以删除与容量预留组的关联
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```
    在请求正文中，将 `capacityReservationGroup` 属性设置为 NULL，以删除组的虚拟机规模集关联：

    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null    
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 解除分配虚拟机规模集。 以下内容将解除分配规模集中的所有虚拟机： 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    ```

1. 更新规模集以删除与容量预留组的关联。 将 `CapacityReservationGroupId` 属性设置为 NULL 会删除规模集与容量预留组的关联： 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

若要了解详细信息，请转到 Azure PowerShell 命令 [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss)、[Get-AzVmss](/powershell/module/az.compute/get-azvmss) 和 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>将预留数量更新为零 

第二个选项涉及将预留数量更新为零，然后更改“容量预留组”属性。

当无法解除分配虚拟机规模集以及不再需要预留时，此选项非常适用。 例如，可以创建容量预留，以临时确保大规模部署期间的容量。 完成后，就可以不再需要该预留。 

有关自动、滚动和手动升级的详细信息，请参阅[升级策略](#upgrade-policies)。 

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

1. 更新虚拟机规模集以删除与容量预留组的关联

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```

    在请求正文中，将 `capacityReservationGroup` 属性设置为 NULL 以删除关联：
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. 将预留数量更新为零：

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

2. 通过将 `CapacityReservationGroupId` 属性设置为 NULL，更新规模集以删除与容量预留组的关联： 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

要了解详细信息，请转到 Azure PowerShell 命令 [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation)、[Get-AzVmss](/powershell/module/az.compute/get-azvmss) 和 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="upgrade-policies"></a>升级策略

- **自动升级** - 在此模式下，规模集 VM 实例与容量预留组自动解除关联，无需你执行任何进一步操作。
- **滚动升级** - 在此模式下，规模集 VM 实例与容量预留组解除关联，无需你执行任何进一步操作。 不过，它们是分批更新的，各批次之间有一个可选的暂停时间。
- **手动升级** - 在此模式下，当虚拟机规模集更新时，规模集 VM 实例保持原样。 你将需要分别删除每个规模集 VM，方法是[使用最新的规模集模型进行升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解超量分配容量预留](capacity-reservation-overallocate.md)
