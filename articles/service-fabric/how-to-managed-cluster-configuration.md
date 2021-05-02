---
title: 配置 Service Fabric 托管群集（预览版）
description: 了解如何针对自动 OS 升级、NSG 规则等配置 Service Fabric 托管群集。
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732626"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Service Fabric 托管群集（预览版）配置选项

除了在创建群集时选择 [Service Fabric 托管群集 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) 以外，还可以使用多种其他方法对其进行配置。 在当前预览版中，可以：

* 为群集配置[网络选项](how-to-managed-cluster-networking.md)
* 将[虚拟机规模集扩展](how-to-managed-cluster-vmss-extension.md)添加到节点类型
* 在节点类型上配置[托管标识](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md)
* 为节点启用[自动 OS 升级](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* 在节点上启用 [OS 磁盘和数据磁盘加密](how-to-enable-managed-cluster-disk-encryption.md)

## <a name="enable-automatic-os-image-upgrades"></a>启用自动 OS 映像升级

可以选择对运行托管群集节点的虚拟机启用自动 OS 映像升级。 尽管是通过 Service Fabric 托管群集代表你管理虚拟机规模集资源，但是你可以选择为群集节点启用自动 OS 映像升级。 与[经典 Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) 群集一样，默认情况下不会升级托管群集节点，以防止群集意外中断。

若要启用自动 OS 升级，请执行以下操作：

* 使用 `2021-01-01-preview`（或更高）版本的“Microsoft.ServiceFabric/managedclusters”和“Microsoft.ServiceFabric/managedclusters/nodetypes”资源
* 将群集的属性 `enableAutoOSUpgrade` 设置为“true”
* 将群集 nodeTypes 的资源属性 `vmImageVersion` 设置为“latest”

例如：

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

启用后，Service Fabric 将开始查询和跟踪托管群集中的 OS 映像版本。 如果有新的 OS 版本可用，则升级群集节点类型（虚拟机规模集），一次一个。 仅在确认没有群集节点 OS 映像升级正在进行的情况下，才执行 Service Fabric 运行时升级。

如果升级失败，Service Fabric 将在 24 小时后重试，最多重试三次。 与经典（非托管）Service Fabric 升级类似，运行不正常的应用或节点可能会阻止 OS 映像升级。

有关映像升级的详细信息，请参阅 [Azure 虚拟机规模集的自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)。

## <a name="next-steps"></a>后续步骤

[Service Fabric 托管群集概述](overview-managed-cluster.md)

[Service Fabric 群集模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)
