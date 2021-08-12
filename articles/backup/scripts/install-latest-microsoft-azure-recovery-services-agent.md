---
title: 脚本示例 - 在本地 Windows 服务器上安装最新 MARS 代理
description: 了解如何使用脚本在本地 Windows 服务器上的存储帐户中安装最新 MARS 代理。
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 1080149d33eebed160449865d58f422e6eba36b1
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560811"
---
# <a name="powershell-script-to-install-the-latest-mars-agent-on-an-on-premises-windows-server"></a>用于在本地 Windows 服务器上安装最新 MARS 代理的 PowerShell 脚本

此脚本可帮助你在本地 Windows 服务器上安装最新 MARS 代理。

## <a name="sample-script"></a>示例脚本

```azurepowershell
<#

.SYNOPSIS
Sets MARS agent

.DESCRIPTION
Sets MARS agent

.ROLE
Administrators

#>
Set-StrictMode -Version 5.0
$ErrorActionPreference = "Stop"
Try {
    $agentPath = $env:TEMP + '\MARSAgentInstaller.exe'
    Invoke-WebRequest -Uri 'https://aka.ms/azurebackup_agent' -OutFile $agentPath
    & $agentPath /q | out-null

    $env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
    $azureBackupModuleName = 'MSOnlineBackup'
    $azureBackupModule = Get-Module -ListAvailable -Name $azureBackupModuleName
    if ($azureBackupModule) {
        $true
    }
    else {
        $false
    }
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="next-steps"></a>后续步骤

[详细了解](../backup-client-automation.md)如何使用 PowerShell 通过 MARS 代理部署和管理本地备份。