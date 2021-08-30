---
title: Azure 虚拟机规模集的自动 OS 映像升级
description: 了解如何自动升级规模集中 VM 实例上的 OS 映像
author: mayanknayar
ms.author: manayar
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: automatic-os-upgrade
ms.date: 07/29/2021
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b080c741276233e671d5724b3ee72cc7b4738446
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751732"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure 虚拟机规模集自动 OS 映像升级

在规模集上启用自动 OS 映像升级有助于简化更新管理，这样可以安全自动升级规模集中所有实例的 OS 磁盘。

自动 OS 升级具有以下特征：

- 配置后，映像发布者发布的最新 OS 映像将自动应用于规模集，无需用户干预。
- 每次发布者发布新映像时，以滚动方式分批升级实例。
- 与应用程序运行状况探测和[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)集成。
- 适用于所有 VM 大小以及 Windows和 Linux 映像，包括通过[共享映像库](../virtual-machines/shared-image-galleries.md)获取的自定义映像。
- 可以随时取消自动升级（也可以手动启动 OS 升级）。
- VM 的 OS 磁盘将被替换为使用最新映像版本创建的新 OS 磁盘。 运行配置的扩展和自定义数据脚本，同时保留永久性数据磁盘。
- 支持[扩展定序](virtual-machine-scale-sets-extension-sequencing.md)。
- 可在任意大小的规模集上启用。

## <a name="how-does-automatic-os-image-upgrade-work"></a>自动 OS 映像升级的工作原理是什么？

升级工作原理是将 VM 的 OS 磁盘替换为使用最新映像版本创建的新磁盘。 所配置的任何扩展和自定义数据脚本将在 OS 磁盘上运行，同时保留数据磁盘。 为了尽量减少应用程序停机时间，将分批进行升级，在任一时间，规模集内正在进行升级的计算机不会超过 20%。

可以集成 Azure 负载均衡器应用程序运行状况探测或[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)，以便在升级后跟踪应用程序的运行状况。 我们建议整合应用程序检测信号来验证升级是否成功。

### <a name="availability-first-updates"></a>可用性优先更新
下面所述的用于平台协调式更新的可用性优先模型可确保在多个可用性级别遵循 Azure 中的可用性配置。

跨区域：
- 更新将以分阶段的方式在整个 Azure 中全局推进，以防出现 Azure 范围的部署失败。
- 一个“阶段”可以涵盖一个或多个区域，仅当前一阶段中符合条件的 VM 成功更新时，更新才会进入下一阶段。
- 地理配对的区域不会并发更新，因此它们不能处于同一区域阶段。
- 更新是否成功是通过跟踪 VM 在更新后的运行状况来衡量的。

在区域内部：
- 位于不同 Azure 可用性区域中的 VM 不会使用同一更新同时进行更新。

在“集”内部：
- 公用规模集中的所有 VM 不会同时更新。  
- 公用虚拟机规模集中的 VM 将分组成批，并在更新域边界内更新，如下所述。

将会遵循平台协调式更新过程每月推出受支持的 OS 平台映像升级。 对于通过共享映像库获取的自定义映像，仅当已将新映像发布并[复制](../virtual-machines/shared-image-galleries.md#replication)到该规模集的区域时，才为特定的 Azure 区域启动映像升级。

### <a name="upgrading-vms-in-a-scale-set"></a>升级规模集中的 VM

规模集的区域可以通过适用于平台映像的可用性优先过程或者通过复制共享映像库的新自定义映像版本来获取映像升级。 然后，按如下所述将映像升级分批应用于单个规模集：
1. 在开始升级过程之前，业务流程协调程序将确保整个规模集中（出于任何原因）不正常的实例不会超过 20%。
2. 升级业务流程协调程序会标识要升级的 VM 实例的批，其中任何一个批最多可以包括占实例总数的 20% 的实例，具体取决于一个虚拟机的最小批大小。 没有最小规模集大小的要求，包含 5 个或更少实例的规模集的每个升级批包含 1 个 VM（最小批大小）。
3. 所选升级批中每个 VM 的 OS 磁盘将替换为基于最新映像创建的新 OS 磁盘。 规模集模型中的所有指定扩展和配置都将应用于已升级的实例。
4. 对于配置了应用程序运行状况探测或应用程序运行状况扩展的规模集，升级最多等待 5 分钟来等待实例变得正常，然后继续升级下一批。 如果实例在升级后 5 分钟内未恢复其运行状况，则默认情况下将还原该实例先前的 OS 磁盘。
5. 升级业务流程协调程序还会跟踪升级后不正常的实例百分比。 如果升级过程中超过 20% 的已升级实例变得不正常，升级将会停止。
6. 上述过程会持续到升级了规模集中的所有实例为止。

规模集 OS 升级业务流程协调程序在升级每个批之前会检查规模集总体运行状况。 升级某一批时，可能有其他并发的计划内或计划外维护活动影响规模集实例的运行状况。 在这种情况下，如果超过 20% 的规模集实例不正常，则当前批结束时，规模集升级将会停止。

> [!NOTE]
>自动 OS 升级不会升级规模集上的引用映像 SKU。 若要更改 SKU（例如将 Ubuntu 16.04-LTS 更改为 18.04-LTS），必须直接通过所需的映像 SKU 更新[规模集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)。 无法更改现有规模集的映像发布者和产品/服务。  

## <a name="supported-os-images"></a>支持的 OS 映像
当前仅支持特定 OS 平台映像。 如果规模集通过[共享映像库](../virtual-machines/shared-image-galleries.md)使用自定义映像，则[支持](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images)自定义映像。

目前支持以下平台 SKU（我们会定期添加更多 SKU）：

| 发布者               | OS 产品/服务      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| OpenLogic               | CentOS        | 7.5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-gensecond |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>配置自动 OS 映像升级时的要求

- 映像的版本属性必须设置为“最新” 。
- 对于非 Service Fabric 规模集，或者对于仅限无状态节点类型的铜级持久性层上的 Service Fabric 规模集，请使用应用程序运行状况探测或[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)。
- 请使用计算 API 版本 2018-10-01 或更高版本。
- 确保规模集模型中指定的外部资源可用且已更新。 示例包括，VM 扩展属性中用于引导有效负载的 SAS URI、存储帐户中的有效负载、对模型中的机密的引用，等等。
- 对于使用 Windows 虚拟机的规模集，从计算 API 版本 2019-03-01 开始，在规模集模型定义中，必须将“virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates”属性设置为“false” 。 enableAutomaticUpdates 属性启用 VM 内部修补，其中的“Windows 更新”将应用操作系统补丁但不替换 OS 磁盘。 在规模集上启用自动 OS 映像升级后，不需要通过 Windows 更新执行额外的修补过程。

### <a name="service-fabric-requirements"></a>Service Fabric 要求

如果使用的是 Service Fabric，请确保满足以下条件：
-   Service Fabric [持续性级别](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)为白银或黄金，而不是青铜（支持自动 OS 映像升级的纯无状态节点类型除外）。
-   根据规模集模型定义，Service Fabric 扩展必须具有 TypeHandlerVersion 1.1 或更高版本。
-   根据规模集模型定义，Service Fabric 群集与 Service Fabric 扩展的持久性级别应相同。
- 对于银级或金级持久性层，不需要额外的运行状况探测或使用应用程序运行状况扩展。 仅限无状态节点类型的铜级持久性层需要额外的运行状况探测。
- 在规模集模型定义中，virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates 属性必须设置为 false 。 enableAutomaticUpdates 属性启用通过“Windows 更新”进行的 VM 内部修补，在 Service Fabric 规模集上不受支持。

请确保 Service Fabric 群集与 Service Fabric 扩展上的持久性设置匹配，因为不匹配将导致升级错误。 可以通过[此页](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)上概述的指导原则来修改持久性级别。


## <a name="automatic-os-image-upgrade-for-custom-images"></a>自定义映像的 OS 映像自动升级

通过[共享映像库](../virtual-machines/shared-image-galleries.md)部署的自定义映像支持 OS 映像自动升级。 OS 映像自动升级不支持其他自定义映像。

### <a name="additional-requirements-for-custom-images"></a>自定义映像的其他要求
- 对于所有规模集，用于 OS 映像自动升级的安装和配置过程均相同，如此页的[配置部分](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade)中所述。
- 发布新版本映像并将其[复制](../virtual-machines/shared-image-galleries.md#replication)到该规模集的区域时，为 OS 映像自动升级配置的规模集实例将升级到最新版本的共享映像库映像。 如果新映像未复制到部署规模的区域，则规模集实例将不会升级到最新版本。 区域映像复制使你可控制规模集的新映像的推出。
- 新映像版本不应从该库映像的最新版本中排除。 从库映像的最新版本中排除的映像版本不会通过 OS 映像自动升级向规模集推出。

> [!NOTE]
>首次为 OS 自动升级配置规模集之后，规模集最多可能需要 3 小时才能触发首次映像升级推出。 这是每规模集发生的一次性延迟。 后续映像的推出将于 30-60 分钟内在规模集上触发。


## <a name="configure-automatic-os-image-upgrade"></a>配置自动 OS 映像升级
若要配置自动 OS 映像升级，请确保在规模集模型定义中将 *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* 属性设置为 *true*。

### <a name="rest-api"></a>REST API
以下示例说明如何在规模集模型上设置自动 OS 升级：

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2021-03-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 为规模集配置 OS 映像自动升级。 以下示例为 myResourceGroup 资源组中的 myScaleSet 规模集配置自动升级 ：

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss update](/cli/azure/vmss#az_vmss_update) 为规模集配置 OS 映像自动升级。 使用 Azure CLI 2.0.47 或更高版本。 以下示例为 myResourceGroup 资源组中的 myScaleSet 规模集配置自动升级 ：

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>为规模集配置 OS 映像自动升级后，如果规模集使用“手动”[升级策略](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)，则还必须将规模集 VM 引入最新的规模集模型。

## <a name="using-application-health-probes"></a>使用应用程序运行状况探测

OS 升级过程中，规模集中的 VM 实例每次只升级一批。 只有客户应用程序在升级后的 VM 实例上正常运行时，才会继续升级。 建议应用程序向规模集 OS 升级引擎提供运行状况信号。 默认情况下，在 OS 升级期间，平台根据 VM 电源状态和扩展预配状态确定升级后 VM 实例是否正常运行。 在 VM 实例的 OS 升级期间，VM 实例上的 OS 磁盘被替换为基于最新版本映像的新磁盘。 OS 升级完毕后，配置的扩展在这些 VM 上运行。 仅当已成功预配实例上的所有扩展时，才将应用程序视为正常。

可以选择为规模集配置应用程序运行状况探测，用于为平台提供关于应用程序当前状态的准确信息。 应用程序运行状况探测是用作运行状况信号的自定义负载均衡器探测。 规模集 VM 实例上运行的应用程序可以响应外部的 HTTP 或 TCP 请求，指示其是否正常运行。 若要详细了解自定义负载均衡器探测的工作原理，请参阅[了解负载均衡器探测](../load-balancer/load-balancer-custom-probe-overview.md)。 Service Fabric 规模集不支持应用程序运行状况探测。 非 Service Fabric 规模集需要负载均衡器应用程序运行状况探测或[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)。

如果规模集配置为使用多个放置组，则探测需使用[负载均衡器标准版](../load-balancer/load-balancer-overview.md)。

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>在规模集上将自定义负载均衡器探测配置为应用程序运行状况探测
最佳做法是为规模集运行状况显式创建负载均衡器探测。 运行状况探测可使用与现有 HTTP 探测或 TCP 探测相同的终结点，但所需的行为可能与传统负载均衡器探测不同。 例如，如果实例的负载过高，传统负载均衡器探测可能返回“不正常”，但是，这不符合自动 OS 升级过程中实际的实例运行状况。 请将探测配置为不超过两分钟的高探测速率。

可以在规模集的 networkProfile 中引用负载均衡器探测，并可将探测与内部或公共的负载均衡器相关联，如下所示：

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> 使用带 Service Fabric 的自动 OS 升级时，更新域将推出新的 OS 映像，以维持 Service Fabric 中运行的服务的高可用性。 若要利用 Service Fabric 中的自动 OS 升级，必须将群集节点类型配置为使用白银持续性层或更高层级。 对于青铜持续性层，只有无状态节点类型支持自动 OS 升级。 有关 Service Fabric 群集的持续性特征的详细信息，请参阅[此文档](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)。

### <a name="keep-credentials-up-to-date"></a>请使凭据保持最新
如果规模集使用任何凭据访问外部资源（例如进行配置以使用 SAS 令牌访问存储帐户的 VM 扩展），请确保凭据保持最新。 如果任何凭据（包括证书和令牌）过期，则升级将失败，并且第一批 VM 将处于失败状态。

如果发生资源身份验证故障，则建议执行以下步骤来恢复 VM 和重新启用 OS 自动升级：

* 重新生成传递到扩展的令牌（或任何其他凭据）。
* 确保用于从 VM 内部访问外部实体的所有凭据均保持最新。
* 使用任何新令牌更新规模集模型中的扩展。
* 部署更新后的规模集，这将更新包括失败实例在内的所有 VM 实例。

## <a name="using-application-health-extension"></a>使用应用程序运行状况扩展
应用程序运行状况扩展部署在虚拟机规模集实例中，并从规模集实例中报告 VM 的运行状况。 可以配置扩展，以探测应用程序终结点并更新该实例上应用程序的状态。 Azure 会检查此实例状态，以确定实例是否符合升级操作的条件。

因为扩展从 VM 中报告运行状况，所以扩展可在无法利用外部探测（例如使用自定义 Azure 负载均衡器[探测](../load-balancer/load-balancer-custom-probe-overview.md)的应用程序运行状况探测）的情况下使用。

可以使用多种方法将应用程序运行状况扩展部署到规模集，如[此文](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)中的示例所详述。

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>获取自动 OS 映像升级的历史记录
可以使用 Azure PowerShell、Azure CLI 2.0 或 REST API 检查在规模集上执行的最新 OS 升级的历史记录。 可以获取过去两个月内最近五次 OS 升级尝试的历史记录。

### <a name="rest-api"></a>REST API
以下示例使用 [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) 检查 myResourceGroup 资源组中 myScaleSet 规模集的状态 ：

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2021-03-01`
```

GET 调用将返回类似以下示例输出的属性：

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet 检查规模集的 OS 升级历史记录。 以下示例详细说明如何检查 myResourceGroup 资源组中 myScaleSet 规模集的 OS 升级状态 ：

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss get-os-upgrade-history](/cli/azure/vmss#az_vmss_get_os_upgrade_history) 检查规模集的 OS 升级历史记录。 使用 Azure CLI 2.0.47 或更高版本。 以下示例详细说明如何检查 myResourceGroup 资源组中 myScaleSet 规模集的 OS 升级状态 ：

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>如何获取最新版本的平台 OS 映像？

可以根据以下示例获取 OS 自动升级支持的 SKU 的可用映像版本：

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2021-03-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>手动触发 OS 映像升级
在规模集上启用 OS 映像自动升级后，无需在规模集上手动触发映像更新。 无需任何手动干预，OS 升级业务流程协调程序会自动将最新的可用映像版本应用于规模集实例。

对于不希望等待业务流程协调程序应用最新映像的特定情况，可以使用以下示例手动触发 OS 映像升级。

> [!NOTE]
> OS 映像升级的手动触发器不提供自动回退功能。 如果实例在升级操作后未恢复其运行状况，则无法还原其先前的 OS 磁盘。

### <a name="rest-api"></a>REST API
使用[启动 OS 升级](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API 调用来启动滚动升级，将所有虚拟机规模集实例移动到最新可用的映像 OS 版本。 已在运行最新可用 OS 版本的实例不会受到影响。 以下示例详细说明如何在 myResourceGroup 资源组中的 myScaleSet 规模集上启动 OS 滚动升级 ：

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2021-03-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) cmdlet 检查规模集的 OS 升级历史记录。 以下示例详细说明如何在 myResourceGroup 资源组中的 myScaleSet 规模集上启动 OS 滚动升级 ：

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss rolling-upgrade start](/cli/azure/vmss/rolling-upgrade#az_vmss_rolling_upgrade_start) 检查规模集的 OS 升级历史记录。 使用 Azure CLI 2.0.47 或更高版本。 以下示例详细说明如何在 myResourceGroup 资源组中的 myScaleSet 规模集上启动 OS 滚动升级 ：

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解应用程序运行状况扩展](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
