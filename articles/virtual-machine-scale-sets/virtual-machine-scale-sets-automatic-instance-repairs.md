---
title: Azure 虚拟机规模集的自动实例修复
description: 了解如何为规模集中的 VM 实例配置自动修复策略
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 6134e1f4b4823b2198a1ecc316644ef0cd11ca33
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690533"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>适用于 Azure 虚拟机规模集的自动实例修复

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 统一规模集

为 Azure 虚拟机规模集启用自动实例修复，有助于通过维护一组正常运行的实例来实现应用程序的高可用性。 如果发现规模集中的实例在[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)中报告为不正常，则此功能会自动执行实例修复，删除不正常的实例并创建一个新实例来替换它。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自动实例修复的要求

**启用规模集的应用程序运行状况监视**

规模集应启用实例的应用程序运行状况监视。 可以使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)来完成此操作。 一次只能启用其中一个。 应用程序运行状况扩展或负载均衡器探测对虚拟机实例上配置的应用程序终结点进行 ping 操作，以确定应用程序的运行状况。 此运行状况由规模集业务流程协调程序用于监视实例运行状况，并在需要时执行修复。

**配置终结点以提供运行状况**

启用自动实例修复策略之前，请确保规模集实例配置了应用程序终结点，以发出应用程序运行状况。 如果实例在此应用程序终结点上返回状态 200 (OK)，则此实例将标记为“正常”。 在所有其他情况下，实例将标记为“不正常”，包括以下场景：

- 在虚拟机实例中未配置可提供应用程序运行状况的应用程序终结点
- 当应用程序终结点配置不正确
- 当应用程序终结点不可访问

对于标记为“不正常”的实例，规模集会触发自动修复。 请确保在启用自动修复策略之前正确配置应用程序终结点，以避免在配置终结点时执行意外的实例修复。

**规模集中实例的最大数目**

此功能目前仅适用于最多 500 个实例的规模集。 规模集可以部署为单个放置组或多个放置组，但如果为规模集启用了自动实例修复，则实例计数不得超过 500。

**API 版本**

计算 API 版本 2018-10-01 或更高版本支持自动修复策略。

**资源或订阅移动限制**

启用自动修复功能时，目前不支持对规模集进行资源或订阅移动。

**Service Fabric 规模集限制**

Service Fabric 规模集当前不支持此功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自动实例修复如何工作？

自动实例修复功能依赖于规模集中单个实例的运行状况监视。 规模集中的 VM 实例可以配置为使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)发出应用程序运行状况。 如果发现实例不正常，规模集将通过删除不正常的实例并创建一个新实例来替换它来执行修复操作。 可使用最新的虚拟机规模集模型创建新实例。 可以使用 automaticRepairsPolicy 对象在虚拟机规模集模型中启用此功能。

### <a name="batching"></a>批处理

自动实例修复操作是批量执行的。 在任意给定时间，通过自动修复策略修复的实例数量不得超过 5%。 这有助于避免在发现不正常实例时同时删除和重新创建大量实例。

### <a name="grace-period"></a>宽限期

当一个实例因为在规模集上执行了 PUT、PATCH 或 POST 操作（例如重新映像、重新部署、更新等）而进行状态更改操作时，该实例上的任何修复操作都只在等待宽限期之后才执行。 宽限期是指允许实例恢复正常状态的时间。 宽限期在状态更改完成后开始。 这有助于避免任何过早或意外的修复操作。 宽限期适用于规模集中任何新创建的实例（包括由于修复操作而创建的实例）。 宽限期以分钟为单位（指定格式为 ISO 8601），可以使用 automaticRepairsPolicy.gracePeriod 属性进行设置。 宽限期范围为 10 分钟到 90 分钟，默认值为 30 分钟。

### <a name="suspension-of-repairs"></a>暂停修复

虚拟机规模集提供了在需要时临时暂停自动实例修复的功能。 虚拟机规模集的实例视图中属性 orchestrationServices 下自动修复的 serviceState 显示了自动修复的当前状态。 如果选择将规模集加入自动修复，则将参数 serviceState 的值设置为“运行”。 如果为规模集暂停自动修复，则参数 serviceState 将设置为“暂停”。 如果在规模集上定义 automaticRepairsPolicy，但未启用自动修复功能，则参数 serviceState 将设置为“未运行”。

如果用于替换规模集中不正常实例的新创建的实例仍然不正常，即使在重复执行修复操作后仍如此，则平台会将自动修复的 serviceState 更新为“暂停”。 可以通过将自动修复的 serviceState 的值设置为“运行”再次恢复自动修复。 [查看和更新规模集自动修复策略的服务状态](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy)一节中提供了详细说明。

自动实例修复过程如下所示：

1. [应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)对规模集中每个虚拟机内的应用程序终结点执行 ping 操作，以获取每个实例的应用程序运行状况。
2. 如果终结点响应状态为 200 (OK)，则实例将标记为“正常”。 在所有其他情况下（包括无法访问终结点的情况），实例都将标记为“不正常”。
3. 如果发现实例不正常，规模集将触发修复操作，删除不正常的实例并创建一个新实例来替换它。
4. 实例修复批量执行。 在任意给定时间，规模集中要修复的实例不得超过总实例数的 5%。 如果规模集的实例数少于 20 个，则每次对一个不正常的实例进行修复。
5. 上述过程将继续进行，直到修复规模集中所有不正常的实例。

## <a name="instance-protection-and-automatic-repairs"></a>实例保护和自动修复

如果规模集中的实例通过应用其中一个[保护策略](./virtual-machine-scale-sets-instance-protection.md)进行保护，则不会对该实例执行自动修复。 这适用于以下保护策略：“防止进行横向缩减”和“防止进行规模集操作”操作。

## <a name="terminatenotificationandautomaticrepairs"></a>终止通知和自动修复

如果在规模集上启用了[终止通知](./virtual-machine-scale-sets-terminate-notification.md)功能，则在自动修复操作期间，删除不正常的实例将遵循终止通知配置。 终止通知通过 Azure 元数据服务（计划事件）发送，并在配置的延迟超时持续时间内延迟删除实例。 但是，创建新实例来替换不正常的实例不会等待延迟超时完成。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>创建新规模集时启用自动修复策略

若要在创建新规模集时启用自动修复策略，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应为规模集实例正确配置应用程序终结点，以避免在配置终结点时触发意外修复。 对于新创建的规模集，只有在等待宽限期后才会执行实例修复。 若要在规模集中启用自动实例修复，请在虚拟机规模集模型中使用 automaticRepairsPolicy 对象。

还可以使用此[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vmss-automatic-repairs-slb-health-probe)部署虚拟机规模集，同时启用负载均衡器运行状况探测和自动实例修复，宽限期为 30 分钟。

### <a name="azure-portal"></a>Azure 门户

以下步骤将在创建新规模集时启用自动修复策略。

1. 转到“虚拟机规模集”。
1. 选择“+ 添加”，创建新的规模集。
1. 转到“运行状况”选项卡。
1. 找到“运行状况”部分。
1. 启用“监视应用程序运行状况”选项。
1. 找到“自动修复策略”部分。
1. 启用“自动修复”选项。
1. 在“宽限期(分钟)”中，指定以分钟为单位的宽限期，取值范围为 30 到 90 分钟。
1. 创建完新的规模集后，选择“查看 + 创建”按钮。

### <a name="rest-api"></a>REST API

下面的示例演示如何在规模集模型中启用自动实例修复。 使用 API 版本 2018-10-01 或更高版本。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 创建新规模集时，可以启用自动实例修复功能。 此示例脚本使用配置文件演示如何创建模集和相关资源：[创建完整的虚拟机规模集](./scripts/powershell-sample-create-complete-scale-set.md)。 可以通过将参数 EnableAutomaticRepair 和 AutomaticRepairGracePeriod 添加到用于创建规模集的配置对象来配置自动实例修复策略。 以下示例以 30 分钟的宽限期启用这项功能。

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下示例在使用 [az vmss create](/cli/azure/vmss#az_vmss_create) 创建新规模集时启用自动修复策略。 首先创建一个资源组，然后创建一个将自动修复策略宽限期设置为 30 分钟的新规模集。

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

上面的示例使用现有负载均衡器和运行状况探测来监视实例的应用程序运行状况。 如果希望改用应用程序运行状况扩展进行监视，可以创建规模集、配置应用程序运行状况扩展，然后使用 az vmss update 启用自动实例修复策略，如下一节中所述。

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>更新现有规模集时启用自动修复策略

在现有规模集中启用自动修复策略之前，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应为规模集实例正确配置应用程序终结点，以避免在配置终结点时触发意外修复。 若要在规模集中启用自动实例修复，请在虚拟机规模集模型中使用 automaticRepairsPolicy 对象。

更新现有规模集的模型后，确保最新模型应用于该规模的所有实例。 请参阅[如何将 VM 更新到最新的规模集模型](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)获取相关说明。

### <a name="azure-portal"></a>Azure 门户

可以通过 Azure 门户修改现有规模集的自动修复策略。

1. 转到现有虚拟机规模集。
1. 在左侧菜单的“设置”下，选择“运行状况和修复”。
1. 启用“监视应用程序运行状况”选项。
1. 找到“自动修复策略”部分。
1. 启用“自动修复”选项。
1. 在“宽限期(分钟)”中，指定以分钟为单位的宽限期，取值范围为 30 到 90 分钟。
1. 完成后，选择“保存”。

### <a name="rest-api"></a>REST API

以下示例以 40 分钟的宽限期启用此策略。 使用 API 版本 2018-10-01 或更高版本。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 可在现有规模集中修改自动实例修复功能的配置。 以下示例将宽限期更新为 40 分钟。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下示例使用 [az vmss update](/cli/azure/vmss#az_vmss_update) 更新现有规模集的自动实例修复策略。

```azurecli-interactive
az vmss update \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>查看并更新自动实例修复策略的服务状态

### <a name="rest-api"></a>REST API

在虚拟机规模集上使用 API 版本 2019-12-01 或更高版本的[获取实例视图](/rest/api/compute/virtualmachinescalesets/getinstanceview)，以便在属性 orchestrationServices 下查看自动修复的 serviceState。

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

在虚拟机规模集上使用 API 版本 2019-12-01 或更高版本的 setOrchestrationServiceState API，以设置自动修复的状态。 选择将规模集加入到自动修复功能后，可以使用此 API 来暂停或恢复规模集的自动修复。

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI

使用 [get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) cmdlet 查看自动实例修复的 serviceState。

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

使用 [set-orchestration-service-state](/cli/azure/vmss#az_vmss_set_orchestration_service_state) cmdlet 更新自动实例修复的 serviceState。 选择将规模集加入到自动修复功能后，可以使用此 cmdlet 来暂停或恢复规模集的自动修复。

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

结合使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet 和参数 InstanceView，以查看自动实例修复的 ServiceState。

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

使用 Set-AzVmssOrchestrationServiceState cmdlet 可更新自动实例修复的 serviceState。 选择将规模集加入到自动修复功能后，可以使用此 cmdlet 来暂停或恢复规模集的自动修复。

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>疑难解答

**启用自动修复策略失败**

如果收到“BadRequest”错误并显示消息“在‘properties’类型的对象中找不到成员‘automaticRepairsPolicy’”，请检查用于虚拟机规模集的 API 版本。 此功能需要 API 版本 2018-10-01 或更高版本。

**即使在启用策略后实例仍未修复**

实例可能处于宽限期。 这是在实例上发生任何状态更改后，在执行修复之前等待的时间。 这是为了避免任何过早或意外的修复。 修复操作应在实例的宽限期完成后发生。

**查看规模集实例的应用程序运行状况**

你可以对虚拟机规模集中的实例使用[获取实例视图 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) 来查看应用程序运行状况。 使用 Azure PowerShell，可以将 cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) 与 -InstanceView 标志结合使用。 应用程序运行状况在 vmHealth 属性下提供。

在 Azure 门户中，还可以查看运行状况。 转到现有规模集，从左侧菜单中选择“实例”，并查看“运行状况”列，以查看每个规模集实例的运行状况。

## <a name="next-steps"></a>后续步骤

了解如何为规模集配置[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)。
