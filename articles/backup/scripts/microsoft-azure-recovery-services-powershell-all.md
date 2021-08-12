---
title: 脚本示例 - 为本地 Windows 服务器配置备份
description: 了解如何使用脚本为本地 Windows 服务器配置备份。
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 7ddf8fcca6761821f586030147276eefd364fc48
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560793"
---
# <a name="powershell-script-to-configure-backup-for-on-premises-windows-server"></a>用于为本地 Windows 服务器配置备份的 PowerShell 脚本

此脚本可用于为本地 Windows 服务器配置备份，从创建保管库到配置 MARS 代理和策略，均可为你提供帮助。

## <a name="sample"></a>示例

```azurepowershell
# Create Recovery Services Vault (RSV)
Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
New-AzResourceGroup –Name "test-rg" –Location "WestUS"
New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
$Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant

Get-AzRecoveryServicesVault

# Installing the Azure Backup agent
$MarsAURL = 'https://aka.ms/Azurebackup_Agent'
$WC = New-Object System.Net.WebClient
$WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
C:\Downloads\MARSAgentInstaller.EXE /q

MARSAgentInstaller.exe /q # Please note the commandline install options available here: https://docs.microsoft.com/en-us/azure/backup/backup-client-automation#installation-options

# Registering Windows Server or Windows client machine to a Recovery Services Vault
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false

# Networking settings
Set-OBMachineSetting -NoProxy
Set-OBMachineSetting -NoThrottle

# Encryption settings
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>" #NOTE: You must generate a security pin by selecting Generate, under Settings > Properties > Security PIN in the Recovery Services vault section of the Azure portal. 
# See: https://docs.microsoft.com/en-us/rest/api/backup/securitypins/get 
# See: https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/Add-AzureKeyVaultKey?view=azurermps-6.13.0 

# Back up files and folders
$NewPolicy = New-OBPolicy
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule

# Configuring a retention policy
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy

# Including and excluding files to be backed up
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions

# Applying the policy
Get-OBPolicy | Remove-OBPolicy
Set-OBPolicy -Policy $NewPolicy
Get-OBPolicy | Get-OBSchedule
Get-OBPolicy | Get-OBRetentionPolicy
Get-OBPolicy | Get-OBFileSpec

# Performing an on-demand backup
Get-OBPolicy | Start-OBBackup

# Remote management
Get-Service -Name WinRM
Enable-PSRemoting -Force
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force

```

## <a name="next-steps"></a>后续步骤

[详细了解](../backup-client-automation.md)如何使用 PowerShell 通过 MARS 代理部署和管理本地备份。