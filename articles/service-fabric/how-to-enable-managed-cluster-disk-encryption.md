---
title: 为 Service Fabric 托管群集 (预览) 节点启用磁盘加密
description: 了解如何在 Windows 中使用 ARM 模板为 Azure Service Fabric 托管群集节点启用磁盘加密。
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642356"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>为 Service Fabric 托管群集 (预览) 节点启用磁盘加密

在本指南中，你将了解如何使用 azure [磁盘加密](../virtual-machines/windows/disk-encryption-overview.md) 功能通过 azure 资源管理器 (ARM) 模板在 [Windows 的 Service Fabric](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) 托管群集节点上启用磁盘加密。

> [!IMPORTANT]
> 虚拟机规模集磁盘加密预览版尚不支持映像升级或重置映像。 如果需要升级 OS 映像，请不要使用。

## <a name="register-for-azure-disk-encryption"></a>注册 Azure 磁盘加密

针对虚拟机规模集的磁盘加密预览版需要自我注册。 运行以下命令：

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

通过运行以下内容检查注册状态：

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

状态更改为 " *已注册*" 后，即已准备好继续。

## <a name="provision-a-key-vault-for-disk-encryption"></a>为磁盘加密预配 Key Vault

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 Key Vault 和 Service Fabric 托管群集必须位于同一个 Azure 区域和订阅中。 只要满足这些要求，就可以使用新的或现有的 Key Vault，只需为磁盘加密启用即可。

### <a name="create-key-vault-with-disk-encryption-enabled"></a>创建启用了磁盘加密的 Key Vault

运行以下命令以创建磁盘加密的新 Key Vault。 请确保 [Service Fabric 管理的群集支持](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) Key Vault 区域，并且与群集位于同一区域。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>更新现有 Key Vault 以启用磁盘加密

运行以下命令为现有 Key Vault 启用磁盘加密。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>更新用于磁盘加密的模板和参数文件

以下步骤将指导你完成所需的模板更改，以便在 [现有托管群集](tutorial-managed-cluster-deploy.md)上启用磁盘加密。 或者，你可以使用此模板启用磁盘加密来部署新的 Service Fabric 托管群集： https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. 将以下参数添加到模板，并将你自己的订阅、资源组名称和保管库名称替换为 `keyVaultResourceId` ：

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. 接下来，将 `AzureDiskEncryption` VM 扩展添加到模板中的托管群集节点类型：

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. 最后，更新参数文件，在 *keyVaultResourceId* 中替换自己的订阅、资源组和密钥保管库名称：

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>部署并验证更改

准备就绪后，部署这些更改以在托管群集上启用磁盘加密。

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

您可以使用命令检查节点类型的底层规模集上的磁盘加密状态 `Get-AzVmssDiskEncryption` 。 首先，需要找到托管群集的支持资源组的名称， (包含基础虚拟网络、负载均衡器、公共 IP、NSG、规模集 (s) 和存储帐户) 。 务必修改 `VmssName` 要检查的任何群集节点类型名称，) 中指定 (。

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

输出应如下所示：

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>后续步骤

[示例：标准 SKU Service Fabric 托管群集，1个启用了磁盘加密的节点类型](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[适用于 Windows VM 的 Azure 磁盘加密](../virtual-machines/windows/disk-encryption-overview.md)

[使用 Azure 资源管理器加密虚拟机规模集](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
