---
title: Azure Service Fabric 基础结构即代码最佳做法
description: 用于管理 Azure Service Fabric 基础结构即代码的最佳做法和设计注意事项。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ae7eaa628e0e3cfc256b7cd01035d9e068f47867
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671110"
---
# <a name="infrastructure-as-code"></a>基础结构即代码

在生产方案中，使用资源管理器模板创建 Azure Service Fabric 群集。 资源管理器模板可以更好地控制资源属性，并确保你具有一致的资源模型。

[GitHub 上的 Azure 示例](https://github.com/Azure-Samples/service-fabric-cluster-templates)中提供了适用于 Windows 和 Linux 的示例资源管理器模板。 这些模板可用作群集模板的起点。 下载 `azuredeploy.json` 和 `azuredeploy.parameters.json` 并编辑它们以满足你的自定义要求。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要部署你在上面下载的 `azuredeploy.json` 和 `azuredeploy.parameters.json` 模板，请使用以下 Azure CLI 命令：

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

使用 PowerShell 创建资源

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="service-fabric-resources"></a>Service Fabric 资源

可以通过 Azure 资源管理器，将应用程序和服务部署到 Service Fabric 群集。 有关详细信息，请参阅[将应用程序和服务作为 Azure 资源管理器资源进行管理](./service-fabric-application-arm-resource.md)。 下面是要在资源管理器模板资源中包括的最佳做法 Service Fabric 应用程序特定资源。

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

若要使用 Azure 资源管理器部署应用程序，必须首先[创建一个 sfpkg](./service-fabric-package-apps.md#create-an-sfpkg) Service Fabric 应用程序包。 下面的 python 脚本是有关如何创建 sfpkg 的示例：

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="virtual-machine-os-automatic-upgrade-configuration"></a>虚拟机 OS 自动升级配置

升级虚拟机是用户启动的操作，建议为 Service Fabric 群集节点补丁的管理[启用虚拟机规模集自动映像升级](how-to-patch-cluster-nodes-windows.md)。 补丁业务流程应用程序 (POA) 是一种替代解决方案，适用于非 Azure 托管群集。 尽管可以在 Azure 中使用 POA，但托管它需要更多的管理，而不仅仅是简单地启用规模集自动 OS 映像升级。 下面是用于启用自动 OS 升级的虚拟机规模集资源管理器模板属性：

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
使用带 Service Fabric 的 OS 自动升级时，将推出新的 OS 映像（每次一个更新域），以维持 Service Fabric 中运行的服务的高可用性。 若要利用 Service Fabric 中的自动 OS 升级，必须将群集配置为使用银级持久性层或更高层级。

确保将以下注册表项设置为 false，以防止 Windows 主机启动不协调的更新：HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU。

设置以下虚拟机规模集模板属性以禁用 Windows 更新：
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="service-fabric-cluster-upgrade-configuration"></a>Service Fabric 群集升级配置

下面是用于启用自动升级的 Service Fabric 群集模板属性：

```json
"upgradeMode": "Automatic",
```

若要手动升级群集，请将 cab/deb 发行版下载到群集虚拟机，然后调用以下 PowerShell：

```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)
