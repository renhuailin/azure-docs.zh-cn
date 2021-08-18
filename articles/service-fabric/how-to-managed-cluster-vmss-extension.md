---
title: 将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型
description: 下面介绍如何将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型
ms.topic: article
ms.date: 5/10/2021
ms.openlocfilehash: 0c0aac8d7804caeb6c08657b1ed36c45958a0ea5
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033688"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type"></a>将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型

Service Fabric 托管群集中的每个节点类型都受虚拟机规模集的支持。 这使你能够将[虚拟机规模集扩展](../virtual-machines/extensions/overview.md)添加到 Service Fabric 托管群集节点类型。

可使用 [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell 命令将虚拟机规模集扩展添加到节点类型。

或者，你可以通过 Azure 资源管理器模板将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型，例如：

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

有关配置 Service Fabric 托管群集节点类型的详细信息，请参阅[托管群集节点类型](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Service Fabric 托管群集，请参阅：

> [!div class="nextstepaction"]
> [Service Fabric 托管群集常见问题](./faq-managed-cluster.yml)
