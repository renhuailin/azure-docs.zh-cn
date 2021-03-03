---
title: Azure AD Connect 云预配代理 gMSA PowerShell cmdlet
description: 了解如何使用 Azure AD Connect 云预配代理 gMSA powershell cmdlet。
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
ms.openlocfilehash: 411a8e46151a762bcd270fb676f78a91f760ac4f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653790"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect 云预配代理 gMSA PowerShell cmdlet

本文档的目的是介绍 Azure AD Connect 云预配代理 gMSA PowerShell cmdlet。 这些 cmdlet 可让你更细致地了解 (gmsa) 上应用于服务帐户的权限。 默认情况下，Azure AD Connect 云同步在默认 gmsa 或自定义 gmsa 上应用与 Azure AD Connect 类似的所有权限。 

本文档将介绍以下 cmdlet：  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>如何使用 cmdlet：  

若要使用这些 cmdlet，需要满足以下先决条件。

1. 安装预配代理。 
2. 将预配代理 PS 模块导入 PowerShell 会话。 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. 删除现有权限。  若要删除对服务帐户的所有现有权限，自使用除外： `Set-AADCloudSyncRestrictedPermission` 。  

    此 cmdlet 需要一个 `Credential` 可传递的参数，如果不使用该参数，则会提示。

    创建变量使用  

   `$credential = Get-Credential` 

   这会提示用户输入用户名和密码。 凭据必须是安装代理的域的最小域管理员 () ，也可以是企业管理员。 

4.  然后，可以调用 cmdlet 来删除额外的权限： 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. 或者只需调用 

   `Set-AADCloudSyncRestrictedPermissions` 这会提示输入凭据。 

 6.  添加特定权限类型。  添加的权限与 Azure AD Connect 相同。  有关设置权限的示例，请参阅下面的 [使用 AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) 。

## <a name="using-set-aadcloudsyncpermissions"></a>使用 Set-AADCloudSyncPermissions 
`Set-AADCloudSyncPermissions` 支持以下与 Azure AD Connect 使用的权限相同的权限类型。 支持下列权限类型： 

|权限类型|说明|
|-----|-----|
|BasicRead| 有关 Azure AD Connect，请参阅 [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) 权限|
|PasswordHashSync|有关 Azure AD Connect，请参阅 [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) 权限|
|PasswordWriteBack|有关 Azure AD Connect，请参阅 [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) 权限|
|HybridExchangePermissions|有关 Azure AD Connect，请参阅 [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) 权限| 
|ExchangeMailPublicFolderPermissions| 有关 Azure AD Connect，请参阅 [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) 权限| 
|CloudHR| 对 "此对象和所有后代对象" 上的 "子代用户对象" 和 "创建/删除用户对象" 应用 "完全控制"| 
|全部|添加上述所有权限。| 



可以通过以下两种方式之一使用 AADCloudSyncPermissions：
- [向所有配置的域授予特定权限](#grant-a-certain-permission-to-all-configured-domains) 
- [向特定域授予特定权限](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>向所有配置的域授予特定权限 
将某些权限授予所有配置的域将需要使用企业管理员帐户。


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>向特定域授予特定权限 
向特定域授予某些权限将需要至少使用你尝试添加的域的域管理员帐户。


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

注意：为1。 凭据必须是最低企业管理员。 

对于“2. 凭据可以是域管理员或企业管理员。 

  

