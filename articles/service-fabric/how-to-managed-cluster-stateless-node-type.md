---
title: 通过无状态节点类型部署 Service Fabric 托管群集
description: 了解如何在 Service Fabric 托管群集中创建和部署无状态节点类型
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: a18c09e92cd8d78e314969fe15a802e4b7e2cc67
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546361"
---
# <a name="deploy-a-service-fabric-managed-cluster-with-stateless-node-types"></a>通过无状态节点类型部署 Service Fabric 托管群集

关于 Service Fabric 节点类型，存在一种固有假设，即在某个时间点，有状态服务可能会被放置到节点上。 无状态节点类型放宽了对节点类型的该假设。 放宽该假设使节点无状态节点类型能够从消除针对维修和维护操作的一些限制以加快横向扩展操作中受益。

* 主节点类型不能配置为无状态
* 无状态节点类型需使用 2021-05-01 或更高版本的 API
* 这样会自动将“multipleplacementgroup”属性设置为“true”。可[在此处了解详细信息](how-to-managed-cluster-large-virtual-machine-scale-sets.md)
* 这样，给定节点类型将最多支持 1000 个节点

现提供示例模板：[Service Fabric 无状态节点类型模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enable-stateless-node-types-in-a-service-fabric-managed-cluster"></a>在 Service Fabric 托管群集中启用无状态节点类型
若要在节点类型资源中将一个或多个节点类型设置为无状态，请将“isStateless”属性设置为“true” 。 部署具有无状态节点类型的 Service Fabric 群集时，需至少具有一种在群集中不是无状态的主节点类型。

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
    "isStateless": true,
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

## <a name="configure-stateless-node-types-with-multiple-availability-zones"></a>配置具有多个可用性区域的无状态节点类型
若要配置跨多个可用性区域的无状态节点类型，请遵循[跨可用性区域的 Service Fabric 群集](.\service-fabric-cross-availability-zones.md)。 

>[!NOTE]
> 必须在群集级别设置区域复原能力属性，并且不能就地更改该属性。

## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>迁移为在群集中使用无状态节点类型
对于所有迁移场景，都需要添加一个新的无状态节点类型。 现有节点类型不能迁移到无状态类型。 可以向现有 Service Fabric 托管群集添加新的无状态节点类型，并从该群集中删除所有初始节点类型。 

## <a name="next-steps"></a>后续步骤 

若要详细了解 Service Fabric 托管群集，请参阅：

> [!div class="nextstepaction"]
> [Service Fabric 托管群集常见问题](./faq-managed-cluster.yml)
