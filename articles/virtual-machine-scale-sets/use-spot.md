---
title: 使用 Azure 点虚拟机创建规模集
description: 了解如何创建使用 Azure 点虚拟机节省成本的 Azure 虚拟机规模集。
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 33aa553e688b595551c20e8b1432163152865537
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675020"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>虚拟机规模集的 Azure 点虚拟机 

使用规模集上的 Azure 点虚拟机，你可以利用我们的未使用容量，以显著节省成本。 在 Azure 需要恢复容量时，Azure 基础结构将逐出 Azure 点虚拟机实例。 因此，Azure 点虚拟机实例非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

可用容量可能因大小、区域、一天内的时间等因素而异。 在规模集上部署 Azure 点虚拟机实例时，只有当容量可用时，Azure 才会分配实例，但对于这些实例没有 SLA。 Azure 点虚拟机规模集部署在单个容错域中，不提供高可用性保证。


## <a name="pricing"></a>定价

基于区域和 SKU，Azure 点虚拟机实例的定价是可变的。 有关详细信息，请参阅针对 [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 的定价。 


使用可变定价，可以选择设置最大价格（美元） (USD) ，最多可使用五个小数位数。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最高价格设置为 `-1`，则不会根据价格逐出实例。 此实例的价格将是 Azure 点虚拟机的当前价格，或者是标准实例的价格，只要容量和配额可用即可。


## <a name="limitations"></a>限制

Azure 点虚拟机不支持以下大小：
 - B 系列
 - 任意大小 (促销版本，如 Dv2、NV、NC、H 促销大小) 

Azure 点虚拟机可部署到除 Microsoft Azure 中国世纪地区以外的任何区域。

<a name="channel"></a>

目前支持以下 [产品/服务类型](https://azure.microsoft.com/support/legal/offer-details/) ：

-   企业协议
-   即用即付产品/服务代码003P
-   赞助
- 对于云服务提供商)  (CSP，请参阅 [合作伙伴中心](https://docs.microsoft.com/partner-center/azure-plan-get-started) 或直接联系你的合作伙伴。

## <a name="eviction-policy"></a>逐出策略

使用 Azure 点虚拟机创建规模集时，可以将逐出策略设置为 *释放* (默认) 或 *删除*。 

“解除分配”策略可将逐出的实例移到已停止-已解除分配状态，以允许重新部署逐出的实例。 但是，不保证分配将成功。 已解除分配的 VM 将计入规模集实例配额，基础磁盘仍会产生费用。 

如果希望在逐出实例时删除实例，可以将逐出策略设置为 " *删除*"。 将逐出策略设置为删除后，可以通过增大规模集实例计数属性来创建新的 VM。 逐出的 VM 会连同其基础磁盘一起删除，因此可以避免存储费用。 还可以使用规模集的自动缩放功能来自动尝试补偿逐出的 VM，但是，无法保证分配成功。 建议你在将逐出策略设置为 "删除" 时，仅在 Azure 点虚拟机规模集上使用自动缩放功能，以避免磁盘成本和达到配额限制。 

用户可以选择通过 [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)接收 VM 内通知。 这样，系统就会在你的 VM 被逐出时向你发送通知。在逐出之前，你将有 30 秒的时间来完成任何作业并执行关闭任务。 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>尝试 (预览版 & 还原) 

这个新的平台级别功能将使用 AI 自动尝试还原规模集内的已逐出 Azure 点虚拟机实例，以维护目标实例计数。 

> [!IMPORTANT]
> 请尝试 & 还原目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

尝试 & 还原权益：
- 默认情况下，在规模集中部署 Azure 点虚拟机时启用。
- 尝试还原由于容量原因而逐出的 Azure 点虚拟机。
- 所还原的 Azure 点虚拟机预计运行时间较长，且容量触发逐出的可能性较低。
- 提高 Azure 点虚拟机的使用期限，使工作负荷运行更长时间。
- 帮助虚拟机规模集维护 Azure 点虚拟机的目标计数，类似于 "保留即用即付" Vm 的 "维护目标计数" 功能。

尝试在使用 [自动缩放](virtual-machine-scale-sets-autoscale-overview.md)的规模集中禁用 & 还原。 规模集中的 Vm 数由自动缩放规则驱动。

## <a name="placement-groups"></a>放置组

放置组是类似于 Azure 可用性集的构造，具有其自己的容错域和升级域。 默认情况下，一个规模集包含一个放置组，最大大小为 100 台 VM。 如果将被调用的规模集属性 `singlePlacementGroup` 设置为 *false*，则规模集可以由多个放置组组成，其范围为 0-1000 个 vm。 

> [!IMPORTANT]
> 除非使用的是与 HPC 一起使用的，否则强烈建议将 "规模集" 属性设置 `singlePlacementGroup` 为 *false* ，以启用多个放置组，以便更好地在区域或区域中进行缩放。 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>在规模集中部署 Azure 点虚拟机

若要在规模集上部署 Azure 点虚拟机，你可以将新的 *优先级* 标志设置为 " *发现*"。 规模集中的所有 VM 都将设置为 Spot。 若要使用 Azure 点虚拟机创建规模集，请使用以下方法之一：
- [Azure 门户](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 资源管理器模板](#resource-manager-templates)

## <a name="portal"></a>门户

创建使用 Azure 点虚拟机的规模集的过程与入门 [文章](quick-create-portal.md)中详述的过程相同。 部署规模集时，可以选择设置点标志和逐出策略： ![ 使用 Azure 点虚拟机创建规模集](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

使用 Azure 点虚拟机创建规模集的过程与入门 [文章](quick-create-cli.md)中详述的过程相同。 只需添加“--Priority Spot”，并添加 `--max-price`。 在此示例中，对于 `--max-price`，我们使用了 `-1`，因此系统不会基于价格来逐出实例。

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

使用 Azure 点虚拟机创建规模集的过程与入门 [文章](quick-create-powershell.md)中详述的过程相同。
只需向 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) 中添加“-Priority Spot”并提供 `-max-price`。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>资源管理器模板

创建使用 Azure 点虚拟机的规模集的过程与适用于 [Linux](quick-create-template-linux.md) 或 [Windows](quick-create-template-windows.md)的入门文章中详述的过程相同。 

对于 Azure 点虚拟机模板部署，使用 `"apiVersion": "2019-03-01"` 或更高版本。 

将 `priority` 、 `evictionPolicy` 和属性添加到模板中的节，将属性添加到节 `billingProfile` `"virtualMachineProfile":` `"singlePlacementGroup": false,` `"Microsoft.Compute/virtualMachineScaleSets"` 中：

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

可以 [模拟逐出](https://docs.microsoft.com/rest/api/compute/virtualmachines/simulateeviction) Azure 点虚拟机，以测试应用程序对突然逐出的响应情况。 

将以下内容替换为你的信息： 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` 表示模拟逐出成功。 

## <a name="faq"></a>常见问题解答

**问：** 创建后，Azure 点虚拟机实例是否与标准实例相同？

**答：** 是，但 Azure 点虚拟机没有 SLA，可以随时将其逐出。


**问：** 当被逐出但仍然需要容量时，该怎么办？

**答：** 如果需要立即使用容量，建议使用标准 Vm，而不是 Azure 点虚拟机。


**问：** 如何管理 Azure 点虚拟机的配额？

**答：** Azure 点虚拟机实例和标准实例将具有不同的配额池。 Azure 点虚拟机配额将在 Vm 和规模集实例之间共享。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。


**问：** 能否为 Azure 点虚拟机请求额外的配额？

**答：** 是的，你将能够通过 [标准配额请求进程](../azure-portal/supportability/per-vm-quota-requests.md)提交请求以增加 Azure 点虚拟机的配额。


**问：** 能否将现有规模集转换为 Azure 点虚拟机规模集？

**答:** 不可以，只有在创建时才支持设置 `Spot` 标志。


**问：** 如果我过去使用的是表示低优先级规模集的 `low`，是否需要开始改用 `Spot`？

**答:** 目前，`low` 和 `Spot` 都有效，但你应该开始改用 `Spot`。


**问：** 能否使用常规 Vm 和 Azure 点虚拟机创建规模集？

**答:** 不可以，一个规模集只能支持一种优先级类型。


**问：**  能否对 Azure 点虚拟机规模集使用自动缩放？

**答：** 是的，可以在 Azure 点虚拟机规模集上设置自动缩放规则。 如果 Vm 被逐出，自动缩放可尝试创建新的 Azure 点虚拟机。 但记住，不保证会创建成功。 


**问：** 自动缩放是否可用于这两种收回策略（解除分配和删除）？

**答：** 是的，但建议在使用自动缩放时将逐出策略设置为删除。 这是因为解除分配的实例将计入规模集的容量计数。 如果使用自动缩放，由于实例已解除分配且已收回，实例很可能会快速达到目标数。 此外，你的缩放操作可能会受到点逐出的影响。 例如，在缩放操作期间，由于存在多个点逐出，虚拟机规模集实例可能低于设置的最小计数。 


**问：** 我可以在何处发布问题？

**答:** 你可以在 [问答](/answers/topics/azure-spot.html)中发布问题并使用 `azure-spot` 来标记问题。 

## <a name="next-steps"></a>后续步骤

有关价格详细信息，请查看[虚拟机规模集定价页](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。
