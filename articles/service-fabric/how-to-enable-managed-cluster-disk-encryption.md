---
title: 为 Service Fabric 托管群集节点启用磁盘加密
description: 了解如何在 Windows 中使用 ARM 模板为 Azure Service Fabric 托管群集节点启用磁盘加密。
ms.topic: how-to
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b10c5fbade903ec9eb5786fc33eaaddff212956
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284122"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-nodes"></a>为 Service Fabric 托管群集节点启用磁盘加密

本指南介绍如何使用适用于[虚拟机规模集](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)的 [Azure 磁盘加密](../virtual-machines/windows/disk-encryption-overview.md)功能，在 Windows 中通过 Azure 资源管理器 (ARM) 模板在 Service Fabric 托管群集节点上启用磁盘加密。

## <a name="register-for-azure-disk-encryption"></a>注册 Azure 磁盘加密

针对虚拟机规模集的磁盘加密预览版需要自我注册。 运行下面的命令：

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

运行以下命令，检查注册状态：

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

状态更改为“已注册”后，即可执行后续操作。

## <a name="provision-a-key-vault-for-disk-encryption"></a>为密钥保管库预配磁盘加密

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 密钥保管库和 Service Fabric 托管群集必须位于同一个 Azure 区域和订阅中。 满足这些要求后，就可为新的或现有的密钥保管库启用磁盘加密，然后使用它们。

### <a name="create-key-vault-with-disk-encryption-enabled"></a>创建启用了磁盘加密的密钥保管库

运行以下命令，创建启用磁盘加密的新密钥保管库。 请确保密钥保管库的区域[受 Service Fabric 托管群集支持](./faq-managed-cluster.yml#what-regions-are-supported-)，并与群集所在区域相同。

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

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>更新现有密钥保管库，以启用磁盘加密

运行以下命令，为现有密钥保管库启用磁盘加密。

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

以下步骤将指导你完成所需的模板更改，以便在[现有托管群集](tutorial-managed-cluster-deploy.md)上启用磁盘加密。 或者，也可使用以下模板部署启用磁盘加密的新 Service Fabric 托管群集： https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. 将以下参数添加到模板，并在 `keyVaultResourceId` 下替换你自己的订阅、资源组名称和保管库名称：

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
                "typeHandlerVersion": "2.2", 
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

3. 最后，更新参数文件，在 keyVaultResourceId 中替换你自己的订阅、资源组和密钥保管库名称：

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

准备就绪后，部署这些更改，在托管群集上启用磁盘加密。

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

你可使用 `Get-AzVmssDiskEncryption` 命令来检查某个节点类型的底层规模集上的磁盘加密状态。 首先，需要找到托管群集的支持资源组（包含底层虚拟网络、负载均衡器、公共 IP、NSG、规模集和存储帐户）的名称。 确保将 `VmssName` 修改为要检查的任何群集节点类型名称（在部署模板中指定）。

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

[示例：标准 SKU Service Fabric 托管群集，1 个节点类型启用了磁盘加密](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[适用于 Windows VM 的 Azure 磁盘加密](../virtual-machines/windows/disk-encryption-overview.md)

[使用 Azure 资源管理器加密虚拟机规模集](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)