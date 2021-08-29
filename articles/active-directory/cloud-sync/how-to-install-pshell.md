---
title: 使用命令行接口 (CLI) 和 PowerShell 安装 Azure AD Connect 云预配代理
description: 了解如何通过使用 PowerShell cmdlet 来安装 Azure AD Connect 云预配代理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c150f4bf18f9c4f5b9215122a45df1de32f48c32
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506234"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-by-using-a-cli-and-powershell"></a>通过使用 CLI 和 PowerShell 来安装 Azure AD Connect 预配代理
本文介绍如何通过使用 PowerShell cmdlet 安装 Azure Active Directory (Azure AD) Connect 预配代理。
 
>[!NOTE]
>本文介绍如何通过使用命令行界面 (CLI) 来安装预配代理。 若要了解如何使用向导来安装 Azure AD Connect 预配代理，请参阅[安装 Azure AD Connect 预配代理](how-to-install.md)。

## <a name="prerequisite"></a>先决条件

Windows 服务器必须启用 TLS 1.2，然后你才能使用 PowerShell cmdlet 来安装 Azure AD Connect 预配代理。 若要启用 TLS 1.2，请按照 [Azure AD Connect 云同步的先决条件](how-to-prerequisites.md#tls-requirements)中的步骤操作。

>[!IMPORTANT]
>以下安装说明假定所有[先决条件](how-to-prerequisites.md)均已具备。

## <a name="install-the-azure-ad-connect-provisioning-agent-by-using-powershell-cmdlets"></a>使用 PowerShell cmdlet 安装 Azure AD Connect 预配代理 

 1. 登录到 Azure 门户，然后选择“Azure Active Directory”。
 1. 在左侧菜单中，选择“Azure AD Connect”。
 1. 选择“管理预配(预览版)” > “查看所有代理”。
 1. 从 Azure 门户下载 Azure AD Connect 预配代理。

    ![显示如何下载本地代理的屏幕截图。](media/how-to-install/install-9.png)</br>

 1. 为了进行这些说明，该代理已下载到 C:\ProvisioningSetup 文件夹。 
 1. 在静默模式下安装预配代理。

       ```
       $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
       $installerProcess.WaitForExit()  
       ```
 1. 导入预配代理 PS 模块。

       ```
       Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.PowerShell.dll" 
       ```
 1. 通过使用全局管理员凭据来连接到 Azure AD。 可以自定义本部分，以便从某个安全存储中提取密码。 

       ```
       $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 
    
       $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
       
       Connect-AADCloudSyncAzureAD -Credential $globalAdminCreds 
       ```
 1. 添加 gMSA 帐户，并提供域管理员的凭据，以创建默认的 gMSA 帐户。
 
       ```
       $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 
    
       Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
       ```
 1. 或者使用前一个 cmdlet 来提供预先创建的 gMSA 帐户。
 
       ```
       Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
       ```
 1. 添加域。

       ```
       $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
       ```
 1. 或者使用前一个 cmdlet 来配置首选域控制器。

       ```
       $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
       ```
 1. 重复前一步，以添加更多的域。 分别提供这些域的帐户名称和域名。
 
 1. 重启服务。
 
       ```
       Restart-Service -Name AADConnectProvisioningAgent  
       ```
 1. 转到 Azure 门户创建云同步配置。

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>预配代理 gMSA PowerShell cmdlet
现在已安装代理，可以将更多精细权限应用于 gMSA 了。 有关如何配置权限的信息和分步说明，请参阅 [Azure AD Connect 云预配代理 gMSA PowerShell cmdlet](how-to-gmsa-cmdlets.md)。

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [Azure AD Connect 云预配代理 gMSA PowerShell cmdlet](how-to-gmsa-cmdlets.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)