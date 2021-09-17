---
title: 通过主机加密启用端到端加密 - Azure CLI - 托管磁盘
description: 通过主机加密在 Azure 托管磁盘上启用端到端加密。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c5bdbcf37818cba66b9eaf7ba4f5f95deb785fe4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691912"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>使用 Azure CLI 通过主机加密来启用端到端加密

**适用于：** :heavy_check_mark: Linux VMs :heavy_check_mark: 灵活规模集 

启用主机加密时，存储在 VM 主机上的数据将静态加密，且已加密的数据将流向存储服务。 有关主机加密以及其他托管磁盘加密类型的概念信息，请参阅[主机加密 - 为 VM 数据启用端到端加密](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

可以通过编程方式拉取受支持的 VM 大小的完整列表。 若要了解如何以编程方式检索它们，请参阅[查找支持的 VM 大小](#finding-supported-vm-sizes)部分。
升级 VM 大小将导致进行验证，以检查新 VM 大小是否支持 EncryptionAtHost 功能。

## <a name="prerequisites"></a>先决条件

必须先为订阅启用此功能，然后才能使用 VM/VMSS 的 EncryptionAtHost 属性。 请按照以下步骤为订阅启用此功能：

1.  执行以下命令，为订阅注册此功能

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  在试用该功能之前，请使用以下命令检查注册状态是否为“已注册”（需要几分钟）。

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>创建 Azure Key Vault 和 DiskEncryptionSet

启用该功能后，需要设置 Azure Key Vault 和 DiskEncryptionSet（如果尚未设置）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>示例

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>使用客户管理的密钥创建 VM，并启用主机加密。 

使用之前创建的 DiskEncryptionSet 的资源 URI 创建包含托管磁盘的 VM，以便使用客户管理的密钥加密 OS 和数据磁盘的缓存。 临时磁盘通过平台管理的密钥加密。 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>使用平台管理的密钥创建 VM，并启用主机加密。 

创建一个 VM，并启用主机加密，以便使用平台管理的密钥加密 OS/数据磁盘和临时磁盘的缓存。 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>更新 VM 以启用主机加密。 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>检查 VM 的主机加密状态

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```


### <a name="update-a-vm-to-disable-encryption-at-host"></a>更新虚拟机以禁用主机加密。 

必须先解除分配虚拟机，然后才能在主机上禁用加密。

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=false
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>使用客户管理的密钥创建虚拟机规模集，并启用主机加密。 

使用之前创建的 DiskEncryptionSet 的资源 URI 创建包含托管磁盘的虚拟机规模集，以便使用客户管理的密钥加密 OS 和数据磁盘的缓存。 临时磁盘通过平台管理的密钥加密。 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>使用平台管理的密钥创建虚拟机规模集，并启用主机加密。 

创建一个虚拟机规模集，并启用主机加密，以便使用平台管理的密钥加密 OS/数据磁盘和临时磁盘的缓存。 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>更新虚拟机规模集以启用主机加密。 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>检查虚拟机规模集的主机加密状态

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

### <a name="update-a-virtual-machine-scale-set-to-disable-encryption-at-host"></a>更新虚拟机规模集以禁用主机加密。 

你可以在虚拟机规模集上禁用主机上的加密，但这只会影响在主机上禁用加密之后创建的虚拟机。 对于现有虚拟机，必须先将虚拟机解除分配，[在该虚拟机上禁用主机上的加密](#update-a-vm-to-disable-encryption-at-host)，然后才能重新分配虚拟机。

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=false
```

## <a name="finding-supported-vm-sizes"></a>找到支持的 VM 大小

不支持“旧的 VM 大小”。 可以通过以下任一方法查找支持的 VM 大小列表：

调用[资源 SKU API](/rest/api/compute/resourceskus/list) 并检查 `EncryptionAtHostSupported` 功能是否设置为 True。

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

或者，调用 [AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) PowerShell cmdlet。

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>后续步骤

创建并配置这些资源之后，可以使用它们来保护托管磁盘。 以下链接包含示例脚本，每个脚本都有各自的方案，可用于保护托管磁盘。

[Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
