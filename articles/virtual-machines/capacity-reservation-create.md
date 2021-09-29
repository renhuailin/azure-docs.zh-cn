---
title: 在 Azure 中创建容量预留（预览版）
description: 了解如何通过创建容量预留来预留 Azure 区域或可用性区域中的计算容量。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: db9b8b71ddbdccb2cdc4893ce7ea041304bafba9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776607"
---
# <a name="create-a-capacity-reservation-preview"></a>创建容量预留（预览版）

容量预留始终作为容量预留组的一部分创建。 如果合适的组尚不存在，则第一步是创建一个组，然后创建预留。 成功创建后，预留可立即供虚拟机使用。 只要不删除预留，就会预留该容量供你使用。     

由于格式正确的容量预留组请求不会预留任何容量，因此该请求应始终成功。 它仅作为预留的容器。 但是，如果没有 VM 系列所需的配额，或者 Azure 没有足够的容量来满足请求，则容量预留请求可能会失败。 要么请求更多配额，要么尝试使用不同的 VM 大小、位置或区域组合。 

容量预留创建完全成功，或者完全失败。 对于预留 10 个实例的请求，仅在可以分配 10 个实例时才返回成功， 否则，容量预留创建将失败。 

> [!IMPORTANT]
> 容量预留功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="register-for-capacity-reservation"></a>注册容量预留 

必须先[注册预览版订阅](capacity-reservation-overview.md#register-for-capacity-reservation)，然后才能使用容量预留功能。 注册可能需要几分钟才能完成。 可以使用 Azure CLI 或 PowerShell 来完成功能注册。


## <a name="considerations"></a>注意事项

容量预留必须满足以下规则： 
- location 参数必须与父容量预留组的 location 属性匹配。 如果不匹配，将导致错误。 
- VM 大小必须在目标区域中可用， 否则，预留创建将失败。 
- 订阅必须具有足够的批准配额，该配额等于或大于为 VM 系列和整个区域预留的 VM 数量。 如果需要，请[请求更多配额](../azure-portal/supportability/per-vm-quota-requests.md)。
- 对于给定的 VM 大小，每个容量预留组只能有一个预留。 例如，只能为 VM 大小 `Standard_D2s_v3` 创建一个容量预留。 如果尝试在同一容量预留组中为 `Standard_D2s_v3` 创建第二个预留，将导致错误。 但是，可以在同一组中为其他 VM 大小（例如 `Standard_D4s_v3`、`Standard_D8s_v3` 等）创建另一个预留。  
- 对于支持区域的容量预留组，每种预留类型由 VM 大小和区域的组合定义 。 例如，支持 `Zone 1` 中 `Standard_D2s_v3` 的一个容量预留、`Zone 2` 中 `Standard_D2s_v3` 的另一个容量预留以及 `Zone 3` 中 `Standard_D2s_v3` 的第三个容量预留。


## <a name="create-a-capacity-reservation"></a>创建容量预留 

### <a name="api"></a>[API](#tab/api1)

1. 创建容量预留组 

    要创建容量预留组，请在 Microsoft.Compute 提供程序上构造以下 PUT 请求： 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}&api-version=2021-04-01
    ``` 
    
    在请求正文中，添加以下命令： 
    
    ```json
    { 
      "location":"eastus"
    } 
    ```
    
    创建此组以包含美国东部位置的预留。 
    
    在此示例中，由于创建时未指定区域，因此该组将仅支持区域性预留。 要创建区域组，请在请求正文中传递一个额外的参数 zones，如下所示： 
    
    ```json
    { 
      "location":"eastus",
      "zones": ["1", "2", "3"] 
    } 
    ```
 
1. 创建容量预留 

    要创建预留，请在 Microsoft.Compute 提供程序上构造以下 PUT 请求： 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01 
    ```
    
    在请求正文中，添加以下命令： 
    
    ```json
    { 
      "location": "eastus", 
      "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
      }, 
     "tags": { 
            "environment": "testing" 
    } 
    ```
    
    上述请求在美国东部位置为 D2s_v3 VM 大小创建 5 个预留。 


### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. 打开 [Azure 门户](https://portal.azure.com)
1. 在搜索栏中，输入“容量预留组”
1. 从选项中选择“容量预留组”
1. 选择“创建”
1. 在“基本”选项卡下，创建容量预留组：
    1. 选择“订阅”
    1. 选择或创建“资源组”
    1. 对组进行命名 
    1. 选择“区域” 
    1. （可选）选择“可用性区域”，或选择不指定任何区域并允许 Azure 为你选择
1. 选择“下一步”
1. 在“预留”选项卡下，至少创建一个容量预留：
    1. 为每个预留指定“预留名称”、VM 实例的数量，并选择唯一的“VM 大小”  
    1. “费用/月”列将根据你的选择显示账单信息
1. 选择“下一步”
1. 在“标记”选项卡下，可选择性地创建标记
1. 选择“下一步” 
1. 在“查看 + 创建”选项卡下，查看容量预留组信息
1. 选择“创建”


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 在可创建容量预留之前，请使用 `New-AzResourceGroup` 创建资源组。 以下示例在美国东部位置创建资源组 myResourceGroup。

    ```powershell-interactive
    New-AzResourceGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    ```

1. 立即使用 `New-AzCapacityReservationGroup` 创建容量预留组。 以下示例在美国东部位置为 3 个可用性区域创建组 myCapacityReservationGroup。

    ```powershell-interactive
    New-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1","2","3"
    -Name "myCapacityReservationGroup"
    ```

1. 创建容量预留组后，使用 `New-AzCapacityReservation` 创建新的容量预留。 以下示例在美国东部位置区域 1 中为 Standard_D2s_v3 VM 大小创建 5 个 myCapacityReservation。

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 5
    ```

要了解详细信息，请转到 Azure PowerShell 命令 [New-AzResourceGroup](/powershell/module/az.Resources/new-azresourcegroup)、[New-AzCapacityReservationGroup](/powershell/module/az.compute/new-azcapacityreservationgroup) 和 [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation)。


### <a name="arm-template"></a>[ARM 模板](#tab/arm1)

 [ARM 模板](../azure-resource-manager/templates/overview.md) 是一个 JavaScript 对象表示法 (JSON) 文件，用于定义项目的基础结构和配置。 模板使用声明性语法。 在声明性语法中，你可以在不编写创建部署的编程命令序列的情况下，描述预期部署。 

ARM 模板允许部署相关资源的组。 在单个模板中，可创建容量预留组和容量预留。 可通过 Azure 门户、Azure CLI、Azure PowerShell 或持续集成/持续交付 (CI/CD) 管道部署模板。 

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请使用以下任一模板： 

- [创建区域性容量预留](https://github.com/Azure/on-demand-capacity-reservation/blob/main/ZonalCapacityReservation.json)
- [创建具有容量预留的 VM](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json)
- [创建具有容量预留的虚拟机规模集](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json)


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="check-on-your-capacity-reservation"></a>查看容量预留 

成功创建后，容量预留可立即供 VM 使用。 

### <a name="api"></a>[API](#tab/api2)

```rest
GET  
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```
 
```json
{ 
    "name": "<CapacityReservationName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}", 
    "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
    "location": "eastus", 
    "tags": { 
        "environment": "testing" 
    }, 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
    }, 
    "properties": { 
        "reservationId": "<reservationId>", 
         "provisioningTime": "<provisioningTime>", 
         "provisioningState": "Updating" 
    } 
} 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

查看容量预留：

```powershell-interactive
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
```

要查找 VM 大小和预留数量，请使用以下命令： 

```powershell-interactive
$CapRes =
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">

$CapRes.sku
```

要了解详细信息，请转到 Azure PowerShell 命令 [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)。

### <a name="portal"></a>[Portal](#tab/portal3)

1. 打开 [Azure 门户](https://portal.azure.com)
1. 在搜索栏中，输入“容量预留组”
1. 从选项中选择“容量预留组”
1. 从列表中，选择刚刚创建的容量预留组名称
1. 选择左侧的“概述”
1. 选择“预留”
1. 在此视图中，可以看到组中的所有预留以及 VM 大小和预留数量

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何修改容量预留](capacity-reservation-modify.md)
