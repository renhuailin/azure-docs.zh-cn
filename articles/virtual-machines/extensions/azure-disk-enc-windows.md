---
title: 适用于 Windows 的 Azure 磁盘加密
description: 使用虚拟机扩展将 Azure 磁盘加密部署到 Windows 虚拟机。
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.collection: windows
ms.date: 03/19/2020
ms.openlocfilehash: 62ad5ca5d3b150aef5a83eaa4d5231e7bb5a6a62
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110100173"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>适用于 Windows 的 Azure 磁盘加密 (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概述

Azure 磁盘加密利用 BitLocker 在运行 Windows 的 Azure 虚拟机上提供完全磁盘加密。  此解决方案与 Azure Key Vault 集成，以管理 Key Vault 订阅中的磁盘加密密钥和机密。

## <a name="prerequisites"></a>先决条件

有关先决条件的完整列表，请参阅[适用于 Windows VM 的 Azure 磁盘加密](../windows/disk-encryption-overview.md)，特别是以下部分：

- [支持的 VM 和操作系统](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [网络要求](../windows/disk-encryption-overview.md#networking-requirements)
- [组策略要求](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>扩展架构

Azure 磁盘加密 (ADE) 的扩展架构有两个版本：
- v2.2 - 建议使用的较新架构，它不使用 Azure Active Directory (AAD) 属性。
- v1.1 - 需要 Azure Active Directory (AAD) 属性的较旧架构。

若要选择目标架构，需要将 `typeHandlerVersion` 属性设置为要使用的架构版本。

### <a name="schema-v22-no-aad-recommended"></a>架构 v2.2：无 AAD（推荐）

v2.2 架构建议用于所有新 VM，并且不需要 Azure Active Directory 属性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[kekVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>架构 v1.1：使用 AAD

1\.1 架构需要 `aadClientID` 和 `aadClientSecret` 或 `AADClientCertificate`，建议不要用于新 VM。

使用 `aadClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryption | string |
| typeHandlerVersion | 2.2、1.1 | string |
| （1.1 架构）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID |
| （1.1 架构）AADClientSecret | password | string |
| （1.1 架构）AADClientCertificate | thumbprint | string |
| EncryptionOperation | EnableEncryption | string |
| （可选 - 默认 RSA-OAEP）KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| （可选）KeyEncryptionKeyURL | url | string |
| （可选）KekVaultResourceId | url | string |
| （可选）SequenceVersion | uniqueidentifier | string |
| VolumeType | OS, Data, All | string |

## <a name="template-deployment"></a>模板部署

有关基于架构 v2.2 的模板部署的示例，请参阅 Azure 快速入门模板 [encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-windows-vm-without-aad)。

有关基于架构 v1.1 的模板部署的示例，请参阅 Azure 快速入门模板 [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)。

>[!NOTE]
> 此外，如果 `VolumeType` 参数设置为 All，则仅当数据磁盘采用正确格式时才会对其进行加密。

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关故障排除，请参阅 [Azure 磁盘加密故障排除指南](../windows/disk-encryption-troubleshooting.md)。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。

或者，你也可以提出 Azure 支持事件。 转到 [Azure 支持](https://azure.microsoft.com/support/options/)，然后选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤

* 有关扩展的详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](features-windows.md)。
* 有关适用于 Windows 的 Azure 磁盘加密的详细信息，请参阅 [Windows 虚拟机](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)。
