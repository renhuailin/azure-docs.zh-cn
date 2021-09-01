---
title: 适用于 Linux 的 Azure 磁盘加密
description: 使用虚拟机扩展将适用于 Linux 的 Azure 磁盘加密部署到虚拟机。
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 2b589f24c5338dde503d21a71fcbb9863f8da5b4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734582"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>适用于 Linux 的 Azure 磁盘加密 (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>概述

Azure 磁盘加密利用 Linux 中的 dm-crypt 子系统在[选择 Azure Linux 发行版](../linux/disk-encryption-overview.md)上提供完整磁盘加密。  此解决方案与 Azure Key Vault 集成，用于管理磁盘加密密钥和机密。

## <a name="prerequisites"></a>先决条件

有关先决条件的完整列表，请参阅[适用于 Linux VM 的 Azure 磁盘加密](../linux/disk-encryption-overview.md)，特别是以下部分：

- [支持的 VM 和操作系统](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 要求](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [网络要求](../linux/disk-encryption-overview.md#networking-requirements)
- [加密密钥存储要求](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>扩展架构

Azure 磁盘加密 (ADE) 的扩展架构有两个版本：
- v1.1 - 建议使用的较新架构，它不使用 Azure Active Directory (AAD) 属性。
- v0.1 - 需要 Azure Active Directory (AAD) 属性的较旧架构。

若要选择目标架构，需要将 `typeHandlerVersion` 属性设置为要使用的架构版本。

### <a name="schema-v11-no-aad-recommended"></a>架构 v1.1：无 AAD（推荐）

建议使用 v1.1 架构，它不需要 Azure Active Directory (AAD) 属性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>架构 v0.1：使用 AAD

0.1 版架构需要 `AADClientID` 和 `AADClientSecret` 或 `AADClientCertificate`。

使用 `AADClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

使用 `AADClientCertificate`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>属性值

注意：所有属性值区分大小写。

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1.1、0.1 | int |
| （0.1 版架构）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID |
| （0.1 版架构）AADClientSecret | password | string |
| （0.1 版架构）AADClientCertificate | thumbprint | string |
| （可选）（0.1 版架构）密码 | password | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 字典 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string |
| （可选 - 默认 RSA-OAEP）KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| （可选）KeyEncryptionKeyURL | url | string |
| （可选）KekVaultResourceId | url | string |
| （可选）SequenceVersion | uniqueidentifier | string |
| VolumeType | OS, Data, All | string |

## <a name="template-deployment"></a>模板部署

有关基于架构 v1.1 的模板部署的示例，请参阅 Azure 快速入门模板 [encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-linux-vm-without-aad)。

有关基于架构 v0.1 的模板部署的示例，请参阅 Azure 快速入门模板 [encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-linux-vm)。

>[!WARNING]
> - 如果之前是使用 Azure 磁盘加密与 Azure AD 来加密 VM，则必须继续使用此选项来加密 VM。
> - 加密 Linux OS 卷时，应将 VM 视为不可用。 我们强烈建议在加密过程中避免 SSH 登录，以避免阻止加密过程中需要访问的任何打开文件的问题。 若要检查进度，请使用 [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet 或 [vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI 命令。 对于 30GB 操作系统卷，此过程可能需要几小时才能完成，还需要额外的时间来加密数据卷。 除非使用“encrypt format all”选项，否则数据卷加密时间将与数据卷的大小和数量成比例。
> - 在 Linux VM 上，仅支持对数据卷禁用加密。 如果 OS 卷已加密，则不支持对数据卷或 OS 卷禁用加密。

>[!NOTE]
> 此外，如果 `VolumeType` 参数设置为 All，则仅当数据磁盘正确装载时才会对其进行加密。

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关故障排除，请参阅 [Azure 磁盘加密故障排除指南](../linux/disk-encryption-troubleshooting.md)。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。

或者，你也可以提出 Azure 支持事件。 转到 [Azure 支持](https://azure.microsoft.com/support/options/)，然后选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤

* 有关 VM 扩展的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](features-linux.md)。
* 有关适用于 Linux 的 Azure 磁盘加密的详细信息，请参阅 [Linux 虚拟机](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)。
