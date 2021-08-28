---
title: 安全 Azure AD 域服务 | Microsoft Docs
description: 了解如何为 Azure Active Directory 域服务托管域禁用弱密码、旧协议和 NTLM 密码哈希同步。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/21/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe8f41c2ecf92034f81f2332aabee5a55df66a92
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439247"
---
# <a name="harden-an-azure-active-directory-domain-services-managed-domain"></a>强化 Azure Active Directory 域服务托管域

默认情况下，Azure Active Directory 域服务 (Azure AD DS) 允许使用 NTLM v1 和 TLS v1 等密码。 某些旧版应用程序可能需要这些密码，但这些密码视为弱密码，如果不需要，可以将其禁用。 如果使用 Azure AD Connect 进行本地混合连接，则还可以禁用 NTLM 密码哈希的同步。

本文说明如何使用设置来强化托管域，例如： 

- 禁用 NTLM v1 和 TLS v1 密码
- 禁用 NTLM 密码哈希同步
- 禁用通过 RC4 加密更改密码的功能
- 启用 Kerberos 保护

## <a name="prerequisites"></a>先决条件

若要完成本文，需准备好以下资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。

## <a name="use-security-settings-to-harden-your-domain"></a>使用安全设置强化你的域

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure AD 域服务”。
1. 选择你的托管域，例如 *aaddscontoso.com*。
1. 在左侧，选择“安全设置”。
1. 对以下设置单击“启用”或“禁用” ：
   - **仅限 TLS 1.2 模式**
   - **NTLM 身份验证**
   - 从本地进行密码同步
   - **从本地进行 NTLM 密码同步**
   - RC4 加密
   - **Kerberos 保护**

   ![禁用弱密码和 NTLM 密码哈希同步的安全设置的屏幕截图](media/secure-your-domain/security-settings.png)

## <a name="assign-azure-policy-compliance-for-tls-12-usage"></a>针对 TLS 1.2 的使用分配 Azure Policy 合规性

除了“安全性设置”外，Microsoft Azure Policy 还有一项“合规性”设置，可强制使用 TLS 1.2。 策略在分配之前不会产生任何影响。 策略在分配后显示在“合规性”中：

- 如果分配为“审核”，则合规性会报告 Azure AD DS 实例是否合规。
- 如果分配为“拒绝”，则合规性会阻止在不需要 TLS 1.2 的情况下创建 Azure AD DS 实例，并阻止在需要 TLS 1.2 之前对 Azure AD DS 实例进行任何更新。

![合规性设置的屏幕截图](media/secure-your-domain/policy-tls.png)

## <a name="audit-ntlm-failures"></a>审核 NTLM 失败

虽然禁用 NTLM 密码同步会提高安全性，但是许多应用程序和服务并未设计为在没有它的情况下正常工作。 例如，通过其 IP 地址连接到任何资源（如 DNS 服务器管理或 RDP）会失败并显示“拒绝访问”。 如果禁用 NTLM 密码同步后应用程序或服务未按预期工作，则可通过对“登录/注销” > “审核登录”事件类别启用安全审核来检查是否存在 NTLM 身份验证失败的情况，其中 NTLM 在事件详细信息中指定为“身份验证包”。 有关详细信息，请参阅[为 Azure Active Directory 域服务启用安全审核](security-audit-events.md)。

## <a name="use-powershell-to-harden-your-domain"></a>使用 PowerShell 强化你的域

如果需要，请[安装并配置 Azure PowerShell](/powershell/azure/install-az-ps)。 确保使用 [Connect-AzAccount][Connect-AzAccount] cmdlet 登录到 Azure 订阅。 

此外，如果需要，请[安装并配置 Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。 确保使用 [Connect-AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户。

若要禁用弱密码套件和 NTLM 凭据哈希同步，请登录 Azure 帐户，然后使用 [Get-AzResource][Get-AzResource] cmdlet 获取 Azure AD DS 资源：

> [!TIP]
> 如果使用 [Get-AzResource][Get-AzResource] 命令时收到错误，指示 Microsoft.AAD/DomainServices 资源不存在，请[提升你的访问权限以便能够管理所有 Azure 订阅和管理组][global-admin]。

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

接下来，定义 DomainSecuritySettings 以配置以下安全性选项：

1. 禁用 NTLM v1 支持。
2. 禁止从本地 AD 进行 NTLM 密码哈希同步。
3. 禁用 TLS v1。

> [!IMPORTANT]
> 如果在 Azure AD DS 托管域中禁用了 NTLM 密码哈希同步，则用户和服务帐户将无法执行 LDAP 简单绑定。 如果需要执行 LDAP 简单绑定，请不要在下面的命令中设置 "SyncNtlmPasswords"="Disabled"; 安全配置选项。

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled";"KerberosRc4Encryption"="Disabled";"KerberosArmoring"="Disabled"}}
```

最后，使用 [Set-AzResource][Set-AzResource] cmdlet 将定义的安全设置应用于托管域。 指定第一步中的 Azure AD DS 资源和上一步中的安全设置。

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -ApiVersion “2021-03-01” -Verbose -Force
```

将安全设置应用到托管域需要一些时间。

> [!IMPORTANT]
> 禁用 NTLM 之后，在 Azure AD Connect 中执行完整的密码哈希同步，以删除托管域中的所有密码哈希。 如果禁用 NTLM 但不强制执行密码哈希同步，则仅在下次更改密码时才会删除用户帐户的 NTLM 密码哈希。 如果用户在使用 NTLM 作为身份验证方法的系统上缓存了凭据，则此行为可以允许用户继续登录。
>
> NTLM 密码哈希与 Kerberos 密码哈希不同后，将无法回退到 NTLM。 如果 VM 已连接到托管域控制器，则缓存凭据也不再有效。  

## <a name="next-steps"></a>后续步骤

若要了解有关同步过程的详细信息，请参阅[如何在托管域中同步对象和凭据][synchronization]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
