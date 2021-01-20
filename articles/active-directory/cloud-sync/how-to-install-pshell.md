---
title: 使用 powershell 安装 Azure AD Connect 云预配代理
description: 了解如何使用 powershell cmdlet 安装 Azure AD Connect 云预配代理。
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
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613155"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>使用 powershell cmdlet 安装 Azure AD Connect 预配代理 
以下文档将指导你介绍如何使用 PowerShell cmdlet 安装 Azure AD Connect 预配代理。
 

## <a name="prerequisite"></a>先决条件： 


>[!IMPORTANT]
>以下安装说明假定已满足所有[先决条件](how-to-prerequisites.md)。
>
> Windows server 需要启用 TLS 1.2，然后才能使用 powershell cmdlet 安装 Azure AD Connect 预配代理。 若要启用 TLS 1.2，你可以使用 [此处](how-to-prerequisites.md#tls-requirements)所述的步骤。

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>使用 powershell cmdlet 安装 Azure AD Connect 预配代理 


 1. 登录到 Azure 门户，然后 **Azure Active Directory**"。
 2. 在左侧菜单中，选择 " **Azure AD Connect**"。
 3. 选择“管理预配(预览版)” > “查看所有代理”。
 4. 将 Azure AD Connect 预配代理从 Azure 门户下载到本地。  

   ![下载本地代理](media/how-to-install/install-9.png)</br>
 5. 对于这些说明，代理已下载到以下文件夹： "C:\ProvisioningSetup" 文件夹。 
 6. 在静默模式下安装 ProvisioningAgent

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. 导入预配代理 PS 模块 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. 使用全局管理员凭据连接到 AzureAD，你可以自定义此部分以从安全存储中获取密码。 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD-Credential $globalAdminCreds 

 9. 添加 gMSA 帐户，提供域管理员的凭据以创建默认的 gMSA 帐户 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. 或使用下面的 cmdlet 来提供预先创建的 gMSA 帐户 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. 添加域 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. 或使用上述 cmdlet 来配置首选域控制器 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. 重复上一步以添加更多域，请提供各自域的帐户名称和域名 
 14. 重新启动服务。 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  请通过 azure 门户创建云同步配置。

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>GMSA PowerShell cmdlet 的预配代理
现在，你已安装了代理，你可以将更细化的权限应用到 gMSA。  有关配置权限的详细信息和分步说明，请参阅 [Azure AD Connect 云预配代理 GMSA PowerShell cmdlet](how-to-gmsa-cmdlets.md) 。

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [Azure AD Connect 云预配代理 gMSA PowerShell cmdlet](how-to-gmsa-cmdlets.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)