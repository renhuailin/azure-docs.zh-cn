---
title: Azure 中 VM 和规模集的自动扩展升级
description: 了解如何为 Azure 中的虚拟机和虚拟机规模集启用自动扩展升级。
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a239b362cc7d85b45a5ae0c4f102471ae46cc450
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110673577"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>预览版：Azure 中 VM 和规模集的自动扩展升级

适用于 Azure VM 和 Azure 虚拟机规模集的自动扩展升级以预览版提供。 在 VM 或规模集中启用自动扩展升级后，每当扩展发行商发布了扩展的新版本，该扩展就会自动升级。

 自动扩展升级具有以下特性：
- 支持 Azure VM 和 Azure 虚拟机规模集。 目前不支持 Service Fabric 虚拟机规模集。
- 在可用性优先部署模型中应用升级（详情如下）。
- 对于虚拟机规模集，在一个批次中升级的规模集虚拟机数不超过总数的 20%。 最小批次大小为一个虚拟机。
- 适用于所有 VM 大小，并且适用于 Windows 和 Linux 扩展。
- 随时可以选择停用自动升级。
- 可在任意大小的虚拟机规模集中启用自动扩展升级。
- 每个受支持的扩展将单独注册，可以选择要自动升级哪些扩展。
- 在所有公有云区域中均受支持。


> [!IMPORTANT]
> 自动扩展升级目前为公共预览版。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="how-does-automatic-extension-upgrade-work"></a>自动扩展升级的工作原理是什么？
扩展升级过程会将 VM 上的现有扩展版本替换为扩展发布者为同一扩展发布的新版本。 安装新扩展后，VM 的运行状况会受到监视。 如果 VM 在升级完成后的 5 分钟内处于不正常状态，则扩展版本将回滚到前一个版本。

系统会自动重试失败的扩展更新。 它会每隔几天自动重试，无需用户的干预。

### <a name="availability-first-updates"></a>可用性优先更新
适用于由平台协调的更新的可用性优先模型可确保在多个可用性级别遵循 Azure 中的可用性配置。

对于正在更新的一组虚拟机，Azure 平台将协调更新：

跨区域：
- 更新将以分阶段的方式在整个 Azure 中全局推进，以防出现 Azure 范围的部署失败。
- 一个“阶段”可以涵盖一个或多个区域，仅当前一阶段中符合条件的 VM 成功更新时，更新才会进入下一阶段。
- 地理配对的区域不会并发更新，因此它们不能处于同一区域阶段。
- 更新是否成功是通过跟踪 VM 在更新后的运行状况来衡量的。 VM 运行状况是通过 VM 的平台运行状况指示器跟踪的。 对于虚拟机规模集，将通过应用程序运行状况探测或应用程序运行状况扩展（如果已在规模集中应用）跟踪 VM 运行状况。

在区域内部：
- 位于不同可用性区域中的 VM 不会并发更新。
- 不在可用性集内的单个 VM 将按照尽力而为的原则进行批处理，以避免对订阅中的所有 VM 进行并发更新。  

在“集”内部：
- 公用可用性集或规模集中的所有 VM 不会并发更新。  
- 公用可用性集中的 VM 在更新域边界内更新，跨多个更新域的 VM 不会并发更新。  
- 公用虚拟机规模集中的 VM 将分组成批，并在更新域边界内更新。

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>虚拟机规模集的升级过程
1. 在开始升级过程之前，业务流程协调程序将确保整个规模集内（出于任何原因）不正常的 VM 不会超过 20%。

2. 升级业务流程协调程序将识别要升级的一批 VM 实例。 一个升级批最多可以包含 VM 总数的 20%，最小批大小限制为一个虚拟机。

3. 对于配置了应用程序运行状况探测或应用程序运行状况扩展的规模集，升级过程最多会等待 5 分钟（或定义的运行状况探测配置）来让 VM 变正常，然后再升级下一批。 如果 VM 在升级后未恢复其正常运行状况，则默认情况下，会在该 VM 上重新安装前一个扩展版本。

4. 升级业务流程协调程序还会跟踪升级后变为不正常的 VM 百分比。 如果升级过程中超过 20% 的已升级实例变得不正常，升级将会停止。

上述过程会持续到升级了规模集中的所有实例为止。

规模集升级业务流程协调程序在升级每个批之前会检查规模集总体运行状况。 升级某一批时，可能有其他并发的计划内或计划外维护活动，这些活动可能会影响规模集虚拟机的运行状况。 在这种情况下，如果超过 20% 的规模集实例不正常，则当前批结束时，规模集升级将会停止。

## <a name="supported-extensions"></a>支持的扩展
自动扩展升级预览版支持以下扩展（以后会定期添加更多扩展）：
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) 和 [Linux](./extensions/agent-dependency-linux.md)
- [应用程序运行状况扩展](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows 和 Linux


## <a name="enabling-preview-access"></a>启用预览版访问
启用预览版功能需要根据下面的详述为每个订阅一次性选用 AutomaticExtensionUpgradePreview 功能。

### <a name="rest-api"></a>REST API
以下示例说明如何为订阅启用预览版：

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为订阅启用预览版。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
使用 [az feature register](/cli/azure/feature#az_feature_register) 为订阅启用预览版。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>启用自动扩展升级
若要为扩展启用自动扩展升级，必须确保属性 enableAutomaticUpgrade 设置为 true 并已分别添加到每个扩展定义 。


### <a name="rest-api-for-virtual-machines"></a>适用于虚拟机的 REST API
若要在 Azure VM 上为扩展（在本示例中为 Dependency Agent 扩展）启用自动扩展升级，请使用以下命令：

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

### <a name="rest-api-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 REST API
使用以下命令将扩展添加到规模集模型：

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

### <a name="azure-powershell-for-virtual-machines"></a>适用于虚拟机的 Azure PowerShell
使用 [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) cmdlet：

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


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 Azure PowerShell
使用 [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet 将扩展添加到规模集模型：

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

添加扩展后，使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) 更新规模集。


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

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 Azure CLI
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

## <a name="extension-upgrades-with-multiple-extensions"></a>对多个扩展进行扩展升级

一个 VM 或虚拟机规模集可以包含多个已启用自动扩展升级的扩展。 同一个 VM 或规模集还可以包含其他未启用自动扩展升级的扩展。  

如果虚拟机有多个可用的扩展升级，可将这些升级一起进行批处理，但每个扩展升级将单独在一个虚拟机上应用。 一个扩展发生失败不会影响到可能正在升级的其他扩展。 例如，如果已计划升级两个扩展，而第一个扩展升级失败，在这种情况下，仍会升级第二个扩展。

当 VM 或虚拟机规模集包含配置了[扩展排序](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)的多个扩展时，也可以应用自动扩展升级。 扩展排序适用于 VM 的首次部署，以后对扩展进行的扩展升级将独立应用。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解应用程序运行状况扩展](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
