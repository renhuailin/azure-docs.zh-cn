---
title: 为 Service Fabric 托管群集上的大型虚拟机规模集配置辅助节点类型
description: 本文分步介绍如何将辅助节点类型配置为大型虚拟机规模集
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 05177b519caa504ac9d931b07b9f6126a3851c0b
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545089"
---
# <a name="service-fabric-managed-cluster-node-type-scaling"></a>Service Fabric 托管群集节点类型缩放

Service Fabric 托管群集中的每个节点类型都受虚拟机规模集的支持。 如果要允许托管群集节点类型创建[大型虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)，必须向节点类型定义添加属性 `multiplePlacementGroups`。 默认情况下，托管群集节点类型将此属性设置为 false，使容错域和升级域在放置组中保持一致，但此设置会限制节点类型不能缩放到 100 个 VM 以上。 要帮助确定应用程序是否可以有效利用大型规模集，请参阅[此要求列表](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md#checklist-for-using-large-scale-sets)。

由于 Azure Service Fabric 托管群集资源提供程序协调缩放并使用托管磁盘处理数据，因此我们能够支持有状态和无状态辅助节点类型的大型规模集。

> [!NOTE]
> 部署节点类型后，无法修改此属性。

## <a name="enable-large-virtual-machine-scale-sets-in-a-service-fabric-managed-cluster"></a>在 Service Fabric 托管群集中启用大型虚拟机规模集
若要将辅助节点类型配置为大型规模集，请将 multiplePlacementGroups 属性设置为 true。
> [!NOTE]
> 不能对主节点类型设置此属性。

* Service Fabric 托管群集资源 apiVersion 应为 2021-05-01 或更高版本。

```json
{
  "apiVersion": "[variables('sfApiVersion')]",
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
  "location": "[resourcegroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
  ],
  "properties": {
    "multiplePlacementGroups": true,
    "isPrimary": false,
    "vmImagePublisher": "[parameters('vmImagePublisher')]",
    "vmImageOffer": "[parameters('vmImageOffer')]",
    "vmImageSku": "[parameters('vmImageSku')]",
    "vmImageVersion": "[parameters('vmImageVersion')]",
    "vmSize": "[parameters('nodeTypeSize')]",
    "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
    "dataDiskSizeGB": "[parameters('nodeTypeDataDiskSizeGB')]"
  }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将应用部署到 Service Fabric 托管群集](./tutorial-managed-cluster-deploy-app.md)
