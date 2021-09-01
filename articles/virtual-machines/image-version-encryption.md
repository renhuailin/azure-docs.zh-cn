---
title: 创建使用你自己的密钥加密的映像版本
description: 使用客户管理的加密密钥在共享映像库中创建映像版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 7/1/2021
ms.author: olayemio
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c04dffcad178694f4f2548f38aa4c1d512c6fe60
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322988"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>预览版：使用客户管理的密钥加密映像

共享映像库中的映像是作为快照存储的，因此会通过服务器端加密来自动对其进行加密。 服务器端加密使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，这是可用的最强分组加密技术之一。 服务器端加密也符合 FIPS 140-2 规范。 有关加密模块基础 Azure 托管磁盘的详细信息，请参阅[加密 API：下一代](/windows/desktop/seccng/cng-portal)。

可以使用平台管理的密钥加密映像，也可以使用你自己的密钥。 还可以同时使用这两种方法进行双重加密。 如果你选择管理使用自己的密钥进行的加密，可以指定客户管理的密钥，用于加密和解密映像中的所有磁盘。 

通过客户管理的密钥进行的服务器端加密使用 Azure Key Vault。 可以将 [RSA 密钥](../key-vault/keys/hsm-protected-keys.md)导入密钥保管库，也可以在 Azure Key Vault 中生成新的 RSA 密钥。

## <a name="prerequisites"></a>先决条件

本文要求你已在要复制映像的每个区域中设置了磁盘加密：

- 若只使用客户管理的密钥，请参阅那些介绍如何使用 [Azure 门户](./disks-enable-customer-managed-keys-portal.md)或 [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation) 为客户管理的密钥启用服务器端加密的文章。

- 若要同时使用平台管理的密钥和客户管理的密钥（进行双重加密），请参阅那些介绍如何通过 [Azure 门户](./disks-enable-double-encryption-at-rest-portal.md)或 [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md) 启用静态双重加密的文章。

   > [!IMPORTANT]
   > 必须使用链接 [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) 访问 Azure 门户。 “静态双重加密”目前不会显示在公共 Azure 门户中，除非你使用该链接。

## <a name="limitations"></a>限制

使用客户管理的密钥加密共享映像库中的映像时存在以下限制：   

- 加密密钥集必须与映像位于同一订阅中。

- 加密密钥集是区域资源，因此每个区域都需要不同的加密密钥集。

- 不能复制或共享那些使用客户管理的密钥的映像。 

- 使用自己的密钥加密磁盘或映像后，不能重新改用平台管理的密钥来加密这些磁盘或映像。


## <a name="powershell"></a>PowerShell

若要指定映像版本的磁盘加密集，请将 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) 与 `-TargetRegion` 参数配合使用： 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>创建 VM

可以从共享映像库创建虚拟机 (VM)，并使用客户管理的密钥来加密磁盘。 语法与从映像创建[通用化](vm-generalized-image-version-powershell.md)或[专用化](vm-specialized-image-version-powershell.md) VM 相同。 请使用扩展的参数集并将 `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` 添加到 VM 配置。

对于数据磁盘，使用 [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) 时请添加 `-DiskEncryptionSetId $setID` 参数。


## <a name="cli"></a>CLI 

若要指定映像版本的磁盘加密集，请将 [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) 与 `--target-region-encryption` 参数配合使用。 `--target-region-encryption` 的格式是用于加密 OS 和数据磁盘的密钥列表（密钥以逗号分隔）。 它应如下所示： `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`。 

如果 OS 磁盘的源是托管磁盘或 VM，请使用 `--managed-image` 指定映像版本的源。 在此示例中，源是一个托管映像，其中包含 OS 磁盘，以及位于 LUN 0 上的数据磁盘。 OS 磁盘将通过 DiskEncryptionSet1 加密，数据磁盘将通过 DiskEncryptionSet2 加密。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

如果 OS 磁盘的源是快照，请使用 `--os-snapshot` 指定 OS 磁盘。 如果还应在映像版本中包含数据磁盘快照，请添加这些快照。 使用 `--data-snapshot-luns` 指定 LUN，使用 `--data-snapshots` 指定快照

在此示例中，源是磁盘快照。 有一个 OS 磁盘，以及一个位于 LUN 0 上的数据磁盘。 OS 磁盘将通过 DiskEncryptionSet1 加密，数据磁盘将通过 DiskEncryptionSet2 加密。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>创建 VM

可以从共享映像库创建 VM，并使用客户管理的密钥来加密磁盘。 语法与从映像创建[通用化](vm-generalized-image-version-cli.md)或[专用化](vm-specialized-image-version-cli.md) VM 相同。 只需添加 `--os-disk-encryption-set` 参数以及加密集的 ID。 对于数据磁盘，请添加 `--data-disk-encryption-sets`，以及数据磁盘的磁盘加密集列表（磁盘加密集以空格分隔）。


## <a name="portal"></a>门户

在门户中创建映像版本时，可以使用“加密”选项卡来应用存储加密集。

1. 在“创建映像版本”页上，选择“加密”选项卡。 
2. 在“加密类型”中，请选择“使用客户管理的密钥进行静态加密”或“通过平台管理的密钥和客户管理的密钥进行双重加密”。   
3. 对于映像中的每个磁盘，请从“磁盘加密集”下拉列表中选择一个加密集。 

### <a name="create-the-vm"></a>创建 VM

可以从映像版本创建 VM，并使用客户管理的密钥来加密磁盘。 在门户中创建 VM 时，请在“磁盘”选项卡上，选择“使用客户管理的密钥进行静态加密”或“通过平台管理的密钥和客户管理的密钥进行双重加密”作为“加密类型”。    然后可以从下拉列表中选择加密集。

## <a name="next-steps"></a>后续步骤

详细了解[服务器端磁盘加密](./disk-encryption.md)。

有关如何提供购买计划信息的信息，请参阅[在创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。
