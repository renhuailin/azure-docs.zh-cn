---
title: 在 Azure 中超量分配产能预留（预览版）
description: 了解有关产能预留的超量分配的工作原理。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 90f7c99aa82dfc91c9910309b66246439e513c88
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532642"
---
# <a name="overallocating-capacity-reservation-preview"></a>超量分配产能预留（预览版）

Azure 允许关联超出“产能预留”预留数量的额外 VM，以支持突发和其他横向扩展方案，且不会产生与预留容量限额有关的管理开销。 唯一的区别是，超出预留数量的 VM 不会获得容量可用性 SLA 权益。 只要 Azure 具有满足虚拟机要求的可用容量，额外分配就会成功。 

产能预留组的实例视图提供每个成员产能预留使用情况的快照。 可以使用实例视图来查看超量分配的工作原理。 

本文假设你已创建产能预留组 (`myCapacityReservationGroup`)、成员产能预留 (`myCapacityReservation`) 和与该组关联的虚拟机 (myVM1)。 有关详细信息，请转到[创建产能预留](capacity-reservation-create.md)和[将 VM 关联到产能预留](capacity-reservation-associate-vm.md)。

> [!IMPORTANT]
> 产能预留目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 


## <a name="instance-view-for-capacity-reservation-group"></a>产能预留组的实例视图 

产能预留组的实例视图将如下所示： 

```rest
GET 
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/myCapacityReservationGroup?$expand=instanceview&api-version=2021-04-01
```

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
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

假设创建了另一个名为 myVM2 的虚拟机，并将其与上述产能预留组相关联。 

产能预留组的实例视图现在将如下所示： 

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            }, 
 { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
                            }, 
{ 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
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

请注意，`virtualMachinesAllocated` (2) 的长度大于 `capacity` (1)。 这种有效状态称为“超量分配”。 

> [!IMPORTANT]
> Azure 不会因为产能预留已完全使用而停止分配。 只要 Azure 有可用容量，自动缩放规则、临时横向扩展和相关要求所用的容量就会超出预留容量。  


## <a name="states-and-considerations"></a>状态和注意事项  

给定产能预留有三种有效状态： 

| 状态  | 状态  | 注意事项  |
|---|---|---|
| 预留容量可用  | `virtualMachinesAllocated` < `capacity` 的长度  | 是否需要所有预留容量？ （可选）减少容量以降低成本。  |
| 预留已使用  | `virtualMachinesAllocated` == `capacity` 的长度  | 除非解除分配某些现有 VM，否则其他 VM 不会收到容量 SLA。 （可选）尝试增加容量，以便额外计划的 VM 可收到 SLA。  |
| 预留已超量分配  | `virtualMachinesAllocated` > `capacity` 的长度  | 其他 VM 不会收到容量 SLA。 此外，如果解除分配，相应数量的 VM（`virtualMachinesAllocated` - `capacity` 的长度）将不会收到容量 SLA。 （可选）增加容量，将容量 SLA 添加到更多现有 VM。  |


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何从容量预留中删除 VM](capacity-reservation-remove-vm.md)
