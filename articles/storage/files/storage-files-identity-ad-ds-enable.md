---
title: 启用对 Azure 文件共享的 AD DS 身份验证
description: 了解如何通过 SMB 为 Azure 文件共享启用 Active Directory 域服务身份验证。 然后，已加入域的 Windows 虚拟机就可使用 AD DS 凭据访问 Azure 文件共享了。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 10/05/2021
ms.author: rogarana
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7a7082005cc2a8154670abfae120d94015b2135c
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545805"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>第一部分：为 Azure 文件共享启用 AD DS 身份验证 

在启用 Active Directory 域服务 (AD DS) 身份验证之前，请确保已阅读[概述文章](storage-files-identity-auth-active-directory-enable.md)，并了解支持的场景和要求。

本文介绍了在存储帐户上启用 Active Directory 域服务 (AD DS) 身份验证所需的过程。 启用此功能后，必须配置存储帐户和 AD DS，才能使用 AD DS 凭据对 Azure 文件共享进行身份验证。 若要通过 SMB 为 Azure 文件共享启用 AD DS 身份验证，需要向 AD DS 注册存储帐户，然后在存储帐户上设置所需的域属性。

要向 AD DS 注册存储帐户，请在 AD DS 中创建一个帐户来代表它。 可以将此过程视为类似于在 AD DS 中创建一个表示本地 Windows 文件服务器的帐户。 在存储帐户上启用此功能后，它将适用于帐户中的所有新的和现有文件共享。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>选项 1（推荐）：使用 AzFilesHybrid PowerShell 模块

AzFilesHybrid PowerShell 模块中的 cmdlet 为你进行必要的修改并启用此功能。 由于 cmdlet 的某些部分与你的本地 AD DS 交互，我们将介绍 cmdlet 的作用，方便你确定这些更改是否符合你的合规性与安全性策略，并确保你具有执行 cmdlet 的适当权限。 虽然我们建议使用 AzFilesHybrid 模块，但如果你无法执行此操作，我们还提供了以下步骤，可供你手动执行。

### <a name="download-azfileshybrid-module"></a>下载 AzFilesHybrid 模块

- 如果未安装 [.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework/net472)，请立即安装。 该模块需要它才能成功导入。
- [下载并解压缩 AzFilesHybrid 模块（GA 模块：v0.2.0+）](https://github.com/Azure-Samples/azure-files-samples/releases)请注意，v0.2.2 或更高版本支持 AES 256 Kerberos 加密。 如果你已在低于 v0.2.2 的 AzFilesHybrid 版本中启用此功能，并且需要更新以支持 AES 256 Kerberos 加密，请参阅[这篇文章](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)。
- 使用有权在目标 AD 中创建服务登录帐户或计算机帐户的 AD DS 凭据在加入本地 AD DS 域的设备中安装和执行此模块。
-  使用同步到 Azure AD 的本地 AD DS 凭据运行脚本。 本地 AD DS 凭据必须在存储帐户上具有“所有者”或“参与者”Azure 角色。

### <a name="run-join-azstorageaccountforauth"></a>运行 Join-AzStorageAccountForAuth

`Join-AzStorageAccountForAuth` cmdlet 代表指定的存储帐户执行与脱机域加入等效的操作。 此脚本使用 cmdlet 在 AD 域中创建一个[计算机帐户](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)。 如果你出于任何原因而不能使用计算机帐户，可以修改脚本，创建一个[服务登录帐户](/windows/win32/ad/about-service-logon-accounts)。 如果你选择手动运行命令，应选择最适合你的环境的帐户。

由 cmdlet 创建的 AD DS 帐户表示存储帐户。 如果 AD DS 帐户是在强制执行密码过期的组织单位 (OU) 下创建的，则必须在密码过期前更新密码。 如果在该日期之前未能更新帐户密码，将导致访问 Azure 文件共享时的身份验证失败。 若要了解如何更新密码，请参阅[更新 AD DS 帐户密码](storage-files-identity-ad-ds-update-password.md)。

在 PowerShell 中执行占位符值之前，请在参数中将其替换为你自己的值。
> [!IMPORTANT]
> 域加入 cmdlet 将创建一个 AD 帐户来表示 AD 中的存储帐户（文件共享）。 你可以选择注册为计算机帐户或服务登录帐户，有关详细信息，请参阅[常见问题解答](./storage-files-faq.md#security-authentication-and-access-control)。 对于计算机帐户，AD 中的默认密码过期时间设置为 30 天。 同样，服务登录帐户可能在 AD 域或组织单位 (OU) 上设置了默认密码过期时间。
> 对于这两种帐户类型，建议你检查 AD 环境中配置的密码过期时间，并计划在密码过期之前[更新 AD 帐户的存储帐户标识的密码](storage-files-identity-ad-ds-update-password.md)。 可以考虑[在 AD 中创建新的 AD 组织单位 (OU)](/powershell/module/activedirectory/new-adorganizationalunit)，并相应地在[计算机帐户](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11))或服务登录帐户上禁用密码过期策略。 

```PowerShell
# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

# Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
# If you are logging into an Azure environment other than Public (ex. AzureUSGovernment) you will need to specify that.
# See https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-ps
# for more information.
Connect-AzAccount

# Define parameters, $StorageAccountName currently has a maximum limit of 15 characters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"
$DomainAccountType = "<ComputerAccount|ServiceLogonAccount>" # Default is set as ComputerAccount
# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory.
$OuDistinguishedName = "<ou-distinguishedname-here>"
# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption.
$EncryptionType = "<AES256|RC4|AES256,RC4>"

# Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType $DomainAccountType `
        -OrganizationalUnitDistinguishedName $OuDistinguishedName `
        -EncryptionType $EncryptionType

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-two-manually-perform-the-enablement-actions"></a>选项 2：手动执行启用操作

如果你在前面已成功执行 `Join-AzStorageAccountForAuth` 脚本，请转到[确认已启用此功能](#confirm-the-feature-is-enabled)部分。 不需要执行以下手动步骤。

### <a name="check-the-environment"></a>检查环境

首先，必须检查你的环境状态。 具体来说，必须检查是否安装了 [Active Directory PowerShell](/powershell/module/activedirectory/)，以及是否正在以管理员权限执行 shell。 然后查看是否已安装 [Az.Storage 2.0 模块（或更新版本）](https://www.powershellgallery.com/packages/Az.Storage/2.0.0)，如果未安装，请立即安装。 完成这些检查后，请检查 AD DS，查看是否有一个已使用 SPN/UPN 创建为“cifs/your-storage-account-name-here.file.core.windows.net”的[计算机帐户](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认）或[服务登录帐户](/windows/win32/ad/about-service-logon-accounts)。 如果帐户不存在，请按照下一节的说明创建一个帐户。

### <a name="create-an-identity-representing-the-storage-account-in-your-ad-manually"></a>在 AD 中手动创建可表示存储帐户的标识

若要手动创建此帐户，请为存储帐户创建一个新的 Kerberos 密钥。 然后，在下面的 PowerShell cmdlet 中，将该 Kerberos 密钥用作你的帐户密码。 此密钥仅在安装过程中使用，不能用于对存储帐户的任何控制或数据平面操作。 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

获得该密钥后，请在 OU 下创建服务或计算机帐户。 使用以下规范（记得将示例文本替换为你的存储帐户名称）：

SPN：“cifs/your-storage-account-name-here.file.core.windows.net”密码：存储帐户的 Kerberos 密钥。

如果你的 OU 强制执行密码过期，则必须在密码过期前更新密码，以防在访问 Azure 文件共享时身份验证失败。 有关详细信息，请参阅[更新 AD 中的存储帐户标识密码](storage-files-identity-ad-ds-update-password.md)。

保留新创建标识的 SID，你在下一步中将用到它。 你创建的表示存储帐户的标识无需同步到 Azure AD。

#### <a name="optional-enable-aes256-encryption"></a>（可选）启用 AES256 加密

要启用 AES 256 加密，请按照本部分中的步骤操作。 如果打算使用 RC4，则可以跳过本部分。

表示存储帐户的域对象必须满足以下要求：
- 存储帐户名不得超过 15 个字符。
- 必须将域对象创建为本地 AD 域中的计算机对象。
- 除尾随符“$”外，存储帐户名必须与计算机对象的 SamAccountName 相同。

如果域对象不满足这些要求，请将其删除并创建符合要求的全新域对象。

将 `<domain-object-identity>` 和 `<domain-name>` 替换为你的值，然后使用以下命令配置 AES256 支持： 

```powershell
Set-ADComputer -Identity <domain-object-identity> -Server <domain-name> -KerberosEncryptionType "AES256"
```

运行该命令后，请将以下脚本中 `<domain-object-identity>` 替换为你的值，然后运行脚本以刷新域对象密码：

```powershell
$KeyName = "kerb1" # Could be either the first or second kerberos key, this script assumes we're refreshing the first
$KerbKeys = New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName $KeyName
$KerbKey = $KerbKeys | Where-Object {$_.KeyName -eq $KeyName} | Select-Object -ExpandProperty Value
$NewPassword = Convert-ToSecureString -String $KerbKey -AsPlainText -Force

Set-ADAccountPassword -Identity <domain-object-identity> -Reset -NewPassword $NewPassword
```

### <a name="enable-the-feature-on-your-storage-account"></a>在存储帐户上启用此功能

现在可以在存储帐户上启用此功能。 在以下命令中为域属性提供一些配置详细信息，然后运行此命令。 以下命令中所需的存储帐户 SID 是你在[上一节](#create-an-identity-representing-the-storage-account-in-your-ad-manually)的 AD DS 中创建的标识的 SID。

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>调试

可以运行 Debug-AzStorageAccountAuth cmdlet，使用已登录的 AD 用户对 AD 配置执行一组基本检查。 AzFilesHybrid v0.1.2+ 版本支持此 cmdlet。 有关此 cmdlet 中执行的检查的详细信息，请参阅 Windows 故障排除指南中的[无法使用 AD 凭据装载 Azure 文件存储](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)。

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>确认是否已启用此功能

通过以下脚本，可以确认是否在存储帐户上启用了此功能：

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

如果成功，输出应如下所示：

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>后续步骤

现在你已成功在存储帐户上启用了此功能。 若要使用此功能，必须分配共享级权限。 请继续阅读下一节。

[第二部分：向标识分配共享级权限](storage-files-identity-ad-ds-assign-permissions.md)
