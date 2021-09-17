---
title: Azure PowerShell - 启用静态双重加密 - 托管磁盘
description: 使用 Azure PowerShell 为托管磁盘数据启用静态双重加密。
author: roygara
ms.date: 06/29/2021
ms.topic: how-to
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 84f8b0971b7585b25cccec485ef15ad9876a60aa
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690835"
---
# <a name="use-the-azure-powershell-module-to-enable-double-encryption-at-rest-for-managed-disks"></a>使用 Azure PowerShell 模块为托管磁盘启用静态双重加密

适用于：:heavy_check_mark: Windows VM 

Azure 磁盘存储支持对托管磁盘的静态双重加密。 有关静态双重加密的概念信息以及其他托管磁盘加密类型，请参阅磁盘加密文章的[静态双重加密](../disk-encryption.md#double-encryption-at-rest)部分。

## <a name="prerequisites"></a>先决条件

安装最新的 [Azure PowerShell 版本](/powershell/azure/install-az-ps)，并使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 登录到 Azure 帐户。

## <a name="getting-started"></a>入门

1. 创建 Azure Key Vault 和加密密钥的实例。

    创建 Key Vault 实例时，必须启用软删除和清除保护。 软删除可确保 Key Vault 在给定的保留期（默认为 90 天）内保留已删除的密钥。 清除保护可确保在保留期结束之前，无法永久删除已删除的密钥。 这些设置可防止由于意外删除而丢失数据。 使用 Key Vault 加密托管磁盘时，这些设置是必需的。
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westus2"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  创建 DiskEncryptionSet，并将 encryptionType 设置为 EncryptionAtRestWithPlatformAndCustomerKeys。 在 Azure 资源管理器 (ARM) 模板中使用 API 版本 2020-05-01。 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $keyVault.ResourceId `
    -keyVaultKeyUrl $key.Key.Kid `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region $LocationName
    ```

1. 授予对密钥保管库的 DiskEncryptionSet 资源访问权限。

    > [!NOTE]
    > Azure 可能需要几分钟时间才能在 Azure Active Directory 中创建 DiskEncryptionSet 的标识。 如果在运行以下命令时收到类似于“找不到 Active Directory 对象”的错误，请等待几分钟，然后重试。

    ```powershell  
    $des=Get-AzDiskEncryptionSet -name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>后续步骤

创建并配置这些资源之后，可以使用它们来保护托管磁盘。 以下链接包含示例脚本，每个脚本都有各自的方案，可用于保护托管磁盘。

- [Azure PowerShell - 使用客户管理的密钥进行服务器端加密 - 托管磁盘](disks-enable-customer-managed-keys-powershell.md)
- [Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
