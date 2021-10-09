---
title: 在 Azure 中修改容量预留（预览版）
description: 了解如何修改容量预留。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 8b6e2ba3c65b5fd521bdb6326069ce5d8be05599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564878"
---
# <a name="modify-a-capacity-reservation-preview"></a>修改容量预留（预览版）

创建容量预留组和容量预留后，你可能想要修改预留。 本文介绍如何使用 API、Azure 门户和 PowerShell 执行以下操作。 

> [!div class="checklist"]
> * 更新容量预留中预留的实例数 
> * 重设与容量预留组关联的 VM 的大小
> * 删除容量预留组和容量预留

> [!IMPORTANT]
> 容量预留功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="update-the-number-of-instances-reserved"></a>更新预留的实例数 

更新容量预留中预留的虚拟机实例数。  

> [!IMPORTANT]
> 在 Azure 无法满足增加现有容量预留的预留数量这一请求等极少数情况下，预留状态可能会更改为“失败”且变得不可用，直到[数量还原到原始数额](#restore-instance-quantity)。

### <a name="api"></a>[API](#tab/api1)

```rest
    PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
``` 
    
在请求正文中，将 `capacity` 属性更新为要保留的新计数： 
    
```json
{
    "sku":
    {
        "capacity": 5
    }
} 
```

请注意，在此示例中，`capacity` 属性现在设置为 5。


### <a name="portal"></a>[Portal](#tab/portal1) 

1. 打开 [Azure 门户](https://portal.azure.com)
1. 转到你的容量预留组
1. 选择“概述” 
1. 选择“预留” 
1. 选择顶部的“管理预留” 
1. 在“管理预留”页面上，在“实例”字段中输入要预留的新数量 
1. 选择“保存” 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

要更新预留数量，请将 `New-AzCapacityReservation` 与更新后的 `capacityToReserve` 属性一起使用。

```powershell-interactive
Update-AzCapacityReservation
-ResourceGroupName "myResourceGroup"
-ReservationGroupName "myCapacityReservationGroup"
-Name "myCapacityReservation"
-CapacityToReserve 5
```

要了解详细信息，请转到 Azure PowerShell 命令 [Update-AzCapacityReservation](/powershell/module/az.compute/update-azcapacityreservation)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="resize-vms-associated-with-a-capacity-reservation-group"></a>重设与容量预留组关联的 VM 的大小 

如果正在重设大小的虚拟机当前附加到容量预留组，而该组没有针对目标大小的预留，那么请为该大小创建一个新预留，或在重设大小之前从预留组中删除该虚拟机。 

检查目标大小是否属于预留组： 

### <a name="api"></a>[API](#tab/api2)

1. 获取组内所有容量预留的名称。
 
    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "<CapacityReservationGroupName>", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
        "type": "Microsoft.Compute/capacityReservationGroups", 
        "location": "eastUS", 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "capacityReservations": [ 
                { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}" 
                }, 
    { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName2}" 
                } 
            ] 
        } 
    } 
    ```

1. 查找每个预留所预留的 VM 大小。 以下示例适用于 `capacityReservationName1`，但可以对其他预留重复此步骤。

    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "capacityReservationName1", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}", 
        "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
        "location": "eastUS", 
        "sku": { 
            "name": "Standard_D2s_v3", 
            "capacity": 3 
        }, 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "reservationId": "<reservationId>", 
            "provisioningTime": "<provisioningTime>", 
            "provisioningState": "Succeeded" 
        } 
    }  
    ```

1. 考虑以下情况： 
    1. 如果目标 VM 大小不属于该组，请为目标 V​​M [创建新的容量预留](capacity-reservation-create.md) 
    1. 如果组中已存在目标 VM 大小，请[重设虚拟机大小](resize-vm.md) 

### <a name="portal"></a>[Portal](#tab/portal2)

1. 打开 [Azure 门户](https://portal.azure.com)
1. 转到你的容量预留组
1. 选择“概述” 
1. 选择“预留” 
1. 查看每个预留所预留的 VM 大小 
    1. 如果目标 VM 大小不属于该组，请为目标 V​​M [创建新的容量预留](capacity-reservation-create.md) 
    1. 如果组中已存在目标 VM 大小，请[重设虚拟机大小](resize-vm.md) 

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. 使用 `Get-AzCapacityReservationGroup` 获取组内所有容量预留的名称

    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. 从响应中，查找所有容量预留的名称

1. 运行以下命令查找每个预留所预留的 VM 大小

    ```powershell-interactive
    $CapRes =
    Get-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "mycapacityReservation"
    
    $CapRes.Sku
    ```

1. 考虑以下情况： 
    1. 如果目标 VM 大小不属于该组，请为目标 V​​M [创建新的容量预留](capacity-reservation-create.md) 
    1. 如果组中已存在目标 VM 大小，请[重设虚拟机大小](resize-vm.md) 


要了解详细信息，请转到 Azure PowerShell 命令 [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup) 和 [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="delete-a-capacity-reservation-group-and-capacity-reservation"></a>删除容量预留组和容量预留 

如果容量预留组的所有成员容量预留均已删除且没有虚拟机与该组关联，Azure 允许删除该组。  

要删除容量预留，请先查找与其关联的所有虚拟机。 虚拟机列表位于 `virtualMachinesAssociated` 属性下。 

### <a name="api"></a>[API](#tab/api3)

首先，查找与容量预留组关联的所有虚拟机并将它们解除关联。
 
```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01
``` 

```json
{ 
    "name": "<capacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}/capacityReservations/{capacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
            }, 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName2}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "{capacityReservationName}", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
                            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
}  
```
从上述响应中，查找 `virtualMachinesAssociated` 属性下所有虚拟机的名称，并按照[删除与容量预留的 VM 关联](capacity-reservation-remove-vm.md)中的步骤将它们从容量预留组中删除。 

从容量预留组中删除所有虚拟机后，删除成员容量预留：

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```

最后，删除父容量预留组。

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
```


### <a name="portal"></a>[Portal](#tab/portal3)

1. 打开 [Azure 门户](https://portal.azure.com)
1. 转到你的容量预留组
1. 选择“资源” 
1. 查找与该组关联的所有虚拟机
1. [解除每个虚拟机的关联](capacity-reservation-remove-vm.md)
1. 删除组中的每个容量预留
    1. 转到“预留”
    1. 选择每个预留 
    1. 选择“删除”
1. 删除容量预留组
    1. 转到“容量预留组”
    1. 选择页面顶部的“删除”


### <a name="powershell"></a>[PowerShell](#tab/powershell3)

查找与容量预留组关联的所有虚拟机并将它们取消关联。

1. 运行以下内容： 
    
    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. 从上述响应中，查找 `VirtualMachinesAssociated` 属性下所有虚拟机的名称，并按照[从容量预留组中删除虚拟机关联](capacity-reservation-remove-vm.md)中详述的步骤将它们从容量预留组中删除。

1. 从组中删除所有虚拟机后，继续执行后续步骤。 

1. 删除容量预留：

    ```powershell-interactive
    Remove-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    ```

1. 删除容量预留组：

    ```powershell-interactive
    Remove-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

要了解详细信息，请转到 Azure PowerShell 命令 [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup)、[Remove-AzCapacityReservation](/powershell/module/az.compute/remove-azcapacityreservation) 和 [Remove-AzCapacityReservationGroup](/powershell/module/az.compute/remove-azcapacityreservationgroup)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="restore-instance-quantity"></a>还原实例数量 

无论与预留关联的虚拟机数量如何，格式正确的预留数量减少请求都应始终成功。 但是，要增加预留数量，可能需要更多配额，并且只有 Azure 才能满足额外的容量请求。 在 Azure 无法满足增加现有预留的预留数量这一请求等极少数场景中，预留状态可能会更改为“失败”且变得不可用，直到预留数量还原到原始数额。  

> [!NOTE]
> 如果预留处于“失败”状态，则与预留关联的所有 VM 都将继续正常工作。  

例如，假设 `myCapacityReservation` 的预留数量为 5。 你请求 5 个额外的实例，使预留总数量等于 10。 但是，由于该区域的容量受到限制，Azure 无法满足请求的额外 5 个数量。 在这种情况下，`myCapacityReservation` 将无法满足其预留数量为 10 的预期状态，并且状态会更改为“失败”。 

要解决此失败，请执行以下步骤来查找旧的预留数量值：  

1. 转到 Azure 门户中的[应用程序更改分析](https://ms.portal.azure.com/#blade/Microsoft_Azure_ChangeAnalysis/ChangeAnalysisBaseBlade) 
1. 在筛选器中选择适用的“订阅”、“资源组”和“时间范围”  
    - 在“时间范围”筛选器中，最多只能追溯到过去 14 天 
1. 搜索容量预留的名称
1. 查找对该预留的 `sku.capacity` 属性所做的更改 
    - 旧的预留数量将是“旧值”列下的值 

将 `myCapacityReservation` 更新为旧的预留数量。 更新后，预留可立即供虚拟机使用。 


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何从容量预留中删除 VM](capacity-reservation-remove-vm.md)
