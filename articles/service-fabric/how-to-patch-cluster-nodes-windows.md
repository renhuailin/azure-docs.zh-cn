---
title: 在 Service Fabric 群集中修补 Windows 操作系统
description: 以下介绍如何启用自动 OS 映像升级以修补 Windows 上运行的 Service Fabric 群集节点。
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 5a3b2c78f0a7a3103c2fc56fe518739ac648ad31
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796488"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>在 Service Fabric 群集中修补 Windows 操作系统

[在虚拟机规模集上执行自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)是在 Azure 中修补操作系统的最佳做法。 基于虚拟机规模集的自动 OS 映像升级需要在规模集上具有白银或更高持续性级别。

### <a name="requirements-for-automatic-os-image-upgrades-by-virtual-machine-scale-sets"></a>虚拟机规模集自动 OS 映像升级的要求

-   Service Fabric [持久性级别](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) 为 Silver 或 Gold，而不是 Bronze。
-   根据规模集模型定义，Service Fabric 扩展必须具有 TypeHandlerVersion 1.1 或更高版本。
-   根据规模集模型定义，Service Fabric 群集与 Service Fabric 扩展的持久性级别应相同。
- 虚拟规模集不需要额外的运行状况探测，也不需要使用应用程序运行状况扩展。

请确保 Service Fabric 群集与 Service Fabric 扩展上的持久性设置匹配，因为不匹配将导致升级错误。 可以通过[此页](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)上概述的指导原则来修改持久性级别。

在青铜级持续性下，无法进行自动 OS 映像升级。 虽然不推荐将[补丁协调应用程序](service-fabric-patch-orchestration-application.md)（仅适用于非 Azure 托管群集）用于白银或更高持续性级别，但对于 Service Fabric 升级域，只能通过它自动执行 Windows 更新。

若要从“修补业务流程应用程序”切换到“自动 OS 映像升级”，必须先弃用“修补业务流程应用程序”。

## <a name="enable-auto-os-upgrades-and-disable-windows-update"></a>启用自动 OS 升级并禁用 Windows 更新

启用自动 OS 更新时，还需在部署模板中禁用 Windows 更新。 部署这些更改后，规模集中的所有计算机都将重置映像，并且将为自动更新启用规模集。

> [!IMPORTANT]
> Service Fabric 不支持虚拟机内升级，其中，Windows 更新在未更换 OS 磁盘的情况下应用操作系统修补程序。


1. 在部署模板中启用自动 OS 映像升级并禁用 Windows 更新：
 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ```

1. 更新规模集模型。 此配置更改后，需要对所有计算机重置映像以更新规模集模型，使所做的更改生效：

    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ```

## <a name="next-steps"></a>后续步骤

了解如何[在虚拟机规模集上启用自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)。
