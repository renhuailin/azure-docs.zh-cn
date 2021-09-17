---
title: 使用 PowerShell 为 Azure 虚拟机规模集上的 OS 映像升级进行维护控制
description: 了解如何使用维护控制和 PowerShell 来控制何时向 Azure 虚拟机规模集推出自动 OS 映像升级。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 312acb2715e76587ac213f73c86e785afe2eadcb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695269"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>使用 PowerShell 为 Azure 虚拟机规模集上的 OS 映像升级进行维护控制

适用于：:heavy_check_mark: 统一规模集

利用维护控制，可以决定何时将自动来宾 OS 映像升级应用到虚拟机规模集。 本主题介绍维护控制的 Azure PowerShell 选项。 若要了解如何使用维护控制，请参阅 [Azure 虚拟机规模集的维护控制](virtual-machine-scale-sets-maintenance-control.md)。


## <a name="enable-the-powershell-module"></a>启用 PowerShell 模块

确保 `PowerShellGet` 为最新版本。    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

安装 `Az.Maintenance` PowerShell 模块。     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

如果在本地安装，请确保以管理员身份打开 PowerShell 提示符。

系统还可能会要求你确认是否要从不受信任的存储库进行安装。 键入 `Y` 或选择“全是”以安装该模块。

## <a name="connect-to-an-azure-account"></a>连接到 Azure 帐户

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 和 [Set-AzAccount](/powershell/module/az.accounts/set-azcontext) 连接到所需的 Azure 帐户。

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>创建维护配置

创建一个资源组作为适用于配置的容器。 在此示例中，在 eastus2 中创建了名为 myMaintenanceRG 的资源组 。 如果已有要使用的资源组，可跳过这部分。 只需在其余示例中将资源组名称替换为你自己的资源组。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

使用 [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 创建维护配置。 此示例创建名为 myConfig 的维护配置，该配置的范围限定为 OS 映像。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> 维护持续时间必须为 5 小时或更长时间。 必须将维护”重复周期”设置为“天”。

使用 `-MaintenanceScope OSImage` 会确保将维护配置用于控制对来宾 OS 的更新。

如果尝试创建同名的但位于不同位置的配置，则会收到错误。 配置名称对于资源组必须是唯一的。

可以使用 [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration) 来查询可用的维护配置。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>将虚拟机规模集关联到维护配置

无论维护配置的区域和订阅如何，虚拟机规模集都可以关联到任何维护配置。 通过选择使用维护配置，将会在下一个可用维护时段自动安排规模集的新 OS 映像更新。

请使用 [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) 将虚拟机规模集与维护配置相关联。

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>启动自动 OS 升级

可以为每个要使用维护控制的虚拟机规模集启用自动 OS 升级。 有关在虚拟机规模集上启用自动 OS 升级的详细信息，请参阅 [Azure 虚拟机规模集自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure 中运行的虚拟机的维护和更新](maintenance-and-updates.md)
