---
title: 配置 Service Fabric 托管群集
description: 了解如何为自动 OS 升级、NSG 规则等配置 Service Fabric 托管群集。
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 5be6422af6a1611bb8978ff4f0b76122cb146eb7
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864351"
---
# <a name="service-fabric-managed-cluster-configuration-options"></a>Service Fabric 托管群集配置选项

除了在创建群集时选择 [Service Fabric 托管群集 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) 以外，还可以使用多种其他方法对其进行配置，包括：

* 将 [虚拟机规模集扩展](how-to-managed-cluster-vmss-extension.md) 添加到节点类型
* 配置群集 [可用性区域范围](how-to-managed-cluster-availability-zones.md)
* 配置群集[网络设置](how-to-managed-cluster-networking.md)
* 为[大型虚拟机规模集](how-to-managed-cluster-large-virtual-machine-scale-sets.md)配置节点类型
* 在群集节点类型上配置 [托管标识](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md)
* 为群集节点启用 [自动 OS 升级](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* 在群集节点上启用 [OS 和数据磁盘加密](how-to-enable-managed-cluster-disk-encryption.md)
* 选择群集 [托管磁盘类型](how-to-managed-cluster-managed-disk.md) SKU
* 为运行时更新配置群集 [升级选项](how-to-managed-cluster-upgrades.md)

## <a name="enable-automatic-os-image-upgrades"></a>启用自动 OS 映像升级

你可以选择对运行托管群集节点的虚拟机启用自动 OS 映像升级。 尽管虚拟机规模集资源是由 Service Fabric 托管群集代表你进行管理的，但你可以选择为群集节点启用自动 OS 映像升级。 与[经典 Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#virtual-machine-os-automatic-upgrade-configuration) 群集一样，默认情况下不会升级托管群集节点，以防止群集意外中断。

启动自动 OS 升级：

* 使用 `2021-05-01`（或更高）版本的 Microsoft.ServiceFabric/managedclusters 和 Microsoft.ServiceFabric/managedclusters/nodetypes 资源 
* 将群集的属性 `enableAutoOSUpgrade` 设置为“true”
* 将群集 nodeType 的资源属性 `vmImageVersion` 设置为“latest”

例如：

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-05-01",
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

启用后，Service Fabric 将开始查询和跟踪托管群集中的 OS 映像版本。 如果有新的 OS 版本可用，将升级群集节点类型（虚拟机规模集），一次升级一个。 仅在确认没有群集节点 OS 映像升级正在进行后，才执行 Service Fabric 运行时升级。

如果升级失败，Service Fabric 将在 24 小时后重试，最多重试三次。 类似于经典（非托管）Service Fabric 升级，运行不正常的应用或节点可能会阻止 OS 映像升级。

有关映像升级的详细信息，请参阅 [Azure 虚拟机规模集的自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)。

## <a name="next-steps"></a>后续步骤

[Service Fabric 托管群集概述](overview-managed-cluster.md)

[Service Fabric 群集模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)
