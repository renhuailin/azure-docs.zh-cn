---
title: 将虚拟机与容量预留组进行关联（预览版）
description: 了解如何将新的或现有的虚拟机与容量预留组进行关联。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 6253039c2c256e421815cad30f06780bc6c89694
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774341"
---
# <a name="associate-a-vm-to-a-capacity-reservation-group-preview"></a>将 VM 与容量预留组进行关联（预览版） 

本文将指导你完成将新的或现有的虚拟机关联到容量预留组的步骤。 要了解有关容量预留的详细信息，请参阅[概述](capacity-reservation-overview.md)一文。 

> [!IMPORTANT]
> 容量预留功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="register-for-capacity-reservation"></a>注册容量预留 

必须先[注册预览版订阅](capacity-reservation-overview.md#register-for-capacity-reservation)，然后才能使用容量预留功能。 注册可能需要几分钟才能完成。 可使用 Azure CLI 或 PowerShell 完成功能注册。

## <a name="associate-a-new-vm"></a>关联新 VM

要将新 VM 与容量预留组相关联，则必须将该组显式引用为虚拟机的属性。 此引用会保护组中的匹配预留，避免不打算使用它的不太关键的应用程序和工作负载意外使用它。  

### <a name="api"></a>[API](#tab/api1)

若要向 VM 添加 `capacityReservationGroup` 属性，请向 Microsoft.Compute 提供程序构造以下 PUT 请求：

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
```

在请求正文包含 `capacityReservationGroup` 属性，如下所示：

```json
{ 
  "location": "eastus", 
  "properties": { 
    "hardwareProfile": { 
      "vmSize": "Standard_D2s_v3" 
    }, 
    … 
   "CapacityReservation":{ 
    "capacityReservationGroup":{ 
        "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}" 
    } 
    "storageProfile": { 
    … 
    }, 
    "osProfile": { 
    … 
    }, 
    "networkProfile": { 
     …     
    } 
  } 
} 
```

### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. 打开 [Azure 门户](https://portal.azure.com)
1. 在搜索栏中，键入“虚拟机”
1. 在“服务”下，选择“虚拟机”
1. 在“虚拟机”页中选择“创建”，然后选择“虚拟机” 
1. 在“基本信息”选项卡中的“项目详细信息”下，选择正确的订阅，然后选择创建新的“资源组”或使用现有资源组  
1. 在“实例详细信息”下，键入“虚拟机名称”，然后选择“区域” 
1. 选择“映像”和“VM 大小” 
1. 在“管理员帐户”下，提供“用户名”和“密码” 
    1. 密码长度必须至少为 12 个字符，且符合定义的复杂性要求
1. 在“入站端口规则”下，选择“允许所选端口”，然后从下拉列表中选择“RDP (3389)”和“HTTP (80)”  
1. 转到“高级”部分
1. 在“容量预留”下拉列表中，选择要与 VM 关联的容量预留组
1. 选择“查看 + 创建”按钮 
1. 运行验证之后，请选择“创建”按钮 
1. 部署完成后，选择“转到资源”


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

使用 `New-AzVM` 创建一个新的 VM 并添加 `CapacityReservationGroupId` 属性，将其与现有容量预留组进行关联。 以下示例在“美国东部”位置创建一个 Standard_D2s_v3 VM，并将 VM 关联到容量预留组。

```powershell-interactive
New-AzVm
-ResourceGroupName "myResourceGroup"
-Name "myVM"
-Location "eastus"
-VirtualNetworkName "myVnet"
-SubnetName "mySubnet"
-SecurityGroupName "myNetworkSecurityGroup"
-PublicIpAddressName "myPublicIpAddress"
-OpenPorts 80,3389
-Size "Standard_D2s_v3"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
```

要了解详细信息，请转到 Azure PowerShell 命令 [New-AzVM](/powershell/module/az.compute/new-azvm)。

### <a name="arm-template"></a>[ARM 模板](#tab/arm1)

 [ARM 模板](../azure-resource-manager/templates/overview.md) 是一个 JavaScript 对象表示法 (JSON) 文件，用于定义项目的基础结构和配置。 模板使用声明性语法。 在声明性语法中，你可以在不编写创建部署的编程命令序列的情况下，描述预期部署。 

ARM 模板允许部署相关资源的组。 在单个模板中，可创建容量预留组和容量预留。 可通过 Azure 门户、Azure CLI、Azure PowerShell 或持续集成/持续交付 (CI/CD) 管道部署模板。 

如果环境满足先决条件，并且你擅长使用 ARM 模板，那么请使用此[使用容量预留创建 VM](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json) 模板。 


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-vm"></a>关联现有 VM 

当容量预留处于预览状态时，若要将现有 VM 关联到容量预留组，则需要先解除分配 VM，然后在重新分配时进行关联。 此过程可确保 VM 使用预留中的一个空白点。 

### <a name="api"></a>[API](#tab/api2)

1. 解除分配 VM。 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. 将 `capacityReservationGroup` 属性添加到 VM。 向 Microsoft.Compute 提供程序构造以下 PUT 请求：

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
    ```

    在请求正文包含 `capacityReservationGroup` 属性： 
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
            }
        }
    }
    }
    ```


### <a name="portal"></a>[Portal](#tab/portal2)

1. 打开 [Azure 门户](https://portal.azure.com)
1. 转到到虚拟机
1. 选择左侧的“概述”
1. 选择页面顶部的“停止”以解除分配 VM 
1. 在左侧转到“配置”
1. 在“容量预留组”下拉列表中，选择要与 VM 关联的组 


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. 解除分配 VM

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

1. 将 VM 与容量预留组进行关联

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId "subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}"
    ```

若要了解详细信息，请转到 Azure PowerShell 命令 [Stop-AzVM](/powershell/module/az.compute/stop-azvm)、[Get-AzVM](/powershell/module/az.compute/get-azvm) 和 [Update-AzVM](/powershell/module/az.compute/update-azvm)。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="view-vm-association-with-instance-view"></a>查看 VM 与实例视图的关联 

设置 `capacityReservationGroup` 属性后，VM 和组之间就存在关联了。 Azure 会在组中自动查找匹配的容量预留并使用预留的槽。 容量预留组的实例视图将在 `virtualMachinesAllocated` 属性下反映出新的 VM，如下所示： 

### <a name="api"></a>[API](#tab/api3)

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{capacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01 
```

```json
{
   "name":"{CapacityReservationGroupName}",
   "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}",
   "type":"Microsoft.Compute/capacityReservationGroups",
   "location":"eastus",
   "properties":{
      "capacityReservations":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/ {CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}"
         }
      ],
      "virtualMachinesAssociated":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
         }
      ],
      "instanceView":{
         "capacityReservations":[
            {
               "name":"{CapacityReservationName}",
               "utilizationInfo":{
                  "virtualMachinesAllocated":[
                     {
                        "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
                     }
                  ]
               },
               "statuses":[
                  {
                     "code":"ProvisioningState/succeeded",
                     "level":"Info",
                     "displayStatus":"Provisioning succeeded",
                     "time":"2021-05-25T15:12:10.4165243+00:00"
                  }
               ]
            }
         ]
      }
   }
} 
``` 
### <a name="powershell"></a>[PowerShell](#tab/powershell3)

```powershell-interactive
$CapRes=
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName] <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
```

要了解详细信息，请转到 Azure PowerShell 命令 [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)。


### <a name="portal"></a>[Portal](#tab/portal3)

1. 打开 [Azure 门户](https://portal.azure.com)
1. 转到容量预留组
1. 在左侧的“设置”下，选择“资源” 
1. 查看该表以查看与容量预留组关联的所有 VM  

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [删除 VM 与容量预留组的关联](capacity-reservation-remove-vm.md)