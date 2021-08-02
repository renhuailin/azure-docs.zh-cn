---
title: 使用 Azure CLI 为 Azure 虚拟机规模集上的 OS 映像升级进行维护控制
description: 了解如何使用维护控制和 Azure CLI 来控制何时向 Azure 虚拟机规模集推出自动 OS 映像升级。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e00341b38cca73ccd351b4e16ce71317a8d0c751
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072983"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-cli"></a>使用 Azure CLI 为 Azure 虚拟机规模集上的 OS 映像升级进行维护控制

利用维护控制，可以决定何时将自动来宾 OS 映像升级应用到虚拟机规模集。 本主题介绍维护控制的 Azure CLI 选项。 若要了解如何使用维护控制，请参阅 [Azure 虚拟机规模集的维护控制](virtual-machine-scale-sets-maintenance-control.md)。


## <a name="create-a-maintenance-configuration"></a>创建维护配置

使用 `az maintenance configuration create` 创建维护配置。 此示例创建名为 myConfig 的维护配置，该配置的范围限定为 OS 映像。 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage\
   --location eastus
```

复制输出中的配置 ID 供以后使用。

使用 `--maintenance-scope osimage` 会确保将维护配置用于控制对来宾 OS 的更新。

如果尝试创建同名的但位于不同位置的配置，则会收到错误。 配置名称对于资源组必须是唯一的。

可以使用 `az maintenance configuration list` 来查询可用的维护配置。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-a-scheduled-window"></a>创建具有计划时段的维护配置
你也可以声明一个 Azure 将用于在资源上应用更新的计划时段。 本示例创建名为 myConfig 的维护配置，该配置的计划时段为每月第四个星期一的 5 个小时。 在创建计划时段后，不再需要手动应用更新。

> [!IMPORTANT]
> 维护持续时间必须为 5 小时或更长时间。 必须将维护”重复周期”设置为“天”。

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Day" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

## <a name="assign-the-configuration"></a>分配此配置

使用 `az maintenance assignment create` 将配置分配给虚拟机规模集。


## <a name="enable-automatic-os-upgrade"></a>启动自动 OS 升级

可以为每个要使用维护控制的虚拟机规模集启用自动 OS 升级。 有关在虚拟机规模集上启用自动 OS 升级的详细信息，请参阅 [Azure 虚拟机规模集自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure 中运行的虚拟机的维护和更新](maintenance-and-updates.md)
