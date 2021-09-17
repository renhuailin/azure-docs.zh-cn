---
title: 创建使用 Azure 现成虚拟机的规模集
description: 了解如何创建使用 Azure 现成虚拟机节省成本的 Azure 虚拟机规模集。
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b0c0ffdce85450900c0d4ca0da936b8675820f79
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690562"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 Azure 现成虚拟机 

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

对规模集使用 Azure 现成虚拟机，可以利用未使用的容量，同时大幅降低成本。 每当 Azure 需要回收容量时，Azure 基础结构就会逐出 Azure 现成虚拟机实例。 因此，Azure 现成虚拟机实例非常适合用于处理服务中断（例如批处理作业）的工作负载、开发/测试环境、大型计算工作负载等。

可用容量可能因大小、区域、一天内的时间等因素而异。 在规模集中部署 Azure 现成虚拟机实例时，如果有可用的容量，则 Azure 会仅分配该实例，但这些实例没有 SLA。 Azure 现成虚拟机规模集部署在单个容错域中，不提供高可用性保证。


## <a name="pricing"></a>定价

根据地区和 SKU 不同，Azure 现成虚拟机实例的定价也不尽相同。 有关详细信息，请参阅针对 [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 的定价。 


使用可变定价，你可以设置最高价格，以美元 (USD) 为单位，最多可使用五个小数位。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最高价格设置为 `-1`，则不会根据价格逐出实例。 实例价格将是 Azure 现成虚拟机的当前价格或标准实例的价格（以较低者为准，但一定要有容量和配额可用）。


## <a name="limitations"></a>限制

Azure 现成虚拟机不支持以下大小：
 - B 系列
 - 任意大小的促销版本（例如 Dv2、NV、NC、H 促销大小）

Azure 现成虚拟机可以部署到除 Microsoft Azure 中国世纪互联以外的任何区域。

<a name="channel"></a>

当前支持以下[套餐类型](https://azure.microsoft.com/support/legal/offer-details/)：

-   企业协议
-   即用即付套餐代码 (003P)
-   赞助（0036P 和 0136P）
- 对于云服务提供商 (CSP)，请参阅[合作伙伴中心](/partner-center/azure-plan-get-started)或直接联系合作伙伴。

## <a name="eviction-policy"></a>逐出策略

使用 Azure 现成虚拟机创建规模集时，可将逐出策略设置为“解除分配”（默认值）或“删除” 。 

“解除分配”策略可将逐出的实例移到已停止-已解除分配状态，以允许重新部署逐出的实例。 但是，不保证分配将成功。 已解除分配的 VM 将计入规模集实例配额，基础磁盘仍会产生费用。 

如果希望在逐出实例后将其删除，可以将逐出策略设置为删除。 将逐出策略设置为删除后，可以通过增大规模集实例计数属性来创建新的 VM。 逐出的 VM 会连同其基础磁盘一起删除，因此可以避免存储费用。 还可以使用规模集的自动缩放功能来自动尝试补偿逐出的 VM，但是，无法保证分配成功。 我们建议仅当逐出策略设置为删除时才对 Azure 现成虚拟机规模集使用自动缩放功能，以免产生磁盘费用和达到配额限制。 

用户可以选择通过 [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md) 来接收 VM 内通知。 这样，系统就会在你的 VM 被逐出时向你发送通知。在逐出之前，你将有 30 秒的时间来完成任何作业并执行关闭任务。 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>尝试还原（预览版）

这一新的平台级别功能会使用 AI 自动尝试在规模集内还原逐出的 Azure 现成虚拟机实例，以维持目标实例计数。 

> [!IMPORTANT]
> 尝试还原功能目前处于公开预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

尝试还原功能优势：
- 尝试还原由于容量原因而逐出的 Azure 现成虚拟机。
- 还原的 Azure 现成虚拟机预计运行持续时间较长，且容量触发逐出的可能性较低。
- 提高 Azure 现成虚拟机生命期，使工作负载运行更长持续时间。
- 帮助虚拟机规模集维持 Azure 现成虚拟机的目标计数，类似于维持即用即付 VM 已有的 目标计数功能。

尝试还原功能在使用[自动缩放](virtual-machine-scale-sets-autoscale-overview.md)的规模集中处于禁用状态。 规模集中的 VM 数由自动缩放规则驱动。

### <a name="register-for-try--restore"></a>注册尝试还原功能

必须先注册预览版订阅，然后才能使用尝试还原功能。 注册可能需要几分钟才能完成。 可以使用 Azure CLI 或 PowerShell 完成功能注册。


**使用 CLI**

使用 [az feature register](/cli/azure/feature#az_feature_register) 为订阅启用预览版。 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令： 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**使用 PowerShell** 

使用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为订阅启用预览版。 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令： 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>放置组

放置组是类似于 Azure 可用性集的构造，具有自己的容错域和升级域。 默认情况下，一个规模集包含一个放置组，最大大小为 100 台 VM。 如果将名为 `singlePlacementGroup` 的规模集属性设置为 false，则该规模集可以由多个放置组组成，其范围为 0-1,000 台 VM。 

> [!IMPORTANT]
> 除非将 Infiniband 与 HPC 一起使用，否则强烈建议将规模集属性 `singlePlacementGroup` 设置为 false 以启用多个放置组，以便更好地在 Azure 区域或 DNS 区域中进行缩放。 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>在规模集中部署 Azure 现成虚拟机

若要在规模集中部署 Azure 现成虚拟机，可将新的 Priority 标志设置为 Spot 。 规模集中的所有 VM 都将设置为 Spot。 若要创建包含 Azure 现成虚拟机的规模集，请使用以下方法之一：
- [Azure 门户](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 资源管理器模板](#resource-manager-templates)

## <a name="portal"></a>门户

创建使用 Azure 现成虚拟机的规模集的过程与[入门文章](quick-create-portal.md)中详述的过程一样。 部署规模集时，可以选择设置 Spot 标志和逐出策略：![创建包含 Azure 现成虚拟机的规模集](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

创建包含 Azure 现成虚拟机的规模集的过程与[入门文章](quick-create-cli.md)中详述的过程一样。 只需添加“--Priority Spot”，并添加 `--max-price`。 在此示例中，对于 `--max-price`，我们使用了 `-1`，因此系统不会基于价格来逐出实例。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

创建包含 Azure 现成虚拟机的规模集的过程与[入门文章](quick-create-powershell.md)中详述的过程一样。
只需向 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) 中添加“-Priority Spot”并提供 `-max-price`。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>资源管理器模板

创建使用 Azure 现成虚拟机的规模集的过程与适用于 [Linux](quick-create-template-linux.md) 或 [Windows](quick-create-template-windows.md) 的入门文章中详述的过程一样。 

对于 Azure 现成虚拟机模板部署，请使用 `"apiVersion": "2019-03-01"` 或更高版本。 

在模板将 `priority`、`evictionPolicy` 和 `billingProfile` 属性添加到 `"virtualMachineProfile":` 节，并将 `"singlePlacementGroup": false,` 属性添加到 `"Microsoft.Compute/virtualMachineScaleSets"` 节：

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

若要在逐出实例后将其删除，请将 `evictionPolicy` 参数更改为 `Delete`。


## <a name="simulate-an-eviction"></a>模拟逐出

可以对 Azure 现成虚拟机[模拟逐出](/rest/api/compute/virtualmachines/simulateeviction)，以测试应用程序对突然逐出的响应情况。 

将以下内容替换为你的信息： 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` 意味着模拟逐出成功。 

有关详细信息，请参阅[测试模拟逐出通知](../virtual-machines/windows/spot-powershell.md#simulate-an-eviction)。

## <a name="faq"></a>常见问题解答

问：创建后，Azure 现成虚拟机实例是否与标准实例相同？

答：是，但 Azure 现成虚拟机没有 SLA，可以随时将其逐出。


**问：** 当被逐出但仍然需要容量时，该怎么办？

答：如果马上需要容量，建议使用标准 VM，而不要使用 Azure 现成虚拟机。


问：如何为 Azure 现成虚拟机管理配额？

答：Azure 现成虚拟机实例和标准实例会有不同的配额池。 会在 VM 与规模集实例之间共享 Azure 现成虚拟机配额。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。


问：是否可以为 Azure 现成虚拟机申请额外的配额？

答：是，可以通过[标准配额申请流程](../azure-portal/supportability/per-vm-quota-requests.md)提交申请，请求提高 Azure 现成虚拟机的配额。


问：是否可以将现有规模集转换为 Azure 现成虚拟机规模集？

**答:** 不可以，只有在创建时才支持设置 `Spot` 标志。


**问：** 如果我过去使用的是表示低优先级规模集的 `low`，是否需要开始改用 `Spot`？

**答:** 目前，`low` 和 `Spot` 都有效，但你应该开始改用 `Spot`。


问：是否可以创建一个同时包含常规 VM 和 Azure 现成虚拟机的规模集？

**答:** 不可以，一个规模集只能支持一种优先级类型。


问：是否可以将自动缩放用于 Azure 现成虚拟机规模集？

答：是，可以对 Azure 现成虚拟机规模集设置自动缩放规则。 如果你的 VM 被逐出，则自动缩放可以尝试创建新的 Azure 现成虚拟机。 但记住，不保证会创建成功。 


**问：** 自动缩放是否可用于这两种收回策略（解除分配和删除）？

答：可以，但建议使用自动缩放时将收回策略设置为删除。 这是因为解除分配的实例将计入规模集的容量计数。 如果使用自动缩放，由于实例已解除分配且已收回，实例很可能会快速达到目标数。 此外，缩放操作可能会受到现成逐出的影响。 例如，在缩放操作期间，由于存在多个现成逐出，虚拟机规模集实例可能会低于设置的最小计数。 


**问：** 我可以在何处发布问题？

**答:** 你可以在 [问答](/answers/topics/azure-spot.html)中发布问题并使用 `azure-spot` 来标记问题。 

## <a name="next-steps"></a>后续步骤

有关价格详细信息，请查看[虚拟机规模集定价页](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。
