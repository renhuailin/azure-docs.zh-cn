---
title: 脚本示例 - 将本地 Windows Server 或客户端计算机注册到恢复服务保管库
description: 了解如何使用脚本将本地 Windows Server 或客户端计算机注册到恢复服务保管库。
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: a35d241ba86868c666916e284fb0700b59034eee
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560792"
---
# <a name="powershell-script-to-register-an-on-premises-windows-server-or-a-client-machine-with-recovery-services-vault"></a>用于将本地 Windows Server 或客户端计算机注册到恢复服务保管库的 PowerShell 脚本

此脚本可帮助你将本地 Windows Server 或客户端计算机注册到恢复服务保管库。 

## <a name="sample-script"></a>示例脚本

```azurepowershell
<#

.SYNOPSIS
Registers MARS agent

.DESCRIPTION
Registers MARS agent

.ROLE
Administrators

#>
param (
    [Parameter(Mandatory = $true)]
    [String]
    $vaultcredPath,
    [Parameter(Mandatory = $true)]
    [String]
    $passphrase
)
Set-StrictMode -Version 5.0
$env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
Import-Module MSOnlineBackup
$ErrorActionPreference = "Stop"
Try {
    $date = Get-Date
    Start-OBRegistration -VaultCredentials $vaultcredPath -Confirm:$false
    $securePassphrase = ConvertTo-SecureString -String $passphrase -AsPlainText -Force
    Set-OBMachineSetting -EncryptionPassphrase $securePassphrase -SecurityPIN " "
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="how-to-execute-the-script"></a>如何执行脚本

1. 使用所选名称和 .ps1 扩展名将上述脚本保存在计算机上。
1. 通过提供以下参数来执行脚本：
   - – vaultcredPath - 下载的保管库凭据文件的完整路径
   - – passphrase- 使用 [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-7.1&preserve-view=true) cmdlet 转换为安全字符串的纯文本字符串。

>[!Note]
>还需要提供从 Azure 门户生成的安全 PIN。 若要生成 PIN，请在“恢复服务保管库”边栏选项卡中导航到“设置” -> “属性” -> “安全 PIN”，然后选择“生成”   。

## <a name="next-steps"></a>后续步骤

[详细了解](../backup-client-automation.md)如何使用 PowerShell 通过 MARS 代理部署和管理本地备份。

