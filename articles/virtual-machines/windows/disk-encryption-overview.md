---
title: 为 Windows VM 启用 Azure 磁盘加密
description: 本文提供有关如何为 Windows VM 启用 Microsoft Azure 磁盘加密的说明。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: e283ff2de003146c8228d36843f00ca8e4faced9
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111748564"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>适用于 Windows VM 的 Azure 磁盘加密

Azure 磁盘加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 它使用 Windows 的 [BitLocker](https://en.wikipedia.org/wiki/BitLocker) 功能为 Azure 虚拟机 (VM) 的操作系统和数据磁盘提供卷加密，并与 [Azure 密钥保管库](../../key-vault/index.yml)集成，以帮助你控制和管理磁盘加密密钥和机密。

Azure 磁盘加密可在区域内复原，与虚拟机的方式相同。 有关详细信息，请参阅[支持可用性区域的 Azure 服务](../../availability-zones/az-region.md)。

如果使用 [Azure 安全中心](../../security-center/index.yml)，当 VM 未加密时，你会收到警报。 这些警报显示为“高严重性”，建议加密这些 VM。

![Azure 安全中心磁盘加密警报](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果之前是使用 Azure 磁盘加密与 Azure AD 来加密 VM，则必须继续使用此选项来加密 VM。 有关详细信息，请参阅[使用 Azure AD 进行的 Azure 磁盘加密（以前的版本）](disk-encryption-overview-aad.md)。 
> - 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外的许可或订阅成本。 必须具有有效的活动 Azure 订阅，才能在 Azure 的受支持区域中创建资源。

通过[使用 Azure CLI 创建和加密 Windows VM 快速入门](disk-encryption-cli-quickstart.md)或[使用 Azure PowerShell 创建和加密 Windows VM 快速入门](disk-encryption-powershell-quickstart.md)，只需几分钟即可了解适用于 Windows 的 Azure 磁盘加密的基本知识。

## <a name="supported-vms-and-operating-systems"></a>支持的 VM 和操作系统

### <a name="supported-vms"></a>支持的 VM

Windows VM 的大小有[多种](../sizes-general.md)。 Azure 磁盘加密在第 1 代和第 2 代 VM 上受支持。 Azure 磁盘加密还可用于使用高级存储的 VM。

Azure 磁盘加密在 [A 系列基本 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) 或内存小于 2 GB 的虚拟机上不可用。  Azure 磁盘加密对于没有临时磁盘（Dv4、Dsv4、Ev4 和 Esv4）的 VM 映像也不可用。  请参阅[无本地临时磁盘的 Azure VM 规格](../azure-vms-no-temp-disk.md)。  有关更多例外，请参阅 [Azure 磁盘加密：不支持的方案](disk-encryption-windows.md#unsupported-scenarios)。

### <a name="supported-operating-systems"></a>支持的操作系统

- Windows 客户端：Windows 8 和更高版本。
- Windows Server：Windows Server 2008 R2 和更高版本。  
 
> [!NOTE]
> Windows Server 2008 R2 要求安装 .NET Framework 4.5 以支持加密；请从 Windows 更新安装此组件，并安装适用于 Windows Server 2008 R2 基于 x64 的系统的 Microsoft .NET Framework 4.5.2 可选更新 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983))。  
>  
> Windows Server 2012 R2 Core 和 Windows Server 2016 Core 要求在 VM 安装 bdehdcfg 组件以支持加密。


## <a name="networking-requirements"></a>网络要求
若要启用 Azure 磁盘加密，VM 必须符合以下网络终结点配置要求：
  - 要获取用于连接密钥保管库的令牌，Windows VM 必须能够连接到 Azure Active Directory 终结点 \[login.microsoftonline.com\]。
  - 若要将加密密钥写入密钥保管库，Windows VM 必须能够连接到密钥保管库终结点。
  - Windows VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 到 Internet 的访问，可以解析上述 URI，并配置特定的规则以允许与这些 IP 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。    


## <a name="group-policy-requirements"></a>组策略要求

Azure 磁盘加密对 Windows VM 使用 BitLocker 外部密钥保护程序。 对于已加入域的 VM，请不要推送会强制执行 TPM 保护程序的任何组策略。 有关“在没有兼容 TPM 的情况下允许 BitLocker”的组策略信息，请参阅 [BitLocker 组策略参考](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

具有自定义组策略的已加入域虚拟机上的 BitLocker 策略必须包含以下设置：[配置 BitLocker 恢复信息的用户存储 -> 允许 256 位恢复密钥](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 如果 BitLocker 的自定义组策略设置不兼容，Azure 磁盘加密将会失败。 在没有正确策略设置的计算机上，应用新策略，强制更新新策略 (gpupdate.exe /force)，然后可能需要重启。

> [!WARNING]
> Azure 磁盘加密不存储恢复密钥。 如果启用了[交互式登录：计算机帐户锁定阈值](/windows/security/threat-protection/security-policy-settings/interactive-logon-machine-account-lockout-threshold)安全设置，则只能通过串行控制台提供恢复密钥来恢复计算机。 有关确保启用适当恢复策略的说明，请参阅 [Bitlocker 恢复指南计划](/windows/security/information-protection/bitlocker/bitlocker-recovery-guide-plan)。

如果域级组策略阻止了 BitLocker 使用的 AES-CBC 算法，Azure 磁盘加密将会失败。

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求  

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 密钥保管库和 VM 必须位于同一 Azure 区域和订阅中。

有关详细信息，请参阅[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。

## <a name="terminology"></a>术语
下表定义了 Azure 磁盘加密文档中使用的一些常用术语：

| 术语 | 定义 |
| --- | --- |
| Azure Key Vault | Key Vault 是基于联邦信息处理标准 (FIPS) 验证的硬件安全模块。 这些标准有助于保护加密密钥和敏感机密。 有关详细信息，请参阅 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)文档和[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 经过了优化，可从命令行管理 Azure 资源。|
| BitLocker |[BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) 是一种行业认可的 Windows 卷加密技术，用于在 Windows VM 上启用磁盘加密。 |
| 密钥加密密钥 (KEK) | 可用于保护或包装机密的非对称密钥 (RSA 2048)。 可提供硬件安全模块 (HSM) 保护的密钥或软件保护的密钥。 有关详细信息，请参阅 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)文档和[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。 |
| PowerShell cmdlet | 有关详细信息，请参阅 [Azure PowerShell cmdlet](/powershell/azure/)。 |

## <a name="next-steps"></a>后续步骤

- [快速入门 - 使用 Azure CLI 创建和加密 Windows VM](disk-encryption-cli-quickstart.md)
- [快速入门 - 使用 Azure PowerShell 创建和加密 Windows VM](disk-encryption-powershell-quickstart.md)
- [Windows VM 上的 Azure 磁盘加密方案](disk-encryption-windows.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started) 
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)
