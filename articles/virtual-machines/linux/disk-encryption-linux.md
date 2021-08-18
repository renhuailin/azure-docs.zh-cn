---
title: Linux VM 上的 Azure 磁盘加密方案
description: 本文介绍了如何针对各种方案为 Linux VM 启用 Microsoft Azure 磁盘加密
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 779574bd5d4e7b982cac065b2bb79bc7b483cee8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467700"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Linux VM 上的 Azure 磁盘加密方案

适用于 Linux 虚拟机 (VM) 的 Azure 磁盘加密使用 Linux 的 DM-Crypt 功能为 OS 磁盘和数据磁盘提供全磁盘加密。 此外，它还在使用 EncryptFormatAll 功能时提供临时磁盘加密。

Azure 磁盘加密[与 Azure Key Vault 集成](disk-encryption-key-vault.md)，帮助你控制和管理磁盘加密密钥与机密。 有关此服务的概述，请参阅[适用于 Linux VM 的 Azure 磁盘加密](disk-encryption-overview.md)。

只能对具有[支持的 VM 大小和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)的虚拟机应用磁盘加密。 还必须满足以下先决条件：

- [VM 的其他要求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [网络要求](disk-encryption-overview.md#networking-requirements)
- [加密密钥存储要求](disk-encryption-overview.md#encryption-key-storage-requirements)

在所有情况下，在对磁盘进行加密之前，应该[创建快照](snapshot-copy-managed-disk.md)和/或备份。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 加密之前，需要备份包含托管磁盘的 VM。 备份之后，可以通过指定 -skipVmBackup 参数，使用 [Set-AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension) 来加密托管磁盘。 有关如何备份和还原已加密 VM 的详细信息，请参阅 [Azure 备份](../../backup/backup-azure-vms-encryption.md)一文。 

>[!WARNING]
> - 如果之前是使用 Azure 磁盘加密与 Azure AD 来加密 VM，则必须继续使用此选项来加密 VM。 有关详细信息，请参阅[使用 Azure AD 进行的 Azure 磁盘加密（以前的版本）](disk-encryption-overview-aad.md)。 
>
> - 加密 Linux OS 卷时，应将 VM 视为不可用。 我们强烈建议在加密过程中避免 SSH 登录，以避免阻止加密过程中需要访问的任何打开文件的问题。 若要检查进度，请使用 [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet 或 [vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI 命令。 对于 30GB 操作系统卷，此过程可能需要几小时才能完成，还需要额外的时间来加密数据卷。 除非使用“encrypt format all”选项，否则数据卷加密时间将与数据卷的大小和数量成比例。 
> - 在 Linux VM 上，仅支持对数据卷禁用加密。 如果 OS 卷已加密，则不支持对数据卷或 OS 卷禁用加密。  

## <a name="install-tools-and-connect-to-azure"></a>安装工具并连接到 Azure

可以通过 [Azure CLI](/cli/azure) 和 [Azure PowerShell](/powershell/azure/new-azureps-module-az) 启用和管理 Azure 磁盘加密。 为此，必须在本地安装工具并连接到 Azure 订阅。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) 是用于管理 Azure 资源的命令行工具。 CLI 旨在提高数据查询灵活性、支持非阻塞进程形式的长时间操作，以及简化脚本编写。 可以按照[安装 Azure CLI](/cli/azure/install-azure-cli) 中的步骤在本地安装它。

 

若要[使用 Azure CLI 登录 Azure 帐户](/cli/azure/authenticate-azure-cli)，请使用 [az login](/cli/azure/reference-index#az_login) 命令。

```azurecli
az login
```

若要选择登录到的租户，请使用：
    
```azurecli
az login --tenant <tenant>
```

如果有多个订阅并想要指定其中的一个，请使用 [az account list](/cli/azure/account#az_account_list) 获取订阅列表，然后使用 [az account set](/cli/azure/account#az_account_set) 指定订阅。
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

有关详细信息，请参阅 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli)。 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az 模块](/powershell/azure/new-azureps-module-az)提供了一组 cmdlet，它们使用 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)模型来管理 Azure 资源。 可以在浏览器中将它与 [Azure Cloud Shell](../../cloud-shell/overview.md) 结合使用，也可以按照[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)中的说明操作，将它安装在本地计算机上。 

如果已在本地安装 PowerShell，请确保使用最新版本的 Azure PowerShell SDK 来配置 Azure 磁盘加密。 下载最新版本的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)。

若要[使用 Azure PowerShell 登录 Azure 帐户](/powershell/azure/authenticate-azureps)，请使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet。

```powershell
Connect-AzAccount
```

如果有多个订阅并要指定一个，请先运行 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet 列出订阅，再运行 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet：

```powershell
Set-AzContext -Subscription <SubscriptionId>
```

运行 [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet 将验证是否选择了正确的订阅。

若要确认已安装 Azure 磁盘加密 cmdlet，请使用 [Get-command](/powershell/module/microsoft.powershell.core/get-command) cmdlet：
     
```powershell
Get-command *diskencryption*
```
有关详细信息，请参阅 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)。 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>在现有或正在运行的 Linux VM 上启用加密
在此方案中，可以使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 如果需要虚拟机扩展的架构信息，请参阅[适用于 Linux 扩展的 Azure 磁盘加密](../extensions/azure-disk-enc-linux.md)一文。

>[!IMPORTANT]
 >启用 Azure 磁盘加密之前，必须在其外部创建基于托管磁盘的 VM 实例的快照和/或备份。 可以通过门户或 [Azure 备份](../../backup/backup-azure-vms-encryption.md)创建托管磁盘的快照。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 备份后，可以通过指定 -skipVmBackup 参数，使用 Set-AzVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 在未备份基于托管磁盘的 VM 且未指定此参数的情况下，对该 VM 使用 Set-AzVMDiskEncryptionExtension 命令会失败。 
>
>加密或禁用加密可能导致 VM 重新启动。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>使用 Azure CLI 在现有或正在运行的 Linux VM 上启用加密 

通过安装并使用 [Azure CLI](/cli/azure/) 命令行工具，可以在加密的 VHD 上启用磁盘加密。 可以在浏览器中结合 [Azure Cloud Shell](../../cloud-shell/overview.md) 使用这些 cmdlet，或者将它们安装在本地计算机上并在任何 PowerShell 会话中使用。 若要在 Azure 中现有或正在运行的 Linux VM 上启用加密，请运行以下 CLI 命令：

使用 [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_show) 命令在 Azure 中运行的虚拟机上启用加密。

- **加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **验证磁盘是否已加密：** 若要检查 VM 的加密状态，请运行 [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) 命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) 命令。 只允许对 Linux VM 的数据卷禁用加密。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>使用 PowerShell 在现有或正在运行的 Linux VM 上启用加密
使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 在 Azure 中运行的虚拟机上启用加密。 在加密磁盘之前，创建[快照](snapshot-copy-managed-disk.md)和/或使用 [Azure 备份](../../backup/backup-azure-vms-encryption.md)备份 VM。 已在 PowerShell 脚本中指定 -skipVmBackup 参数以加密正在运行的 Linux VM。

-  **加密正在运行的 VM：** 以下脚本初始化变量并运行 Set-AzVMDiskEncryptionExtension cmdlet。 已创建必要的资源组、VM 和 Key Vault。 请将 MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault 替换为自己的值。 修改 -VolumeType 参数，以指定要加密哪些磁盘。

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **使用 KEK 加密正在运行的 VM：** 如果加密的是数据磁盘而不是 OS 磁盘，可能需要添加 -VolumeType 参数。 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **验证磁盘是否已加密：** 若要检查 VM 的加密状态，请运行 [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet。 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 只允许对 Linux VM 的数据卷禁用加密。
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>使用模板在现有或正在运行的 Linux VM 上启用加密

可通过[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-linux-vm-without-aad)在 Azure 中为现有或正在运行的 Linux VM 启用磁盘加密。

1. 在 Azure 快速入门模板中，单击“部署到 Azure”。

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 单击“创建”，在现有或正在运行的 VM 上启用加密。

下表列出了现有的或正在运行的 VM 的资源管理器模板参数：

| 参数 | 说明 |
| --- | --- |
| vmName | 运行加密操作的 VM 的名称。 |
| KeyVaultName | 加密密钥应上传到的 Key Vault 的名称。 可使用 cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 或 Azure CLI 命令 `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` 获取该名称。|
| keyVaultResourceGroup | 包含 Key Vault 的资源组的名称。 |
|  keyEncryptionKeyURL | 用于对加密密钥进行加密的密钥加密密钥的 URL。 如果在 UseExistingKek 下拉列表中选择“nokek”  ，则此参数为可选参数。 如果在 UseExistingKek 下拉列表中选择“kek”，则必须输入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 要对其执行加密操作的卷的类型。 有效值为“OS”、“Data”和“All”。 
| forceUpdateTag | 每次操作需要强制运行时，传入一个像 GUID 这样的唯一值。 |
| location | 所有资源的位置。 |

有关配置 Linux VM 磁盘加密模板的详细信息，请参阅[适用于 Linux 的 Azure 磁盘加密](../extensions/azure-disk-enc-linux.md)。

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>对 Linux VM 上的数据磁盘使用 EncryptFormatAll 功能

**EncryptFormatAll** 参数可以减少加密 Linux 数据磁盘所需的时间。 满足特定条件的分区与当前文件系统一起格式化，然后重新装载回在命令执行前的位置。 如果想要排除某个符合条件的数据磁盘，可以在运行命令之前卸载该磁盘。

 在此命令运行后，先前装载的所有驱动器都会被格式化，并在现在已空的驱动器之上启动加密层。 如果你选择这种方式，附加到 VM 的临时磁盘也会得到加密。 如果临时磁盘已重置，则会在下次机会时通过 Azure 磁盘加密解决方案为 VM 重新格式化和重新加密它。 一旦资源磁盘被加密，[Microsoft Azure Linux 代理](../extensions/agent-linux.md)就无法管理资源磁盘和启用交换文件，但你可以手动配置交换文件。

>[!WARNING]
> 如果 VM 的数据卷上存在所需的数据，则不应使用 EncryptFormatAll。 卸载磁盘可将其从加密项中排除。 首先应该在测试 VM 上试用 EncryptFormatAll，以了解功能参数及其影响，然后再尝试在生产 VM 上使用该参数。 EncryptFormatAll 选项会格式化数据磁盘，因此磁盘上的所有数据都会丢失。 在继续之前，请验证是否已正确卸载想要排除的磁盘。 </br></br>
 >如果在更新加密设置时设置此参数，可能会导致在实际加密之前重新启动。 在这种情况下，还需要从 fstab 文件中删除不想要格式化的磁盘。 同样，在启动加密操作之前，应将想要加密并格式化的分区添加到 fstab 文件。 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll 条件
该参数会遍历并加密满足以下 **所有** 条件的所有分区：
- 不是根/OS/启动分区
- 尚未加密
- 不是 BEK 卷
- 不是 RAID 卷
- 不是 LVM 卷
- 已装载

加密组成 RAID 或 LVM 卷而不是 RAID 或 LVM 卷的磁盘。

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>通过 Azure CLI 使用 EncryptFormatAll 参数
使用 [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) 命令在 Azure 中运行的虚拟机上启用加密。

-  **使用 EncryptFormatAll 加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>结合 PowerShell cmdlet 使用 EncryptFormatAll 参数
结合 EncryptFormatAll 参数使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet。 

**使用 EncryptFormatAll 加密正在运行的 VM：** 例如，以下脚本会初始化变量，并结合 EncryptFormatAll 参数运行 Set-AzVMDiskEncryptionExtension cmdlet。 已创建必要的资源组、VM 和 Key Vault。 请将 MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault 替换为自己的值。
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>结合逻辑卷管理器 (LVM) 使用 EncryptFormatAll 参数 
我们建议采用 LVM-on-crypt 设置。 对于下面的所有示例，请将设备路径和装载点替换为适合用例的任何值。 可按如下所述完成此设置：

1.  添加构成 VM 的数据磁盘。

1. 格式化、装载这些磁盘并将其添加到 fstab 文件。

1. 选择分区标准，创建跨越整个驱动器的分区，然后格式化该分区。 此处使用了 Azure 生成的符号链接。 使用符号链接可避免设备名更改所造成的问题。 有关详细信息，请参阅[排查设备名问题](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)一文。
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. 装载磁盘：

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    添加到 fstab 文件：

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. 在运行 Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 的同时使用 -EncryptFormatAll，以加密这些磁盘。

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    若要使用密钥加密密钥 (KEK)，请分别将 KEK 的 URI 和密钥保管库的 ResourceID 传递到 -KeyEncryptionKeyUrl 和 -KeyEncryptionKeyVaultId 参数：

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. 在这些新磁盘的顶层设置 LVM。 请注意，VM 在完成启动后，加密的驱动器会解锁。 因此，后续的 LVM 装载必定会延迟。

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>通过客户加密的 VHD 和加密密钥新建的 VM
在此方案中，可以使用 PowerShell cmdlet 或 CLI 命令启用加密。 

参考相同 Azure 磁盘加密脚本中的说明来准备可在 Azure 中使用的预加密映像。 创建映像后，可使用下一部分中的步骤创建加密的 Azure VM。

* [准备预加密的 Linux VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >启用 Azure 磁盘加密之前，必须在其外部创建基于托管磁盘的 VM 实例的快照和/或备份。 可以从门户创建托管磁盘的快照，也可以使用 [Azure 备份](../../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 备份后，可以通过指定 -skipVmBackup 参数，使用 Set-AzVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 在未备份基于托管磁盘的 VM 且未指定此参数的情况下，对该 VM 使用 Set-AzVMDiskEncryptionExtension 命令会失败。 
>
> 加密或禁用加密可能导致 VM 重新启动。 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>使用 Azure PowerShell 加密包含预加密 VHD 的 VM 
可以使用 PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) 在加密的 VHD 上启用磁盘加密。 以下示例显示了一些常用参数。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新添加的数据磁盘上启用加密

可以使用 [az vm disk attach](add-disk.md) 或[通过 Azure 门户](attach-disk-portal.md)添加新数据磁盘。 在加密之前，需要先装载新附加的数据磁盘。 必须请求加密数据驱动器，因为加密正在进行时，该驱动器不可用。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>使用 Azure CLI 在新添加的磁盘上启用加密

 如果 VM 先前使用“All”进行加密，则 --volume-type 参数应保留为“All”。 All 包括 OS 和数据磁盘。 如果 VM 先前使用卷类型“OS”进行加密，则应将 --volume-type 参数更改为“All”，以便包含 OS 和新数据磁盘。 如果 VM 仅使用卷类型“Data”进行加密，则它可以保留为“Data”，如下所示。 添加新数据磁盘并将其附加到 VM 并不足以为加密做准备。 在启用加密之前，还必须格式化新附加的磁盘并将其正确装载在 VM 中。 在 Linux 上，磁盘必须使用[永久性块设备名称](../troubleshooting/troubleshoot-device-names-problems.md)装载在 /etc/fstab 中。  

与 Powershell 语法相反，在启用加密时，CLI 不要求用户提供唯一的序列版本。 CLI 自动生成并使用自己唯一的序列版本值。

-  **加密正在运行的 VM 的数据卷：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 加密正在运行的 VM 的数据卷：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>使用 Azure PowerShell 在新添加的磁盘上启用加密
 当使用 PowerShell 加密适用于 Linux 的新磁盘时，需要指定新的序列版本。 序列版本必须唯一。 以下脚本生成序列版本的 GUID。 在加密磁盘之前，创建[快照](snapshot-copy-managed-disk.md)和/或使用 [Azure 备份](../../backup/backup-azure-vms-encryption.md)备份 VM。 已在 PowerShell 脚本中指定 -skipVmBackup 参数以加密新添加的数据磁盘。
 

-  **加密正在运行的 VM 的数据卷：** 以下脚本初始化变量并运行 Set-AzVMDiskEncryptionExtension cmdlet。 先决条件是事先创建资源组、VM 和密钥保管库。 请将 MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault 替换为自己的值。 -VolumeType 参数可接受的值为 All、OS 和 Data。 如果 VM 先前使用卷类型“OS”或“All”进行加密，则应将 -VolumeType 参数更改为“All”，以便包含 OS 和新数据磁盘。

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **使用 KEK 加密正在运行的 VM 的数据卷：** -VolumeType 参数可接受的值为 All、OS 和 Data。 如果 VM 先前使用卷类型“OS”或“All”进行加密，则应将 -VolumeType 参数更改为 All，以便包含 OS 和新数据磁盘。

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>为 Linux VM 禁用加密
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>不支持的方案

Azure 磁盘加密不支持以下 Linux 方案、功能和技术：

- 加密通过经典 VM 创建方法创建的基本层 VM。
- 在已加密 OS 驱动器的情况下，在 Linux VM 的 OS 驱动器或数据驱动器上禁用加密。
- 为 Linux 虚拟机规模集加密 OS 驱动器。
- 加密 Linux VM 上的自定义映像。
- 与本地密钥管理系统集成。
- Azure 文件（共享文件系统）。
- 网络文件系统 (NFS)。
- 动态卷。
- 临时 OS 磁盘。
- 加密共享/分布式文件系统，包括但不限于：DFS、GFS、DRDB 和 CephFS。
- 将加密的 VM 移到其他订阅或区域。
- 创建已加密 VM 的映像或快照，并使用它来部署其他 VM。
- 内核故障转储 (kdump)。
- Oracle ACFS（ASM 群集文件系统）。
- Lsv2 系列 VM 的 NVMe 磁盘（请参阅：[Lsv2 系列](../lsv2-series.md)）。
- 具有“嵌套装入点”的 VM，即一个路径中有多个装入点（例如“/1stmountpoint/data/2stmountpoint”）。
- 包含数据驱动器的 VM 装载在 OS 文件夹之上。
- 一个已使用数据磁盘在其上扩展了根（OS 磁盘）逻辑卷的 VM。
- 具有写入加速器磁盘的 M 系列 VM。
- 将 ADE 应用到一个 VM，此 VM 使用[服务器端加密和客户管理的密钥](../disk-encryption.md) (SSE + CMK) 加密磁盘。 将 SSE+CMK 应用于使用 ADE 加密的 VM 上的数据磁盘，这种方案也不受支持。
- 将使用 ADE 加密的 VM，或者曾经使用 ADE 加密的 VM 迁移到[使用客户管理的密钥的服务器端加密](../disk-encryption.md)。
- 加密故障转移群集中的 VM。
- [Azure 超级磁盘](../disks-enable-ultra-ssd.md)的加密。

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密概述](disk-encryption-overview.md)
- [Azure 磁盘加密示例脚本](disk-encryption-sample-scripts.md)
- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)
