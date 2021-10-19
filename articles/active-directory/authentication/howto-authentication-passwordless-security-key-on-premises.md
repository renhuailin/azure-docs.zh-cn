---
title: 无密码安全密钥登录到本地资源 - Azure Active Directory
description: 了解如何使用 Azure Active Directory 启用无密码安全密钥登录到本地资源
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac53f16e80904216a4e19b03772dcd2820882f94
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129657885"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory"></a>使用 Azure Active Directory 启用无密码安全密钥登录到本地资源 

本文档重点介绍在包含 **加入 Azure AD** 和 **加入 Azure AD** 混合 Windows 10 设备的环境中对本地资源启用无密码身份验证。 此功能使用与 Microsoft 兼容的安全密钥提供了无缝单一登录 (SSO) 到本地资源。

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>使用 FIDO2 密钥通过 SSO 登录到本地资源

Azure Active Directory (AD) 可以为一个或多个 Active Directory 域颁发 Kerberos 票证授予票证 (TGT)。 此功能允许用户使用新式凭据（如 FIDO2 安全密钥）登录 Windows，并访问传统的基于 Active Directory 的资源。 Kerberos 服务票证和授权将继续由本地 Active Directory 域控制器控制。

Azure AD Kerberos 服务器对象在本地 Active Directory 中创建，然后安全地发布到 Azure Active Directory。 该对象不与任何物理服务器关联。 它只是 Azure Active Directory 可以用来为你的 Active Directory 域生成 Kerberos TGT 的一种资源。

![从 Azure AD 和 AD DS 获取票证授予票证 (TGT)](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. 用户使用 FIDO2 安全密钥登录到其 Windows 10 设备，并向 Azure AD 进行身份验证。
1. Azure AD 在目录中查找与用户的本地 AD 域匹配的 Kerberos 服务器密钥。
   1. Azure AD 为用户的本地 AD 域生成 Kerberos TGT。 TGT 仅包括用户的 SID。 TGT 中未包含任何授权数据。
1. TGT 连同其 Azure AD 主刷新令牌 (PRT) 一起返回给客户端。
1. 客户端计算机与本地 AD 域控制器联系，并为完全形成的 TGT 的部分 TGT 进行交易。
1. 客户端计算机现在具有 Azure AD PRT 和完整 Active Directory TGT，并可同时访问云和本地资源。

## <a name="requirements"></a>要求

在完成本文中的步骤之前，组织必须完成[为 Windows 10 设备启用无密码安全密钥签名](howto-authentication-passwordless-security-key.md)的步骤。

组织还必须满足以下软件要求。

- 设备必须运行 Windows 10 版本 2004 或更高版本。
- [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 的版本为必须为 1.4.32.0 或更高版本。
  - 有关可用的 Azure AD 混合身份验证选项的详细信息，请参阅[为 Azure Active Directory 混合身份解决方案选择正确的身份验证方法](../hybrid/choose-ad-authn.md)以及[选择要用于 Azure AD Connect 的安装类型](../hybrid/how-to-connect-install-select-installation.md)。
- Windows Server 域控制器必须安装下列修补程序：
    - 对于 [Windows Server 2016](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
    - 对于 [Windows Server 2019](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="supported-scenarios"></a>支持的方案

该方案在以下两个方案中均支持单点登录 (SSO)：

- 对于云资源，如 Microsoft 365 和其他支持 SAML 的应用程序。
- 对于本地资源，集成了 Windows 的网站身份验证。 资源可以包括需要 IIS 身份验证的网站和 SharePoint网站和/或使用 NTLM 身份验证的资源。

### <a name="unsupported-scenarios"></a>不支持的方案

不支持以下方案：

- Windows Server Active Directory 域服务 (AD DS) 已建立域联接（仅针对本地设备）的部署。
- 使用安全密钥的 RDP、VDI 和 Citrix 方案。
- 使用安全密钥的 S/MIME。
- 使用安全密钥的“运行身份”。
- 使用安全密钥登录到服务器。

## <a name="create-kerberos-server-object"></a>创建 Kerberos 服务器对象

管理员使用 Azure AD Connect 服务器中的 PowerShell 工具在其本地目录中创建 Azure AD 的 Kerberos 服务器对象。 在组织中包含 Azure AD 用户的每个域和林中运行以下步骤：

1. 升级到最新版本的 Azure AD Connect。 说明假设已将 Azure AD Connect 配置为支持混合环境。
1. 在 Azure AD Connect 服务器上，打开提升的 PowerShell 提示符，然后导航到 `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 运行以下 PowerShell 命令以同时在本地 Active Directory 域和 Azure Active Directory 租户中创建新的 Azure AD Kerberos 服务器对象。

> [!NOTE]
> 在以下示例中，将 `contoso.corp.com` 替换为本地 Active Directory 域名。

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

> [!NOTE]
> 如果你的组织保护基于密码的登录并强制实施现代身份验证方法（例如 MFA、FIDO2 或智能卡），则必须将“-UserPrincipalName”参数与全局管理员的用户主体名称一起使用。
>    - 在以下示例中，将 `contoso.corp.com` 替换为本地 Active Directory 域名。
>    - 在以下示例中的 `administrator@contoso.onmicrosoft.com` 替换为全局管理员的用户主体名称。

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter a User Principal Name of Azure Active Directory global administrator
$userPrincipalName = "administrator@contoso.onmicrosoft.com"

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -UserPrincipalName $userPrincipalName -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>查看并验证 Azure AD Kerberos 服务器

可以使用以下命令查看和验证新创建的 Azure AD Kerberos 服务器：

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

此命令输出 Azure AD Kerberos 服务器的属性。 可以查看这些属性，以验证是否所有内容都正常。

> [!NOTE]

通过提供凭据来对另一个域运行，这将通过 NTLM 进行连接，然后将失败。 如果用户是 AD 中“受保护用户”安全组的一员，则解决方法是：用其他域用户登录 ADConnect box 并且不提供 domainCredential。 它将使用当前登录用户的 kerebros 票证。 可以通过执行 whoami/groups 来确认用户在 AD 中具备执行上述命令所需的特权
 
| 属性 | 说明 |
| --- | --- |
| ID | AD DS DC 对象的唯一 ID。 该 ID 有时称为“槽”或​​“分支ID”。 |
| DomainDnsName | Active Directory 域的 DNS 域名。 |
| ComputerAccount | Azure AD Kerberos Server 对象的计算机帐户对象 (DC)。 |
| UserAccount | 已禁用的用户帐户对象，该对象保存 Azure AD Kerberos Server TGT 加密密钥。 此帐户的 DN 是 `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion | Azure AD Kerberos Server TGT 加密密钥的密钥版本。 该版本是在该密钥创建时分配的。 然后，该版本会在每次轮换该密钥时都递增。 增量是基于复制元数据的，并且将有可能大于 1。 例如，初始 KeyVersion 可能为 192272 。 该密钥第一次轮换时，该版本可能会前进到 212621。 要验证本地对象的 KeyVersion 和云对象的 CloudKeyVersion 是否相同，这一点很重要 。 |
| KeyUpdatedOn | 更新或创建 Azure AD Kerberos Server TGT 加密密钥的日期和时间。 |
| KeyUpdatedFrom | 上次更新 Azure AD Kerberos Server TGT 加密密钥的 DC。 |
| CloudId | Azure AD 对象的 ID。 必须与上述 ID 匹配。 |
| CloudDomainDnsName | Azure AD 对象的 DomainDnsName。 必须与上面的 DomainDnsName 匹配。 |
| CloudKeyVersion | Azure AD 对象的 KeyVersion。 必须与上面的 KeyVersion 匹配。 |
| CloudKeyUpdatedOn | Azure AD 对象的 KeyUpdatedOn。 必须与上面的 KeyUpdatedOn 匹配。 |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>轮替 Azure AD Kerberos 服务器密钥

Azure AD Kerberos 服务器加密 krbtgt 密钥应定期轮替。 建议遵循用于轮替所有其他 Active Directory 域控制器 krbtgt 密钥的相同计划。

> [!WARNING]
> 还可以使用其他工具轮替 krbtgt 密钥，但是，你必须使用本文档中提到的工具来轮替 Azure AD Kerberos 服务器的 krbtgt 密钥。 这可确保在本地 AD 和 Azure AD 中更新密钥。

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>删除 Azure AD Kerberos 服务器

如果要还原方案并从本地 Active Directory 和 Azure Active Directory 中删除 Azure AD Kerberos 服务器，请运行以下命令：

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>多林和多域方案

Azure AD Kerberos 服务器对象在 Azure AD 中表示为 *KerberosDomain* 对象。 每个本地 Active Directory 域在 Azure AD 中都表示为一个 *KerberosDomain* 对象。

例如，你的组织有一个 Active Directory 林，其中有两个域，分别为 `contoso.com` 和 `fabrikam.com`。 如果选择允许 Azure AD 为整个林颁发 Kerberos TGT，则 Azure AD 中有两个 *KerberosDomain* 对象。 一个是用于 `contoso.com` 的 *KerberosDomain* 对象，另一个用于 `fabrikam.com`。 如果有多个 Active Directory 的林，则每个林中的每个域都有一个 *KerberosDomain* 对象。

你需要在组织中每个包含 Azure AD 用户的域和林中运行[创建 Kerberos 服务器对象](#create-kerberos-server-object)的步骤。

## <a name="known-behavior"></a>已知行为

如果密码已过期，则会阻止通过 FIDO 登录。 希望用户重置其密码，然后才能使用 FIDO 登录。

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

如果你想要共享有关此功能的反馈或遇到的问题，请使用以下步骤，通过 Windows 反馈中心应用来进行共享：

1. 启动反馈中心并确保你已登录。
1. 按照以下分类提交反馈：
   - 类别：安全和隐私
   - 子类别：FIDO
1. 若要捕获日志，请使用选项“重新创建问题”。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-this-work-in-my-on-premises-environment"></a>这在我的本地环境中有效吗？

此功能不适用于纯本地 Active Directory 域服务 (AD DS) 环境。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的组织需要双重身份验证来访问资源。 为支持这一要求，我该做什么？

安全密钥分为多种形式。 请联系相关的设备制造商，讨论如何使用 PIN 或生物识别功能作为次要因素来启用设备。

### <a name="can-admins-set-up-security-keys"></a>管理员可以设置安全密钥吗？

我们正在致力于提供此功能的正式发布 (GA)。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>在哪里可以找到合规的安全密钥？

[FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>如果丢失了安全密钥，我该怎么办？

要从 Azure 门户中删除密钥，可以导航到“安全信息”页面并删除安全密钥。

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>创建混合 Azure AD 联接的计算机后，我无法立即使用 FIDO

如果清理安装混合 Azure AD 联接的计算机，则在加入域并重新启动后，你必须使用密码登录，并等待策略同步，然后才能使用 FIDO 登录。

- 通过在命令窗口中键入 `dsregcmd /status` 来检查当前状态，并检查 AzureAdJoined和 DomainJoined 是否都显示 YES。  
- 此延迟是加入域的设备的已知限制，并非特定于 FIDO。

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>使用 FIDO 登录并收到凭据提示后，无法获取到 NTLM 网络资源的 SSO

请确保对足够的域控制器进行修补，以便及时响应资源服务请求。 要检查是否可以看到运行该功能的域控制器，请查看 `nltest /dsgetdc:contoso /keylist /kdc` 的输出。

> [!NOTE]
> `nltest` 命令中的 `/keylist` 开关在客户端 Windows 10 v2004 及更高版本中可用。

## <a name="next-steps"></a>后续步骤

[详细了解无密码](concept-authentication-passwordless.md)
