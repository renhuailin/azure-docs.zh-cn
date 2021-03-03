---
title: Azure 中 Vm 和规模集的自动扩展升级
description: 了解如何在 Azure 中为虚拟机和虚拟机规模集启用自动扩展升级。
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrades
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: 104eada6dc342c21b8da2f409756e9f34c103936
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668327"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>预览版： Azure 中 Vm 和规模集的自动扩展升级

Azure Vm 和 Azure 虚拟机规模集的预览中提供了自动扩展升级功能。 当在 VM 或规模集上启用自动扩展升级时，只要扩展发布服务器释放该扩展插件的新版本，就会自动升级扩展。

 自动扩展升级具有以下功能：
- 支持 Azure Vm 和 Azure 虚拟机规模集。 当前不支持 Service Fabric 虚拟机规模集。
- 升级在可用性优先的部署模型中应用 (下面) 详细说明。
- 对于虚拟机规模集，不超过20% 的规模集虚拟机将在单个批处理中升级。 最小批处理大小为一台虚拟机。
- 适用于所有 VM 大小，适用于 Windows 和 Linux 扩展。
- 你可以随时选择退出自动升级。
- 可在任意大小的虚拟机规模集上启用自动扩展升级。
- 每个受支持的扩展都是单独注册的，你可以选择自动升级的扩展。
- 在所有公有云区域中均受支持。


> [!IMPORTANT]
> 自动扩展升级目前为公共预览版。 使用下述公共预览功能需要使用选择过程。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="how-does-automatic-extension-upgrade-work"></a>自动扩展升级如何工作？
扩展升级过程会将 VM 上的现有扩展版本替换为扩展发布服务器发布时具有相同扩展插件的新版本。 安装新扩展后，将监视 VM 的运行状况。 如果在升级完成后的5分钟内 VM 未处于正常状态，则扩展版本将回滚到以前的版本。

自动重试失败的扩展更新。 每隔几天自动尝试一次，无需用户干预。

### <a name="availability-first-updates"></a>可用性优先更新
适用于平台的更新的第一个可用性模型将确保 Azure 中的可用性配置跨多个可用性级别。

对于正在进行更新的一组虚拟机，Azure 平台将协调更新：

**跨区域：**
- 更新将以分阶段的方式跨全局移动，以防止 Azure 范围内的部署失败。
- "阶段" 可以有一个或多个区域，并且仅当上一阶段中符合条件的 Vm 成功更新时，更新才会移动到各个阶段。
- 地域配对区域不会同时更新，并且不能在同一区域阶段中进行更新。
- 更新的成功率是通过跟踪 VM 后期更新的运行状况来衡量的。 VM 运行状况通过 VM 的平台运行状况指标进行跟踪。 对于虚拟机规模集，如果应用于规模集，则会通过应用程序运行状况探测或应用程序运行状况扩展跟踪 VM 运行状况。

**在区域内：**
- 不同可用性区域中的 Vm 不会同时更新。
- 不属于可用性集的单个 Vm 会尽力进行批处理，以避免对订阅中的所有 Vm 进行并发更新。  

**在 "集" 内：**
- 公共可用性集中的所有 Vm 或规模集不会同时更新。  
- 公共可用性集中的 Vm 在更新域边界内更新，不会同时更新多个更新域中的 Vm。  
- 公用虚拟机规模集中的 Vm 按批分组，并在更新域边界内更新。

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>虚拟机规模集的升级过程
1. 在开始升级过程之前，协调器将确保整个规模集中的 Vm 数不能超过 20% (出于任何原因) 不正常。

2. 升级 orchestrator 会确定要升级的 VM 实例的批处理。 升级批处理最多可以有20% 的 VM 总数，受限于一个虚拟机的最小批处理大小。

3. 对于已配置应用程序运行状况探测或应用程序运行状况扩展的规模集，升级最多会等待5分钟 (或定义的运行状况探测配置) ，以便在升级下一批之前，VM 变为正常。 如果 VM 不在升级后恢复其运行状况，则默认情况下会重新安装 VM 上的以前的扩展版本。

4. 升级 orchestrator 还跟踪升级后变为不正常的 Vm 的百分比。 如果升级过程中超过 20% 的已升级实例变得不正常，升级将会停止。

上述过程会持续到升级了规模集中的所有实例为止。

规模集升级 orchestrator 会在升级每个批次之前检查总体规模集的运行状况。 升级批时，可能会影响规模集虚拟机的运行状况的其他并发计划或计划外维护活动。 在这种情况下，如果规模集的实例数超过20%，则规模集升级将在当前批处理结束时停止。

## <a name="supported-extensions"></a>支持的扩展
自动扩展升级的预览支持以下扩展 (并且会定期添加更多的) ：
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) 和 [Linux](./extensions/agent-dependency-linux.md)
- [应用程序运行状况扩展](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows 和 Linux


## <a name="enabling-preview-access"></a>启用预览版访问
若要启用预览功能，只需为每个订阅 **AutomaticExtensionUpgradePreview** 功能一次，如下所述。

### <a name="rest-api"></a>REST API
下面的示例说明如何为订阅启用预览：

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为你的订阅启用预览。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
使用 [az feature register](/cli/azure/feature#az-feature-register) 为你的订阅启用预览。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>启用自动扩展升级
若要为扩展启用自动扩展升级，必须确保将属性 *enableAutomaticUpgrade* 设置为 *true* 并分别添加到每个扩展定义。


### <a name="rest-api-for-virtual-machines"></a>虚拟机的 REST API
若要为扩展启用自动扩展升级 (在此示例中，Dependency Agent 扩展) Azure VM，请使用以下内容：

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>虚拟机规模集的 REST API
使用以下内容将扩展添加到规模集模型：

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>虚拟机的 Azure PowerShell
使用 [AzVMExtension](/powershell/module/az.compute/set-azvmextension) cmdlet：

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>虚拟机规模集的 Azure PowerShell
使用 [AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet 将扩展添加到规模集模型：

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

添加扩展后，使用 [AzVmss](/powershell/module/az.compute/update-azvmss) 更新规模集。


### <a name="azure-cli-for-virtual-machines"></a>适用于虚拟机的 Azure CLI
使用 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) cmdlet：

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>虚拟机规模集的 Azure CLI
使用 [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) cmdlet 将扩展添加到规模集模型：

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>具有多个扩展的扩展升级

VM 或虚拟机规模集可以有多个启用了自动扩展升级的扩展。 同一 VM 或规模集也可以包含未启用自动扩展升级的其他扩展。  

如果有多个扩展升级可用于虚拟机，则可以将升级一起分批进行，但会在虚拟机上单独应用每个扩展升级。 一个扩展的失败不会影响可能正在升级)  (的其他扩展。 例如，如果为升级计划了两个扩展，并且第一个扩展升级失败，则仍将升级第二个扩展。

当 VM 或虚拟机规模集具有多个配置有 [扩展序列](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)的扩展时，也可以应用自动扩展升级。 扩展排序适用于首次部署 VM，并且扩展的任何未来扩展升级都是独立应用的。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解应用程序运行状况扩展](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
