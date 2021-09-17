---
title: Linux 虚拟机规模集的 Azure 混合权益
description: 了解 Azure 混合权益如何应用于虚拟机规模集，从而帮助节省 Azure 上运行的 Linux 虚拟机的成本。
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: d8671cab8e81b47316d14c9cabb4b803111643be
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768479"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set"></a>Linux 虚拟机规模集的 Azure 混合权益

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 统一规模集

Linux 虚拟机规模集的 Azure 混合权益现已正式发布。 Azure 混合权益可帮助降低运行 RHEL 和 SLES [虚拟机规模集](./overview.md)的成本。

使用此权益，只需支付规模集的基础结构成本。 该权益适用于所有 RHEL 和 SLES 市场即用即付 (PAYG) 映像。


>[!NOTE]
> 本文介绍适用于 Linux VMSS 的 Azure 混合权益。 这里提供了一篇单独的文章：[Linux VM 的 Azure 混合权益](../virtual-machines/linux/azure-hybrid-benefit-linux.md)，自 2020 年 11 月起，已面向 Azure 客户提供该权益。

## <a name="benefit-description"></a>权益说明
Azure 混合让你能够使用 Red Hat 或 SUSE 的现有云访问许可证，并灵活地将虚拟机规模集实例转换为自带订阅 (BYOS) 计费。 

从 PAYG 市场映像部署的虚拟机规模集将同时收取基础结构和软件费用。 使用 AHB，可以在无需重新部署的情况下将 PAYG 实例转换为 BYOS 计费模式。

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Linux VM 上的 Azure 混合权益成本可视化效果。":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>适用于 Linux 的 Azure 混合权益范围
Azure 混合权益适用于 Azure 市场中的所有 RHEL 和 SLES PAYG 映像。 该权益目前不适用于 RHEL 或 SLES BYOS 映像或 Azure 市场中的自定义映像。

如果已在 Linux VM 上使用 Azure 专用主机实例和 SQL 混合权益，则这些权益不符合 Azure 混合权益的资格。

## <a name="get-started"></a>入门

### <a name="red-hat-customers"></a>Red Hat 客户

RHEL 的 Azure 混合权益适用于同时满足以下两个条件的 Red Hat 客户：

- 具有可在 Azure 中使用的有效或未使用的 RHEL 订阅
- 已启用一个或多个这些订阅，以在 Azure 中用于 [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 计划

> [!IMPORTANT]
> 确保已对[云访问](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)计划启用正确的订阅。

若要开始使用 Red Hat 权益：

1. 使用 [Red Hat Cloud Access 客户界面](https://access.redhat.com/management/cloud)在 Azure 中启用符合资格的 RHEL 订阅。

   然后，在启用 Red Hat Cloud Access 的过程中提供的 Azure 订阅将能够使用 Azure 混合权益功能。
1. 将 Azure 混合权益应用于任何现有和新的 RHEL PAYG 虚拟机规模集。 可以使用 Azure 门户或 Azure CLI 来启用该权益。
1. 若要配置 RHEL VM 的更新源和获取 RHEL 订阅符合性指南，请遵循建议的[后续步骤](https://access.redhat.com/articles/5419341)。


### <a name="suse-customers"></a>SUSE 客户

若要开始使用 SUSE 权益：

1. 向 SUSE 公有云计划进行注册。
1. 通过 Azure 门户或 Azure CLI 将权益应用于新创建的或现有的虚拟机规模集。
1. 使用单独的更新源注册接收权益的 VM。


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>在 Azure 门户中启用和禁用权益 
### <a name="azure-portal-example-to-enable-the-benefit-during-creation"></a>在创建期间启用权益的 Azure 门户示例：
1. 访问 [Microsoft Azure 门户](https://portal.azure.com/)
1. 在门户中转到“创建虚拟机规模集”页面。
 ![创建 VMSS 时的 AHB](./media/azure-hybrid-benefit-linux/create-vmss-ahb.png)
1. 单击复选框来启用 AHB 转换，并使用云访问许可证。
 ![创建 VMSS 时的 AHB 复选框](./media/azure-hybrid-benefit-linux/create-vmss-ahb-checkbox.png)
1. 按照下一组说明创建虚拟机规模集
1. 检查“配置”边栏选项卡，会看到选项已启用。 
![创建后的 AHB OS 边栏选项卡](./media/azure-hybrid-benefit-linux/create-vmss-ahb-os-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-virtual-machine-scale-set"></a>为现有虚拟机规模集启用权益的 Azure 门户示例：
1. 访问 [Microsoft Azure 门户](https://portal.azure.com/)
1. 打开要在其上应用转换的“虚拟机规模集”页面。
1. 转到左侧的“操作系统”选项。 你会看到“许可”部分。 若要启用 AHB 转换，请选中“是”单选按钮，并选中“确认”复选框。
![创建后的 AHB 配置边栏选项卡](./media/azure-hybrid-benefit-linux/create-vmss-ahb-os-blade.png)



## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>使用 Azure CLI 启用和禁用权益

可以使用 `az vmss update` 命令来更新现有 VM。 对于 RHEL VM，使用 `--license-type` 参数 `RHEL_BYOS` 运行该命令。 对于 SLES VM，使用 `--license-type` 参数 `SLES_BYOS` 运行该命令。

### <a name="cli-example-to-enable-the-benefit"></a>启用权益的 CLI 示例
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>禁用权益的 CLI 示例
若要禁用权益，请使用 `--license-type` 值 `None`：

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> 规模集有一项[“升级策略”](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)，该策略决定了如何使用最新的规模集模型对 VM 进行更新。 因此，如果 VMSS 具有“自动”升级策略，则在更新 VM 实例时将自动应用 Azure 混合权益。 如果 VMSS 具有“滚动”升级策略，将基于计划的更新应用 AHB。
如果具有“手动”升级策略，需要对每个现有 VM 执行“手动升级”。  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>在“手动升级”策略情况下升级虚拟机规模集实例的 CLI 示例 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>在创建虚拟机规模集时应用 Azure 混合权益 
除了将 Azure 混合权益应用于现有的即用即付虚拟机规模集之外，还可以在创建虚拟机规模集时调用该权益。 这样做有三重好处：
- 可以使用相同的映像和过程来预配 PAYG 和 BYOS 虚拟机规模集实例。
- 它支持在将来更改许可模式，而这是仅限 BYOS 的映像无法实现的。
- 默认情况下，虚拟机规模集实例将连接到 Red Hat Update Infrastructure (RHUI)，以确保其保持最新且处于安全状态。 可以在部署后随时更改更新后的机制。

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>使用 Azure 混合权益创建虚拟机规模集的 CLI 示例
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>后续步骤
* [了解如何使用 Azure CLI 创建和更新 VM 以及添加许可证类型（RHEL_BYOS、SLES_BYOS）以获得 Azure 混合权益](/cli/azure/vmss)