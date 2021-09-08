---
title: 将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型
description: 下面介绍如何将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型
ms.topic: article
ms.date: 8/02/2021
ms.openlocfilehash: 8755eb7551dd7f7e572632ee50eb57467f675822
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866389"
---
# <a name="virtual-machine-scale-set-extension-support-on-service-fabric-managed-cluster-node-types"></a>Service Fabric 托管群集节点类型上的虚拟机规模集扩展支持

Service Fabric 托管群集中的每个节点类型都受虚拟机规模集的支持。 这使你能够将[虚拟机规模集扩展](../virtual-machines/extensions/overview.md)添加到 Service Fabric 托管群集节点类型。 扩展是小型应用程序，用于在 Azure VM 上提供部署后配置和自动化。 Azure 平台可承载许多扩展，涵盖 VM 配置、监视、安全性和实用工具应用程序。 发布服务器采用某个应用程序，将其包装到扩展中，对安装进行简化。 你只需提供必需的参数。 

## <a name="add-a-virtual-machine-scale-set-extension"></a>添加虚拟机规模集扩展
可以使用 [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell 命令将虚拟机规模集扩展添加到 Service Fabric 托管群集节点类型。

或者，可以在 Azure 资源管理器模板的 Service Fabric 托管群集节点类型上添加虚拟机规模集扩展，例如：

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
