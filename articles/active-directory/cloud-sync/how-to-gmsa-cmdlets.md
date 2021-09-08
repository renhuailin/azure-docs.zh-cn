---
title: Azure AD Connect 云预配代理 gMSA PowerShell cmdlet
description: 了解如何使用 Azure AD Connect 云预配代理 gMSA PowerShell cmdlet。
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
ms.openlocfilehash: 5fc33aaa51a687cc6f9df8aea494d5790dad22a2
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123306150"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect 云预配代理 gMSA PowerShell cmdlet

本本档旨在介绍 Azure AD Connect 云预配代理 gMSA PowerShell cmdlet。 利用这些 cmdlet，可以更细致地了解应用于服务帐户 (gMSA) 的权限。 在默认情况下，Azure AD Connect 云同步会在默认 gMSA 或自定义 gMSA 上应用与 Azure AD Connect 类似的所有权限。

本文档将介绍以下 cmdlet：

`Set-AADCloudSyncRestrictedPermissions`

`Set-AADCloudSyncPermissions`

## <a name="how-to-use-the-cmdlets"></a>如何使用 cmdlet：

若要使用这些 cmdlet，需要满足以下先决条件。

1. 安装预配代理。

2. 将预配代理 PS 模块导入到 PowerShell 会话中。

   ```powershell
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
   ```

3. 删除现有的权限。  若要删除服务帐户上所有的现有权限（SELF 除外），请使用：`Set-AADCloudSyncRestrictedPermission`。

   此 cmdlet 需要一个可以传递的名为 `Credential` 的参数，否则如果在调用时没有该参数，将会出现提示。

   若要创建变量，请使用：

   `$credential = Get-Credential`

   这将会提示用户输入用户名和密码。 凭据至少必须是安装代理的域的域管理员，也可以是企业管理员。

4. 然后，可以调用该 cmdlet 来删除额外的权限：

   ```powershell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```

5. 您也可以直接调用：

   `Set-AADCloudSyncRestrictedPermissions`，它将会提示输入凭据。

6. 添加特定的权限类型。 添加的权限与 Azure AD Connect 相同。 有关设置权限的示例，请参阅下面的[使用 Set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions)。

## <a name="using-set-aadcloudsyncpermissions"></a>使用 Set-AADCloudSyncPermissions

`Set-AADCloudSyncPermissions` 支持以下与 Azure AD Connect 使用的权限完全相同的权限类型。 支持以下权限类型：

|权限类型|说明|
|-----|-----|
|BasicRead| 请参阅 Azure AD Connect 的 [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) 权限|
|PasswordHashSync|请参阅 Azure AD Connect 的 [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) 权限|
|PasswordWriteBack|请参阅 Azure AD Connect 的 [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) 权限|
|HybridExchangePermissions|请参阅 Azure AD Connect 的 [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) 权限|
|ExchangeMailPublicFolderPermissions| 请参阅 Azure AD Connect 的 [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders) 权限|
|CloudHR| 将“创建/删除用户对象”应用于“此对象和所有后代对象”|
|全部|添加上述所有权限。|

可通过两种方式之一来使用 AADCloudSyncPermissions：
- [向所有已配置的域授予某些权限](#grant-a-certain-permission-to-all-configured-domains)
- [向某个特定域授予某些权限](#grant-a-certain-permission-to-a-specific-domain)

## <a name="grant-a-certain-permission-to-all-configured-domains"></a>向所有已配置的域授予某些权限

将某些权限授予所有已配置的域将会需要使用企业管理员帐户。

```powershell
Set-AADCloudSyncPermissions -PermissionType "Any mentioned above" -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>向某个特定域授予某些权限

向某个特定域授予某些权限将至少需要使用你尝试添加的域的域管理员帐户。

```powershell
Set-AADCloudSyncPermissions -PermissionType "Any mentioned above" -TargetDomain "FQDN of domain" (has to be already configured through wizard) -TargetDomainCredential $credential(same as above) 
```

请注意：对于 1。 该凭据至少必须是企业管理员。

对于“2. 该凭据可以是域管理员或企业管理员。
