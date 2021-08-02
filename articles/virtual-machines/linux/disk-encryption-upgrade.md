---
title: 如何在磁盘上升级 Azure 磁盘加密
description: 本文介绍了如何在磁盘上升级 Azure 磁盘加密
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 05/27/2021
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 0cdc32e303de7d9a26a99d1f08188dc54539930b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758125"
---
# <a name="upgrading-the-azure-disk-encryption-version"></a>升级 Azure 磁盘加密版本

第一版 Azure 磁盘加密 (ADE) 依赖 Azure Active Directory (AAD) 进行身份验证；当前版本并非如此。  我们强烈建议使用最新版本。

## <a name="determine-ade-version"></a>确定 ADE 版本

作用域中用于迁移的 ADE 版本为：
- Windows：1.1.*（VM 上的 ADE 必须升级到 2.2）
- Linux：0.1.*（VM 上的 ADE 必须升级到 1.2）

你可以通过 Azure CLI、Azure PowerShell 或 Azure 门户确定为 VM 加密的 ADE 版本。

# <a name="cli"></a>[CLI](#tab/CLI)

若要确定 ADE 版本，请运行 Azure CLI [az vm get-help](/cli/azure/vm#az_vm_get_instance_view) 命令。

```azurecli-interactive
az vm get-instance-view --resource-group  <ResourceGroupName> --name <VMName> 
```

找到输出中的 AzureDiskEncryption 扩展，从输出中的“TypeHandlerVersion”字段识别版本号。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

若要确定 ADE 版本，请运行 Azure PowerShell [Get-AzVM](/powershell/module/az.compute/get-azvm) 命令。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName <ResourceGroupName> -Name <VMName> -Status
```

找到输出中的 AzureDiskEncryption 扩展，从输出中的“TypeHandlerVersion”字段识别版本号。

# <a name="portal"></a>[门户](#tab/Portal)

在 Azure 门户中转到你 VM 的“扩展”边栏选项卡。

:::image type="content" source="../media/disk-encryption/ade-version-1.png" alt-text="查找 ADE 版本门户屏幕截图 1":::

选择适用于 Windows 的“AzureDiskEncryption”扩展或适用于 Linux 的“AzureDiskEncryptionForLinux”扩展，并在“版本”字段中找到版本号。

:::image type="content" source="../media/disk-encryption/ade-version-2.png" alt-text="查找 ADE 版本门户屏幕截图 2":::

---

## <a name="how-to-migrate"></a>如何迁移

从 Azure 磁盘加密（使用 AAD）到 Azure 磁盘加密（不使用 AAD）的迁移只能通过 Azure PowerShell 获取。 确保你已安装最新版本的 Azure PowerShell，且至少安装了 [Azure PowerShell Az module 5.9.0 版本](/powershell/azure/new-azureps-module-az)。

若要从 Azure 磁盘加密（使用 AAD）升级到 Azure 磁盘加密（不使用 AAD），请使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) PowerShell cmdlet。 

> [!WARNING]
> Set-AzVMDiskEncryptionExtension cmdlet 只能用于通过 Azure 磁盘加密（和 AAD）进行的加密的 VM。 如果尝试迁移未加密的 VM 或使用 Azure 磁盘加密（不使用 AAD）进行加密的 VM，将导致终端错误。

```azurepowershell-interactive
Set-AzVMDiskEncryptionExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Migrate
```

当 cmdlet 提示你进行确认时，请输入“Y”。  将更新 ADE 版本，并重新启动 VM。 输出将类似于以下内容：

```bash
> Set-AzVMDiskEncryptionExtension -ResourceGroupName myResourceGroup -VMName myVM -Migrate

Update AzureDiskEncryption version?
This cmdlet updates Azure Disk Encryption version to single pass (Azure Disk Encryption without AAD). This may reboot
the machine and takes 10-15 minutes to finish. Are you sure you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
Azure Disk Encryption Extension Public Settings
"KeyVaultResourceId": /subscriptions/ea500758-3163-4849-bd2c-3e50f06efa7a/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault
"SequenceVersion":
"MigrateFlag": Migrate
"KeyVaultURL": https://myKeyVault.vault.azure.net/
"AADClientID": d29edf8c-3fcb-42e7-8410-9e39fdf0dd70
"KeyEncryptionKeyURL":
"KekVaultResourceId":
"EncryptionOperation": EnableEncryption
"AADClientCertThumbprint":
"VolumeType":
"KeyEncryptionAlgorithm":

Running ADE extension (with AAD) for -Migrate..
ADE extension (with AAD) is now complete. Updating VM model..
Running ADE extension (without AAD) for -Migrate..
ADE extension (without AAD) is now complete. Clearing VM model..

RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


> [!IMPORTANT]
> 完成升级所需的时间至少为 10 - 15 分钟。 在升级过程中，请勿取消 cmdlet。 这样会让 VM 的运行状况处于风险之中。

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)
