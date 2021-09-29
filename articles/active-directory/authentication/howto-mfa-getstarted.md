---
title: Azure AD 多重身份验证部署注意事项
description: 了解成功实现 Azure AD 多重身份验证的部署注意事项和策略
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01044f9d03b911bfb6939023dfb4fd5d3b0a3cd3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124773790"
---
# <a name="plan-an-azure-active-directory-multi-factor-authentication-deployment"></a>规划 Azure Active Directory 多重身份验证部署 

Azure Active Directory (Azure AD) 多重身份验证 (MFA) 通过使用第二种形式的身份验证提供另一层安全性，可帮助保护对数据和应用程序的访问。 组织可以使用[条件访问](../conditional-access/overview.md)来启用多重身份验证，以使解决方案满足其特定需求。

本部署指南介绍了如何计划和实施 [Azure AD MFA](concept-mfa-howitworks.md) 的推出。

## <a name="prerequisites-for-deploying-azure-ad-mfa"></a>部署 Azure AD MFA 的先决条件

在开始部署之前，请确保满足相关方案的以下先决条件。

| 场景 | 先决条件 |
|----------|--------------|
|具有新式身份验证的仅限云的标识环境 | 无先决条件任务 |
|混合标识方案 | 部署 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 并在本地 Active Directory 域服务 (AD DS) 和 Azure AD 之间同步用户身份。 |
| 为云访问发布的本地旧版应用程序| 部署 [Azure AD 应用程序代理](../app-proxy/application-proxy-deployment-plan.md) |

## <a name="choose-authentication-methods-for-mfa"></a>为 MFA 选择身份验证方法

有许多方法可用于第二因素身份验证。 可以从可用身份验证方法列表中选择，在安全性、易用性和可用性方面评估每种方法。

>[!IMPORTANT]
>启用多种 MFA 方法，以便用户在其主要方法不可用时具有可用的备用方法。 方法包括：

- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
- [Microsoft Authenticator 应用](concept-authentication-authenticator-app.md)
- [FIDO2 安全密钥（预览版）](concept-authentication-passwordless.md#fido2-security-keys)
- [OATH 硬件令牌（预览版）](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
- [OATH 软件令牌](concept-authentication-oath-tokens.md#oath-software-tokens)
- [短信验证](concept-authentication-phone-options.md#mobile-phone-verification)
- [语音呼叫验证](concept-authentication-phone-options.md)

选择将在租户中使用的身份验证方法时，请考虑这些方法的安全性和易用性：

![选择正确的身份验证方法](media/concept-authentication-methods/authentication-methods.png)

若要详细了解这些方法的优势和安全性及其工作方式，请参阅以下资源：

- [Azure Active Directory 中有哪些可用的身份验证和验证方法？](concept-authentication-methods.md)
- [视频：选择正确的身份验证方法，确保组织安全](https://youtu.be/LB2yj4HSptc)

可以使用此 [PowerShell 脚本](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)来分析用户的 MFA 配置并建议适当的 MFA 身份验证方法。 

为获得最佳灵活性和易用性，请使用 Microsoft Authenticator 应用。 此身份验证方法提供最佳用户体验和多种模式，如无密码、MFA 推送通知和 OATH 代码。 Microsoft Authenticator 应用还符合美国国家标准和技术研究所 (NIST) 的[验证器确定性 2 级要求](../standards/nist-authenticator-assurance-level-2.md)。

你可以控制租户中可用的身份验证方法。 例如，你可能想要阻止一些安全性最低的方法，例如 SMS。

| 身份验证方法 | 管理方式 | Scoping |
|-----------------------|-------------|---------|
| Microsoft Authenticator（推送通知和无密码手机登录）    | MFA 设置或
身份验证方法策略 | Authenticator 无密码电话登录的范围可缩小到用户和组 |
| FIDO2 安全密钥 | 身份验证方法策略 | 可以将范围限定为用户和组 |
| 软件或硬件 OATH 令牌 | MFA 设置 |     |
| 短信验证 | MFA 设置 | 管理身份验证策略中主要身份验证的短信登录。 可以短信登录的范围限定为用户和组。 |
| 语音呼叫 | 身份验证方法策略 |       |


## <a name="plan-conditional-access-policies"></a>计划条件访问策略

Azure AD MFA 强制实施条件访问策略。 通过这些策略，你可以在有安全需要时提示用户进行多重身份验证，并在不需要时避免用户进入。

![概念性条件访问的流程](media/howto-mfa-getstarted/conditional-access-overview-how-it-works.png)

在 Azure 门户中，你将在“Azure Active Directory” > “安全性” > “条件访问”下配置条件访问策略。

若要详细了解如何创建条件访问策略，请参阅[在用户登录 Azure 门户时用于提示 Azure AD MFA 的条件访问策略](tutorial-enable-azure-mfa.md)。 这有助于：

- 熟悉用户界面
- 初步认识条件访问的工作原理

有关 Azure AD 条件访问部署的端到端指南，请参阅[条件访问部署计划](../conditional-access/plan-conditional-access.md)。

### <a name="common-policies-for-azure-ad-mfa"></a>Azure AD MFA 的常见策略

需要 Azure AD MFA 的常见用例包括：

- 对于[管理员](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- 到[特定应用程序](tutorial-enable-azure-mfa.md)
- 对于[所有用户](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- 对于 [Azure 管理](../conditional-access/howto-conditional-access-policy-azure-management.md)
- 从[你不信任的网络位置](../conditional-access/untrusted-networks.md)

### <a name="named-locations"></a>命名位置

要管理条件访问策略，使用条件访问策略的位置条件可将访问控制设置绑定到用户的网络位置。 我们建议使用[命名位置](../conditional-access/location-condition.md)，这样就可以创建 IP 地址范围、国家和地区的逻辑分组。 这将为所有应用创建一条策略：阻止从命名位置中登录。 请确保从此策略中排除你的管理员。

### <a name="risk-based-policies"></a>基于风险的政策

如果你的组织使用 [Azure AD 标识保护](../identity-protection/overview-identity-protection.md)来检测风险信号，请考虑使用[基于风险的策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)，而不是命名位置。 可以创建策略以在存在身份泄露威胁时强制更改密码，或者当登录[根据事件被视为风险](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation)时（如凭据泄露、从匿名 IP 地址登录等等）要求多重身份验证。 

风险策略包括：

- [要求所有用户注册 Azure AD MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
- [要求高风险用户更改密码](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)
- [要求具有中等或高登录风险的用户执行 MFA](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>将用户从每用户 MFA 转换为基于条件访问的 MFA

如果使用每用户启用和强制执行的 Azure AD 多重身份验证启用用户，则以下 PowerShell 可帮助你转换为基于条件访问的 Azure AD 多重身份验证。

在 ISE 窗口中运行此 PowerShell，或另存为要在本地运行的 `.PS1` 文件。 操作只能使用 [MSOnline 模块](/powershell/module/msonline/?view=azureadps-1.0#msonline)完成。 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="plan-user-session-lifetime"></a>规划用户会话生存期

规划 MFA 部署时，必须考虑提示用户的频率。 经常要求用户提供凭据看似很明智，但有时会适得其反。 如果用户习惯于不加思索地输入凭据，可能会无意中将凭据提供给恶意的凭据提示。
Azure AD 包含多个设置，用于确定用户需要重新执行身份验证的频率。 了解业务和用户的需求，并配置可为你的环境提供最佳平衡的设置。

我们建议使用带有主刷新令牌 (PRT) 的设备以改善最终用户体验，并且仅针对特定业务用例采取登录频率策略，以缩短会话生存期。

有关更多信息，请参阅[优化重新身份验证提示并了解 Azure AD MFA 的会话生存期](concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

## <a name="plan-user-registration"></a>规划用户注册

每个 MFA 部署中的一个主要步骤是让用户注册以使用 MFA。 身份验证方法（如语音和短信）允许预先注册，而其他身份验证方法（如 Authenticator 应用）则需要用户交互。 管理员必须确定用户注册其方法的方式。 

### <a name="combined-registration-for-sspr-and-azure-ad-mfa"></a>SSPR 和 Azure AD MFA 的组合注册
我们建议使用 Azure AD MFA 和 [Azure AD 自助密码重置 (SSPR)](concept-sspr-howitworks.md) 的[组合注册体验](howto-registration-mfa-sspr-combined.md)。 SSPR 允许用户使用用于 Azure AD MFA 的相同方法，以安全的方式重置密码。 合并注册是最终用户需要完成的一个步骤。

### <a name="registration-with-identity-protection"></a>使用标识保护进行注册
Azure AD 标识保护同时为 Azure AD MFA 过程提供自动风险检测的注册策略以及修正策略。 可以创建策略，以便在存在标识泄露威胁时强制更改密码，或者在登录操作被视为有风险时要求使用 MFA。
如果你使用 Azure Active Directory 标识保护，请[配置 Azure AD MFA 注册策略](../identity-protection/howto-identity-protection-configure-mfa-policy.md)，以提示用户在下次以交互方式登录时进行注册。

### <a name="registration-without-identity-protection"></a>不使用标识保护进行注册
如果你没有用于启用 Azure Active Directory 标识保护的许可证，则在下一次需要使用 MFA 进行登录时，会提示用户进行注册。 要要求用户使用 MFA，你可以使用条件访问策略并针对 HR 系统等常用应用程序实施策略。 如果用户的密码被泄露，则泄露的密码可用于注册 MFA，从而控制用户的帐户。 因此，我们[建议使用条件访问策略来保护安全注册过程](../conditional-access/howto-conditional-access-policy-registration.md)，因为条件访问策略要求可信设备和位置。 还可以通过要求[临时访问密码](howto-authentication-temporary-access-pass.md)来进一步确保该过程的安全。 由管理员颁发的一种有时间限制的密码，可满足强身份验证要求，并可用于载入其他身份验证方法（包括无密码方法）。

### <a name="increase-the-security-of-registered-users"></a>提高已注册用户的安全性
如果你的用户使用短信或语音呼叫注册了 MFA，可能需要将其切换为更安全的方法，例如 Microsoft Authenticator 应用。 Microsoft 现在提供功能的公共预览版，可在用户登录过程中提示用户设置 Microsoft Authenticator 应用。 可以按组设置这些提示，控制要提示的用户，从而通过针对性的宣传活动将用户切换为更安全的方法。 

### <a name="plan-recovery-scenarios"></a>规划恢复方案 
如前所述，确保用户注册了多种 MFA 方法，这样在某种 MFA 方法不可用时，用户可以采用备用方法。 如果用户没有可用的备用方法，你可以进行以下操作： 

- 为用户提供一个临时访问密码，以便用户能够管理自己的身份验证方法。 还可以提供一个临时访问密码以便能够临时访问资源。 
- 以管理员角色更新用户的方法。 为此，请在 Azure 门户中选择用户，然后选择“身份验证方法”并更新其方法。
用户通信

通知用户即将进行的更改、Azure AD MFA 注册要求以及任何必要的用户操作至关重要。 我们提供[通信模板](https://aka.ms/mfatemplates)和[最终用户文档](https://support.microsoft.com/account-billing/set-up-your-security-info-from-a-sign-in-prompt-28180870-c256-4ebf-8bd7-5335571bf9a8)来帮助草拟通信。 在该页上选择“安全信息”链接，将用户导航到 [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) 以注册。

## <a name="plan-integration-with-on-premises-systems"></a>计划与本地系统进行集成

直接向 Azure AD 进行身份验证和具有新式身份验证（WS-Fed、SAML、OAuth 和 OpenID Connect）的应用程序可以使用条件访问策略。
某些旧版应用程序和本地应用程序不会直接对 Azure AD 进行身份验证，需要额外的步骤才能使用 Azure AD MFA。 可以使用 Azure AD 应用程序代理或[网络策略服务](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures)来集成这些应用程序。

### <a name="integrate-with-ad-fs-resources"></a>与 AD FS 资源集成

我们建议将使用 Active Directory 联合身份验证服务 (AD FS) 保护的应用程序迁移到 Azure AD。 但是，如果尚未准备好将这些应用程序迁移到 Azure AD，则可以将 Azure MFA 适配器与 AD FS 2016 或更高版本一起使用。
如果你的组织与 Azure AD 联合，可以在本地和云中[将 Azure AD MFA 配置为基于 AD FS 资源的身份验证提供程序](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa)。  

### <a name="radius-clients-and-azure-ad-mfa"></a>RADIUS 客户端和 Azure AD MFA

对于使用 RADIUS 身份验证的应用程序，我们建议将客户端应用程序迁移到新式协议，例如 SAML、Open ID Connect，或 Azure AD 上的 OAuth。 如果无法更新应用程序，可以[使用 Azure MFA 扩展部署网络策略服务器 (NPS)](howto-mfa-nps-extension.md)。 网络策略服务器 (NPS) 扩展充当基于 RADIUS 的应用程序与 Azure AD MFA 之间的适配器，提供身份验证的第二因素。

#### <a name="common-integrations"></a>常见集成

许多供应商现在支持其应用程序的 SAML 身份验证。 如果可能，我们建议将这些应用程序与 Azure AD 联合并通过条件访问强制实施 MFA。 如果供应商不支持新式身份验证，你可以使用 NPS 扩展。
常见的 RADIUS 客户端集成包括[远程桌面网关](howto-mfa-nps-extension-rdg.md)和 [VPN 服务器](howto-mfa-nps-extension-vpn.md)等应用程序。 

其他应用程序可能包括：

- Citrix Gateway

  [Citrix Gateway](https://docs.citrix.com/en-us/advanced-concepts/implementation-guides/citrix-gateway-microsoft-azure.html#microsoft-azure-mfa-deployment-methods) 支持 RADIUS 和 NPS 扩展集成，以及 SAML 集成。

- Cisco VPN
  - Cisco VPN 支持 RADIUS 以及[用于 SSO 的 SAML 身份验证](../saas-apps/cisco-anyconnect.md)。
  - 从 RADIUS 身份验证迁移到 SAML 后，无需部署 NPS 扩展即可集成 Cisco VPN。

- 所有 VPN

## <a name="deploy-azure-ad-mfa"></a>部署 Azure AD MFA

MFA 推出计划应包括一个试点部署，随后在支持的容量范围内进行后续部署。 通过将条件访问策略应用于一小组试点用户来开始推出。 在评估对试点用户、使用的进程和注册行为的影响后，可以向策略添加更多的组，也可以向现有组添加更多用户。

请遵循以下步骤进行配置：

1. 满足必需的先决条件
1. 配置所选的身份验证方法
1. 配置条件访问策略
1. 配置会话生存期设置
1. 配置 Azure AD MFA 注册策略 

## <a name="manage-azure-ad-mfa"></a>管理 Azure AD MFA
本节提供有关 Azure AD MFA 的报告和故障诊断信息。

### <a name="reporting-and-monitoring"></a>报告和监视

Azure AD 具有提供技术和业务见解的报告，请跟踪部署进度并检查用户是否成功使用 MFA 登录。 根据贵组织的要求，让业务负责人和技术应用程序所有者拥有和使用这些报告。

可以使用[身份验证方法活动仪​​表板](howto-authentication-methods-activity.md)监控整个组织中的身份验证方法注册和使用情况。 这有助于了解正在注册哪些方法以及这些方法的使用情况。

#### <a name="sign-in-report-to-review-mfa-events"></a>登录报告以查看 MFA 事件

Azure AD 报告包括提示用户进行多重身份验证时事件的身份验证详细信息，以及是否正在使用任何条件访问策略。 还可以使用 PowerShell 报告注册了 MFA 的用户。 

可以从“安全性” > “MFA” > “活动报告”中查看 NPS 扩展和 AD FS 日志。

有关详细信息和其他 MFA 报告，请参阅[查看 Azure AD 多重身份验证事件](howto-mfa-reporting.md#view-the-azure-ad-sign-ins-report)。

### <a name="troubleshoot-azure-ad-mfa"></a>对 Azure AD MFA 进行故障排除
有关常见问题，请参阅 [Azure AD MFA 故障排除](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)。

## <a name="next-steps"></a>后续步骤

[部署其他标识功能](../fundamentals/active-directory-deployment-plans.md)