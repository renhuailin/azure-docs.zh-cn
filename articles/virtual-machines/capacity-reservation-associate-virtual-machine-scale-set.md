---
title: 将虚拟机规模集与容量预留组进行关联（预览版）
description: 了解如何将新的或现有的虚拟机规模集与容量预留组进行关联。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 39dc20f240ba051dd5b49ddbbcc99a20b732ec77
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273287"
---
# <a name="associate-a-virtual-machine-scale-set-to-a-capacity-reservation-group-preview"></a>将虚拟机规模集与容量预留组进行关联（预览版）

虚拟机规模集有两种模式： 

- **统一业务流程模式：** 在此模式中，虚拟机规模集使用虚拟机 (VM) 配置文件或模板来纵向扩展到所需的容量。 统一模式使用相同的 VM 实例，不过在一定程度上也能够管理或自定义单独的 VM 实例。 这些实例通过虚拟机规模集 VM API 公开，与标准 Azure IaaS VM API 命令不兼容。 规模集执行所有的实际 VM 操作，因此预留项与虚拟机规模集直接关联。 将规模集与预留进行关联后，将针对预留完成所有的后续 VM 分配。 
- **灵活业务流程模式：** 在此模式中，你能够更灵活地管理单个虚拟机规模集 VM 实例，因为它们可使用标准 Azure IaaS VM API，而不是使用规模集界面。 在公共预览版期间，此模式不与容量预留一起使用。

若要详细了解这些模式，请转到[虚拟机规模集业务流程模式](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)。 本文的其余部分会讲解如何将统一虚拟机规模集与容量预留组进行关联。 

> [!IMPORTANT]
> 容量预留功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-for-capacity-reservation"></a>注册容量预留 

必须先[注册预览版订阅](capacity-reservation-overview.md#register-for-capacity-reservation)，然后才能使用容量预留功能。 注册可能需要几分钟才能完成。 可以使用 Azure CLI 或 PowerShell 完成功能注册。

> [!NOTE]
> 按需容量预留仅适用于特选区域中统一业务流程模式下的虚拟机规模集。 若要查看你所在区域是否受到支持，请转到[统一虚拟机规模集部署跟踪器](https://aka.ms/vmssuniformdeploymenttracker)。


## <a name="limitations-of-scale-sets-in-uniform-orchestration"></a>统一业务流程中的规模集限制 

- 若要使统一业务流程中的虚拟机规模集与容量预留兼容，`singlePlacementGroup` 属性必须设置为 False。 
- 容量预留不支持多区域统一规模集的“静态固定分布”可用性选项。 此选项要求使用 5 个容错域，而预留最多支持 3 个容错域来实现常规用途大小。 建议的方法是使用“最大分布”选项，它将 VM 分布到每个区域中尽可能多的容错域 (FD) 中。 如果需要，请将自定义容错域配置设为 3 或更小。 

使用容量预留时，还有其他一些限制。 有关完整列表，请参阅[容量预留概述](capacity-reservation-overview.md)。  

## <a name="associate-a-new-virtual-machine-scale-set-to-a-capacity-reservation-group"></a>将新的虚拟机规模集与容量预留组进行关联


### <a name="api"></a>[API](#tab/api1)  

若要将新的统一虚拟机规模集与容量预留组进行关联，请向 Microsoft.Compute 提供程序构造以下 PUT 请求：

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
```

在 `virtualMachineProfile` 中添加 `capacityReservationGroup` 属性，如下所示： 

```json
{ 
    "name": "<VMScaleSetName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}", 
    "type": "Microsoft.Compute/virtualMachineScaleSets", 
    "location": "eastus", 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "tier": "Standard", 
        "capacity": 3 
}, 
"properties": { 
    "virtualMachineProfile": { 
        "capacityReservation": { 
            "capacityReservationGroup":{ 
                "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroup/{CapacityReservationGroupName}" 
            } 
         }, 
        "osProfile": { 
            … 
        }, 
        "storageProfile": { 
            … 
        }, 
        "networkProfile": { 
            …,
            "extensionProfile": { 
                … 
            } 
        } 
    } 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell1) 

使用 `New-AzVmss` 创建一个新的虚拟机规模集并添加 `CapacityReservationGroupId` 属性，将该规模集与现有容量预留组进行关联。 下面的示例在美国东部位置为 Standard_Ds1_v2 VM 创建一个统一规模集，然后将该规模集关联到一个容量预留组。

```powershell-interactive
$vmssName = <"VMSSNAME">
$vmPassword = ConvertTo-SecureString <"PASSWORD"> -AsPlainText -Force
$vmCred = New-Object System.Management.Automation.PSCredential(<"USERNAME">, $vmPassword)
New-AzVmss
–Credential $vmCred
-VMScaleSetName $vmssName
-ResourceGroupName "myResourceGroup"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
-PlatformFaultDomainCount 2
```

若要了解详细信息，请转到 Azure PowerShell 命令 [New-AzVmss](/powershell/module/az.compute/new-azvmss)。

### <a name="arm-template"></a>[ARM 模板](#tab/arm1)

 [ARM 模板](/azure/azure-resource-manager/templates/overview) 是一个 JavaScript 对象表示法 (JSON) 文件，用于定义项目的基础结构和配置。 模板使用声明性语法。 在声明性语法中，你可以在不编写创建部署的编程命令序列的情况下，描述预期部署。 

ARM 模板允许部署相关资源的组。 在单个模板中，可创建容量预留组和容量预留项。 可通过 Azure 门户、Azure CLI、Azure PowerShell 或持续集成/持续交付 (CI/CD) 管道部署模板。 

如果环境满足先决条件，并且你擅长使用 ARM 模板，那么请使用此[使用容量预留创建虚拟机规模集](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json)模板。 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-virtual-machine-scale-set-to-capacity-reservation-group"></a>将现有虚拟机规模集与容量预留组进行关联 

在公共预览版中，为了将现有统一虚拟机规模集关联到容量预留组，需要先解除分配该规模集，然后在重新分配时进行关联。 这可确保在重新分配时，所有规模集 VM 都使用容量预留。

### <a name="important-notes-on-upgrade-policies"></a>有关升级策略的重要说明 

- **自动升级** - 在此模式中，规模集 VM 实例与容量预留组自动关联，无需你的任何进一步操作。 规模集 VM 重新分配后，会开始使用预留容量。
- **滚动升级** - 在此模式中，规模集 VM 实例与容量预留组进行关联，无需你的任何进一步操作。 不过，它们分批更新，可在两批更新之间暂停一段时间。 规模集 VM 重新分配后，会开始使用预留容量。
- **手动升级** - 在此模式中，当虚拟机规模集附加到容量预留组时，规模集 VM 实例保持原样。 你将需要对每个规模集 VM 进行单独的更新，方法是[使用最新的规模集模型进行升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)。

### <a name="api"></a>[API](#tab/api2)

1. 解除分配虚拟机规模集。 

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. 向规模集模型添加 `capacityReservationGroup` 属性。 向 Microsoft.Compute 提供程序构造以下 PUT 请求：

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
    ```

    在请求正文包含 `capacityReservationGroup` 属性：

    ```json
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
             "capacityReservation": {
                      "capacityReservationGroup": {
                            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
                      }
                }
        }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2) 

1. 解除分配虚拟机规模集。 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup”
    -VMScaleSetName "myVmss”
    ```

1. 将规模集关联到容量预留组。 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
    ```

若要了解详细信息，请转到 Azure PowerShell 命令 [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss)、[Get-AzVmss](/powershell/module/az.compute/get-azvmss) 和 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="view-virtual-machine-scale-set-association-with-instance-view"></a>在实例视图中查看虚拟机规模集关联情况 

将统一虚拟机规模集与容量预留组进行关联后，将针对容量预留进行所有后续 VM 分配。 Azure 会在组中自动查找匹配的容量预留并使用预留的槽。 

### <a name="api"></a>[API](#tab/api3) 

容量预留组实例视图将在 `virtualMachinesAssociated` & `virtualMachinesAllocated` 属性下反映出新的规模集 VM，如下所示： 

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceview&api-version=2021-04-01 
```

```json
{ 
    "name": "<CapacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus" 
}, 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "<CapacityReservationName>", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
                            } 
                        ] 
                    },
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "2021-05-25T15:12:10.4165243+00:00" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell3) 

使用 PowerShell 在实例视图中查看虚拟机规模集和容量预留组关联情况。 

```powershell-interactive
$CapRes=
Get-AzCapacityReservationGroup
-ResourceGroupName <"ResourceGroupName">
-Name <"CapacityReservationGroupName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
``` 

若要了解详细信息，请转到 Azure PowerShell 命令 [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationGroup)。

### <a name="portal"></a>[Portal](#tab/portal3)

1. 打开 [Azure 门户](https://portal.azure.com)
1. 转到容量预留组
1. 在左侧的“设置”下，选择“资源” 
1. 在表中，你将能够查看与容量预留组关联的所有规模集 VM
--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="region-and-availability-zones-considerations"></a>地区和可用性区域注意事项 

虚拟机规模集可在地区创建，或者在一个或多个可用性区域中创建，从而免受数据中心级故障影响。 若要详细了解多区域虚拟机规模集，请参阅[使用可用性区域的虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)。  

 
>[!IMPORTANT]
> 虚拟机规模集和容量预留组的位置（地区和可用性区域）必须匹配，这样才能成功关联。 对于地区性规模集，地区必须在规模集和容量预留组之间匹配。 对于区域性规模集，地区和区域在规模集和容量预留组之间都必须匹配。 


如果某规模集分布在多个区域，那么它始终尝试在包含的可用性区域中均匀部署。 由于这种均匀部署，容量预留组在每个区域应始终具有相同数量的预留 VM。 为了说明这一点为何很重要，请考虑下面的示例。   

在本例中，每个区域预留了不同的数量。 假设虚拟机规模集横向扩展到 75 个实例。 由于规模集将始终尝试跨区域均匀部署，因此 VM 分发应如下所示： 

| 区域  | 预留数量  | 不是。 每个区域中的规模集 VM 数  | 未使用的预留数量  | 过度分配数   |
|---|---|---|---|---|
| 1  | 40  | 25  | 15  | 0  |
| 2  | 20  | 25  | 0  | 5  |
| 3  | 15  | 25  | 0  | 10  |

在此情况下，对于区域 1 中 15 个未使用的实例，规模集会产生额外费用。 横向扩展还依赖于区域 2 中的 5 个 VM 和区域 3 中的 10 个 VM，它们不受容量预留保护。 如果每个区域都预留 25 个容量实例，那么全部 75 个 VM 将受到容量预留保护，部署将不对未使用的实例产生任何额外费用。  

预留可能会过度分配，因此规模集可超出预留限制继续正常缩放。 唯一的区别是，超出预留数量分配的 VM 未涵盖在容量预留 SLA 中。 若要了解详细信息，请转到[容量预留过度分配](capacity-reservation-overallocate.md)。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何取消规模集与容量预留的关联](capacity-reservation-remove-virtual-machine-scale-set.md)