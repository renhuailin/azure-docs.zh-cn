---
title: 使用 Azure AD 应用进行 Azure 磁盘加密的先决条件（早期版本）
description: 本文提供了使用 Azure AD 进行 Azure 磁盘加密的其他要求和先决条件，补充说明了适用于 Linux VM 的 Azure 磁盘加密。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 742e96d07a059c933e6293912c364a230b0ddb27
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695469"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>使用 Azure Active Directory (AD) 进行的 Azure 磁盘加密（以前的版本）

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

新版本的 Azure 磁盘加密无需提供 Azure Active Directory (Azure AD) 应用程序参数即可启用 VM 磁盘加密。 使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。 所有新 VM 都必须使用新版本在没有 Azure AD 应用程序参数的情况下进行加密。 有关如何使用新版本来启用 VM 磁盘加密的说明，请参阅[适用于 Linux VM 的 Azure 磁盘加密](disk-encryption-overview.md)。 已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。

本文提供了使用 Azure AD 进行 Azure 磁盘加密的其他要求和先决条件（旧版），补充说明了[适用于 Linux VM 的 Azure 磁盘加密](disk-encryption-overview.md)。

这些部分中的信息保持不变：

- [支持的 VM 和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 要求](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>网络和组策略

若要使用旧的 AAD 参数语法来启用 Azure 磁盘加密功能，基础结构即服务 (IaaS) VM 必须符合以下网络终结点配置要求： 
  - 若要获取用于连接到密钥保管库的令牌，IaaS VM 必须能够连接到 Azure AD 终结点 login.microsoftonline.com\[\]。
  - IaaS VM 必须能够连接到 Key Vault 终结点，以将加密密钥写入 Key Vault。
  - IaaS VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 到 Internet 的访问，可以解析上述 URI，并配置特定的规则，允许与这些 IP 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。
  - 在 Windows 中，如果显式禁用了 TLS 1.0 且 .NET 版本未更新到 4.6 或更高版本，则下面的注册表更改将使 Azure 磁盘加密能够选择较新的 TLS 版本：

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>组策略
 - Azure 磁盘加密解决方案对 Windows IaaS VM 使用 BitLocker 外部密钥保护程序。 对于已加入域的 VM，请不要推送会强制执行 TPM 保护程序的任何组策略。 有关“在没有兼容 TPM 的情况下允许 BitLocker”选项的组策略信息，请参阅 [BitLocker 组策略参考](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

- 具有自定义组策略的已加入域虚拟机上的 BitLocker 策略必须包含以下设置：[配置 BitLocker 恢复信息的用户存储 -> 允许 256 位恢复密钥](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 如果 BitLocker 的自定义组策略设置不兼容，Azure 磁盘加密会失败。 在没有正确的策略设置的计算机上应用新策略，强制更新新策略 (gpupdate.exe /force)，然后根据需要重启。 

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求 

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 密钥保管库和 VM 必须位于同一 Azure 区域和订阅中。

有关详细信息，请参阅[使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（旧版）](disk-encryption-key-vault-aad.md)。
 
## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](disk-encryption-key-vault-aad.md)
- [在 Linux VM 上使用 Azure AD 启用 Azure 磁盘加密（以前版本）](disk-encryption-linux-aad.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
