---
title: Azure Active Directory 身份验证管理操作参考指南
description: 本操作参考指南介绍了为确保全身份验证管理安全而应采取的检查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: e3fd54dba324ddd8e7340e47d6b7f63ee6b2ea5b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233359"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory 身份验证管理操作参考指南

[Azure AD 操作参考指南](active-directory-ops-guide-intro.md)的这一部分介绍了在保护和管理凭据、定义身份验证体验、委派分配、衡量使用量，以及基于企业安全状况定义访问策略时应该采取的检查和操作。

> [!NOTE]
> 这些建议截至发布之日为最新，但可能会随着时间而改变。 组织应持续评估其标识实践，因为 Microsoft 产品和服务会不断发展。

## <a name="key-operational-processes"></a>关键操作过程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配到关键任务

管理 Azure Active Directory 需要持续执行关键操作任务和过程，这可能并不属于一个推出项目。 设置这些任务对于优化环境仍非常重要。 关键任务及其建议所有者包括：

| 任务 | 所有者 |
| :- | :- |
| 管理 Azure AD 中的单一登录 (SSO) 配置的生命周期 | IAM 运营团队 |
| 为 Azure AD 应用程序设计条件访问策略 | InfoSec 体系结构团队 |
| 存档 SIEM 系统中的登录活动 | InfoSec 运营团队 |
| 存档 SIEM 系统中的风险事件 | InfoSec 运营团队 |
| 会审和调查安全报告 | InfoSec 运营团队 |
| 会审和调查风险事件 | InfoSec 运营团队 |
| 会审和调查标记为有风险的用户和漏洞报告（由 Azure AD 标识保护提供） | InfoSec 运营团队 |

> [!NOTE]
> “Azure AD 标识保护”需要 Azure AD Premium P2 许可证。 要根据需要查找合适的许可证，请参阅[比较 Azure AD 免费版和 Azure AD 高级版的正式发布功能](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

查看列表时，可能会发现需要为缺少所有者的任务分配所有者，或使用与上述建议不符的所有者来调整任务的所有权。

#### <a name="owner-recommended-reading"></a>推荐阅读内容

- [在 Azure Active Directory 中分配管理员角色](../roles/permissions-reference.md)
- [Azure 中的监管](../../governance/index.yml)

## <a name="credentials-management"></a>凭据管理

### <a name="password-policies"></a>密码策略

安全地管理密码是标识和访问管理的最关键部分之一，并且通常是首当其冲的攻击目标。 Azure AD 支持多种功能，这些功能可帮助防止攻击得逞。

使用下表查找用于缓解需要解决的问题的建议解决方案：

| 问题 | 建议 |
| :- | :- |
| 不存在防范弱密码的机制 | 启用 Azure AD [自助服务密码重置 (SSPR)](../authentication/concept-sspr-howitworks.md) 和[密码保护](../authentication/concept-password-ban-bad-on-premises.md) |
| 不存在用于检测泄漏密码的机制 | 启用[密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) 以深刻了解 |
| 使用 AD FS，并且无法迁移到托管身份验证 | 启用 [AD FS Extranet 智能锁定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)和/或[Azure AD 智能锁定](../authentication/howto-password-smart-lockout.md) |
| 密码策略使用基于复杂性的规则，例如长度、多个字符集或过期时间 | 重新考虑采用 [Microsoft 推荐做法](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf)，将您的方法切换为密码管理并部署 [Azure AD 密码保护](../authentication/concept-password-ban-bad.md)。 |
| 用户未注册，无法使用多重身份验证 (MFA) | [注册所有用户的安全信息](../identity-protection/howto-identity-protection-configure-mfa-policy.md)，以便将其用作验证用户标识及其密码的机制 |
| 不存在根据用户风险吊销密码的措施 | 部署 Azure AD [标识保护用户风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)，以使用 SSPR 强制对泄露的凭据进行密码更改 |
| 没有智能锁定机制，无法防止来自已标识 IP 地址的不良参与者进行恶意身份验证 | 使用密码哈希同步或[直通身份验证](../hybrid/how-to-connect-pta-quick-start.md) (PTA) 部署云托管的身份验证 |

#### <a name="password-policies-recommended-reading"></a>密码策略方面的推荐读物

- [Azure AD 和 AD FS 最佳实践：防御密码喷雾攻击 - 企业移动性和安全性](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>启用自助服务密码重置和密码保护

支持人员接到的支持电话中，绝大部分是来自需要更改或重置密码的用户，这也是最主要的成本。 除了成本之外，将密码更改作为一种缓解用户风险的工具也是提高组织安全状况的基本步骤。

在完成前，建议至少部署 Azure AD [自助服务密码重置](../authentication/concept-sspr-howitworks.md) (SSPR) 和本地[密码保护](../authentication/howto-password-ban-bad-on-premises-deploy.md)：

- 减少对支持人员的呼叫次数。
- 弃用临时密码。
- 取代依赖于本地解决方案的任何现有自助服务密码管理解决方案。
- [消除你的组织中的弱密码](../authentication/concept-password-ban-bad.md)。

> [!NOTE]
> 对于具有 Azure AD Premium P2 订阅的组织，建议部署 SSPR，并将其用作[标识保护用户风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)的一部分。

### <a name="strong-credential-management"></a>强大的凭据管理措施

密码本身的安全性无法防止恶意参与者获取对你的环境的访问权限。 至少，任何具有特权帐户的用户都必须启用多因素身份验证 (MFA)。 理想情况下，应该启用[组合式注册](../authentication/concept-registration-mfa-sspr-combined.md)，并要求所有用户使用[组合式注册体验](https://support.microsoft.com/account-billing/set-up-your-security-info-from-a-sign-in-prompt-28180870-c256-4ebf-8bd7-5335571bf9a8)来注册 MFA 和 SSPR。 最终，我们建议采用一种策略来[提供复原能力](../authentication/concept-resilient-controls.md)，以降低由于不可预见的情况而导致锁定的风险。

![组合式用户体验流程](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>本地中断身份验证复原能力

除了简单和能够检测凭据泄漏的优势之外，Azure AD 密码哈希同步 (PHS) 和 Azure AD MFA 还允许用户访问 SaaS 应用程序和 Microsoft 365，即时是由于网络攻击（如 [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)）导致本地中断。 与联合身份验证一起使用时，也可以启用 PHS。 启用 PHS 允许在联合身份验证服务不可用时回退身份验证。

如果本地组织缺乏中断复原策略，或者有一个策略，但未与 Azure AD 集成，则应该部署 Azure AD PHS 并定义包含 PHS 的灾难恢复计划。 启用 Azure AD PHS 后，假如本地 Active Directory 不可用，用户可以向 Azure AD 进行身份验证。

![密码哈希同步流](./media/active-directory-ops-guide/active-directory-ops-img5.png)

要更好地理解你的身份验证选项，请参阅[选择 Azure Active Directory 混合标识解决方案的正确身份验证方法](../hybrid/choose-ad-authn.md)。

### <a name="programmatic-usage-of-credentials"></a>以编程方式使用凭据

使用 PowerShell 的脚本或使用 Microsoft Graph API 的应用程序都需要安全身份验证。 如果执行这些脚本和工具时凭据管理不当，则会增加凭据被盗的风险。 如果你使用的脚本或应用程序依赖于硬编码的密码或密码提示，你应该首先查看配置文件或源代码中的密码，然后替换这些依赖项并尽量使用 Azure 托管标识、集成的 Windows 身份验证或[证书](../reports-monitoring/tutorial-access-api-with-certificates.md)。 对于之前解决方案无法解决问题的应用程序，请考虑使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

如果确定某些服务主体具有密码凭据，并且不确定这些密码凭据如何由脚本或应用程序保护，请与应用程序所有者联系，以便更好地了解使用模式。

Microsoft 还建议你联系应用程序所有者，以了解使用模式（如果某些服务主体带有密码凭据）。

## <a name="authentication-experience"></a>身份验证体验

### <a name="on-premises-authentication"></a>本地身份验证

如果在公司网络内部，并以本地域控制器为目标，则采用集成 Windows 身份验证的联合身份验证 (IWA)、采用密码哈希同步的无缝单一登录 (SSO) 托管身份验证或者直通身份验证，用户体验最佳。 它最大程度地减少了凭据提示造成的疲劳，并降低了用户遭受网络钓鱼攻击的威胁。 如果已在 PHS 或 PTA 中使用云托管的身份验证，但用户在本地进行身份验证时仍需要键入密码，则应立即[部署无缝 SSO](../hybrid/how-to-connect-sso.md)。 另一方面，如果你当前已与计划最终迁移到云托管的身份验证，则应将无缝 SSO 作为迁移项目的一部分实现。

### <a name="device-trust-access-policies"></a>设备信任访问策略

与组织中的用户一样，设备也是要保护的核心标识。 可以使用设备标识随时随地保护你的资源。 对设备进行身份验证并考虑其信任类型可通过以下几个方面改善你的安全状况和易用性：

- 在设备受信任的情况下，避免摩擦（例如，通过 MFA）
- 阻止来自不受信任设备的访问
- 对于 Windows 10 设备，提供[无缝单一登录本地资源](../devices/azuread-join-sso.md)的功能。

要实现此目标，可以使用下述某种方法将设备标识引入 Azure AD 并对其进行管理：

- 组织可以使用 [Microsoft Intune](/intune/what-is-intune) 来管理设备并强制实施符合性策略、证明设备运行状况，并根据设备是否合规来设置条件访问策略。 Microsoft Intune 可以管理 iOS 设备、Mac桌面（通过 JAMF 集成）、Windows 桌面（最初使用 Mobile Device Management for Windows 10，以及通过 Microsoft Endpoint Configuration Manager 进行共同管理）和 Android 移动设备。
- 在具有 Active Directory 加入域的计算机设备的环境中，[混合 Azure AD 加入](../devices/hybrid-azuread-join-managed-domains.md)通过组策略或 Microsoft Endpoint Configuration Manager 提供管理功能。 组织可以通过 PHS 或通过采用无缝 SSO 的 PTA 来部署托管环境。 在 Azure AD 中使用自带设备时，可通过跨云和本地资源的 SSO 最大限度提高用户生产力，同时使您能够通过[条件访问](../conditional-access/overview.md)来确保对云和本地资源的访问安全。

如果你的 Windows 设备已加入域，但未在云中注册，或已在云中注册但没有条件访问策略，则你应注册未注册的设备，而且在上述两种情况下，均应在条件访问策略中[使用混合 Azure AD 加入作为控件](../conditional-access/require-managed-devices.md)。

![需要混合设备的条件访问策略中授权的屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img6.png)

如果要使用 MDM 或 Microsoft Intune 管理设备，但未在条件访问策略中使用设备控件，则建议在这些策略中使用[要求将设备标记为合规](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant)作为控件。

![需要设备合规性的条件访问策略中授权的屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>设备信任访问策略方面的推荐读物

- [如何：规划混合 Azure Active Directory 加入的实施](../devices/hybrid-azuread-join-plan.md)
- [标识和设备访问权限配置](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello 企业版

在 Windows 10 中，[Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/hello-identity-verification)将电脑上的密码替换成了强双重身份验证。 Windows Hello 企业版可为用户提供更简单的 MFA 体验，并减少对密码的依赖。 如果尚未开始推广 Windows 10 设备，或仅部分部署了 Windows 10 设备，则建议升级到 Windows 10，并在所有设备上[启用 Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)。

如果您想了解有关无密码身份验证的更多信息，请参见[利用 Azure Active Directory 实现无密码世界](../authentication/concept-authentication-passwordless.md)。

## <a name="application-authentication-and-assignment"></a>应用程序身份验证和分配

### <a name="single-sign-on-for-apps"></a>应用的单一登录

要让用户获得最佳体验，降低风险，获得报告能力和治理能力，为整个企业提供标准化的单一登录机制至关重要。 如果使用的应用程序支持通过 Azure AD 实现 SSO，但目前已配置为使用本地帐户，则应重新配置这些应用程序以将 SSO 与 Azure AD 一起使用。 同样，如果正在使用的应用程序支持通过 Azure AD 实现 SSO，但目前使用另一个标识提供者，则应将这些应用程序重新配置为也将 SSO与 Azure AD 一起使用。 如果应用程序不支持联合身份验证协议，但支持基于表单的身份验证，我们建议你将应用程序配置为使用[密码保管](../app-proxy/application-proxy-configure-single-sign-on-password-vaulting.md)与 Azure AD 应用程序代理。

![基于 AppProxy 密码的登录](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 如果你没有一种机制来发现组织中非托管应用程序，我们建议使用诸如 [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security) 之类的云访问安全代理解决方案 (CASB) 来实施发现过程。

最后，如果你有一个 Azure AD 应用程序库，并将支持 SSO 的应用程序与 Azure AD 结合使用，我们建议[将该应用程序列在应用程序库中](../develop/v2-howto-app-gallery-listing.md)。

#### <a name="single-sign-on-recommended-reading"></a>单一登录方面的推荐读物

- [什么是应用程序访问以及通过 Azure Active Directory 实现单一登录](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>将 AD FS 应用程序迁移到 Azure AD

[将应用从 AD FS 迁移到 Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md)可实现更高的安全性、更一致的可管理性和更好的协作体验。 如果之前在 AD FS 中配置的某些应用程序支持通过 Azure AD 来实现 SSO，则应重新配置应用程序以将 SSO 与 Azure AD 一起使用。 如果之前在 AD FS 中配置的应用程序具有不太常见的配置，而 Azure AD 不支持这些配置，则应联系应用的所有者以了解特殊配置是否是应用程序的绝对要求。 如果不是绝对需要，则应重新配置应用程序以将 SSO 与 Azure AD 一起使用。

![以 Azure AD 作为主要标识提供者](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> 可以使用 [Azure AD Connect Health for ADFS](../hybrid/how-to-connect-health-adfs.md) 来收集有关或许能够迁移到 Azure AD 的每个应用程序的配置详细信息。

### <a name="assign-users-to-applications"></a>将用户分配给应用程序

最好是使用组[将用户分配到应用程序](../manage-apps/assign-user-or-group-access-portal.md)，因为组具有更大的灵活性和大规模管理能力。 使用组的优点包括[基于属性的动态组成员身份](../enterprise-users/groups-dynamic-membership.md)和[应用所有者的委派](../fundamentals/active-directory-accessmanagement-managing-group-owners.md)。 因此，如果你已在使用和管理组，建议你采取以下操作来改进大规模管理：

- 向应用程序所有者委派组管理和治理任务。
- 允许对应用程序进行自助访问。
- 如果用户属性可以一致地确定对应用程序的访问权限，则定义动态组。
- 使用 [Azure AD 访问评审](../governance/access-reviews-overview.md)将证明实施到用于应用程序访问的组。

另一方面，如果找到已分配给单个用户的应用程序，请务必围绕这些应用程序实施[治理措施](../governance/index.yml)。

#### <a name="assign-users-to-applications-recommended-reading"></a>为应用程序分配用户方面的推荐读物

- [向 Azure Active Directory 中的应用程序分配用户和组](../manage-apps/assign-user-or-group-access-portal.md)
- [在 Azure Active Directory 中委托应用注册权限](../roles/delegate-app-roles.md)
- [Azure Active Directory 中的动态组成员资格规则](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>访问策略

### <a name="named-locations"></a>命名位置

使用 Azure AD 中的[命名位置](../conditional-access/location-condition.md)，可以在组织中标记受信任的 IP 地址范围。 Azure AD 使用命名位置以：

- 预防风险事件中的误报。 从受信任的网络位置登录可降低用户的登录风险。
- 配置[基于位置的条件访问](../conditional-access/location-condition.md)。

![命名位置](./media/active-directory-ops-guide/active-directory-ops-img10.png)

根据优先级，使用下表找到最符合组织需求的推荐解决方案：

| **Priority** | **方案** | 建议 |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | 如果您使用 PHS 或 PTA，但尚未定义命名位置 | 定义命名位置以提高风险事件的检测 |
| 2 | 如果你采用联合身份验证并且未使用“insideCorporateNetwork”声明，而且尚未定义命名位置 | 定义命名位置以提高风险事件的检测 |
| 3 | 如果没有在条件访问策略中使用命名位置，并且条件访问策略中没有风险或设备控件 | 配置条件访问策略以包含命名位置 |
| 4 | 如果你采用联合身份验证并且使用“insideCorporateNetwork”声明，而且尚未定义命名位置 | 定义命名位置以提高风险事件的检测 |
| 5 | 如果你使用将受信任 IP 地址与 MFA 结合使用（而不是命名位置），并且将这些 IP 地址标记为受信任 | 定义命名位置，并将其标记为受信任，以改善风险事件的检测 |

### <a name="risk-based-access-policies"></a>基于风险的访问策略

Azure AD 可以计算每次登录和每个用户的风险。 使用风险作为访问策略中的条件可以提供更好的用户体验（例如，减少身份验证提示）和更好的安全性（例如，仅在需要时提示用户），并且可以自动完成响应和修正。

![登录风险策略](./media/active-directory-ops-guide/active-directory-ops-img11.png)

如果你已拥有的 Azure AD Premium P2 许可证支持在访问策略中使用风险，但未使用风险条件，我们强烈建议将风险添加到安全状况。

#### <a name="risk-based-access-policies-recommended-reading"></a>基于风险的访问策略方面的推荐读物

- [如何：配置登录风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [如何：配置用户风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>客户端应用程序访问策略

Microsoft Intune 应用管理 (MAM) 能够将数据保护控件（如存储加密、PIN、远程存储清理等）推送到兼容的客户端移动应用程序（例如 Outlook Mobile）。 此外，还可以创建条件访问策略，以[限制访问](../conditional-access/app-based-conditional-access.md)，即，只能从经过批准或兼容的应用访问云服务（如 Exchange Online）。

如果你的员工安装支持 MAM 的应用程序（如 Office 移动应用）来访问公司资源（如 Exchange Online 或 SharePoint Online），并且你还支持 BYOD（自带设备），我们建议你部署应用程序 MAM 策略，以在不注册 MDM 的情况下管理个人拥有的设备中的应用程序配置，然后更新你的条件访问策略，以仅允许从具有 MAM 功能的客户端进行访问。

![条件访问授权控件](./media/active-directory-ops-guide/active-directory-ops-img12.png)

如果员工为了访问公司资源而安装了具备 MAM 功能的应用程序，但访问权限在 Intune 托管设备中受到限制，那么您应该考虑部署应用程序 MAM 策略以管理个人设备的应用程序配置，并更新条件访问策略以仅允许具有 MAM 功能的客户端进行访问。

### <a name="conditional-access-implementation"></a>条件访问实现

条件访问是改善组织的安全状况的重要工具。 因此，请务必遵循以下最佳做法：

- 确保所有 SaaS 应用程序都至少应用了一种策略
- 避免将“所有应用”过滤器与“阻止”控件结合使用，以避免发生锁定风险 
- 避免将“所有用户”用作筛选器，避免无意中添加“来宾” 
- 将所有“旧版”策略迁移到 Azure 门户
- 捕获用户、设备和应用程序的所有条件
- 使用条件访问策略来 [实现 MFA](../conditional-access/plan-conditional-access.md)，而不是使用 **基于用户的 MFA**
- 指定一小部分可应用于多个应用程序的核心策略
- 定义空的异常组，并将它们添加到策略中以获得异常策略
- 规划无 MFA 控件的[打碎玻璃](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency)帐户
- 通过为 Exchange Online 和 SharePoint Online 等服务实现相同的控件集合，确保跨 Microsoft 365 客户端应用程序（例如 Teams、OneDrive、Outlook 等）提供一致的体验
- 应通过组（而不是个体）来实现策略的分配
- 定期检查策略中使用的例外组，以限制用户超出安全状态的时间。 如果你有 Azure AD P2，则可以使用访问评审来自动执行此过程

#### <a name="conditional-access-recommended-reading"></a>条件访问方面的推荐读物

- [Azure Active Directory 中条件访问的最佳做法](../conditional-access/overview.md)
- [标识和设备访问权限配置](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory 条件访问设置参考](../conditional-access/concept-conditional-access-conditions.md)
- [常用条件访问策略](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>访问外围应用

### <a name="legacy-authentication"></a>旧式身份验证

强凭据（如 MFA）无法使用旧式身份验证协议保护应用，这使其成为恶意参与者首选的攻击途径。 锁定旧身份验证对于改善访问安全状况至关重要。

“旧身份验证”一词是指应用程序使用的身份验证协议，比如：

- 不使用新式身份验证的早期 Office 客户端（例如 Office 2010 客户端）
- 使用 IMAP/SMTP/POP 等邮件协议的客户端

攻击者强烈倾向于使用这些协议，事实上，几乎 [100% 的密码喷涂攻击](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)都使用旧的身份验证协议！ 黑客使用旧身份验证协议，因为旧协议不支持交互式登录，而其他安全质询（如多重身份验证和设备身份验证）刚好需要交互式登录。

如果你的环境中广泛使用了旧身份验证，则应计划尽快将旧客户端迁移到支持[新式身份验证](/office365/enterprise/modern-auth-for-office-2013-and-2016)的客户端。 在同一令牌中，如果某些用户已在使用新式身份验证，但其他用户仍使用旧身份验证，则应执行以下步骤来锁定旧版身份验证客户端：

1. 使用[登录活动报告](../reports-monitoring/concept-sign-ins.md)来识别仍在使用旧身份验证的用户并计划进行补救措施：

   a. 针对受影响的用户，升级到持新式身份验证的客户端。
   
   b. 计划转换时间范围，按以下步骤进行锁定。
   
   c. 确定哪些旧应用程序强烈依赖旧式身份验证。 请参阅下面的步骤 3。

2. 对于未使用旧身份验证的用户，请在源位置（例如 Exchange 邮箱）禁用旧版协议，以免暴露更多信息。
3. 对于其余帐户（最好是非人工身份，例如服务帐户），请使用[条件访问来限制旧协议](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417)后身份验证。

#### <a name="legacy-authentication-recommended-reading"></a>旧身份验证方面的推荐读物

- [启用或禁用对 Exchange Server 中的邮箱的 POP3 或 IMAP4 访问](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>同意授权

在违法许可授权攻击中，攻击者将创建一个 Azure AD 注册的应用程序，该应用程序请求访问数据（如联系信息、电子邮件或文档）。 当用户登录到恶意网站时，可能会由于网络钓鱼攻击而导致授予对恶意应用程序的同意。

对于 Microsoft 云服务，你可能希望仔细检查下列具有权限的应用：

- 具有委托 \*.ReadWrite 权限的应用
- 具有委托权限的应用可以代表用户读取、发送或管理电子邮件
- 被授予了以下权限的应用：

| 资源 | 权限 |
| :- | :- |
| Exchange Online | EAS.AccessAsUser.All |
| | EWS.AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- 被授予登录用户的完整用户模拟权限的应用。 例如：

|资源 | 权限 |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

为避免这种情况，请参阅[在 Office 365 中检测和修正违法许可授权](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants)，以识别和修复具有非法授权的应用程序或者授权超过了必要限度的应用程序。 接下来，[删除自助服务](../manage-apps/configure-user-consent.md)并[建立治理过程](../manage-apps/configure-admin-consent-workflow.md)。 最后，计划好定期审查应用权限，并在不需要时删除权限。

#### <a name="consent-grants-recommended-reading"></a>同意授予方面的推荐读物

- [Microsoft Graph API 权限](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>用户和组设置

如果没有明确的业务需求，则可以锁定以下用户和组设置：

#### <a name="user-settings"></a>用户设置

- 外部用户 - 外部协作可以通过 Teams、Power BI、SharePoint Online 和 Azure 信息保护等服务在企业中有机进行。 如果你有显式约束来控制用户启动的外部协作，则建议你通过使用 [Azure AD 权利管理](../governance/entitlement-management-overview.md)或受控操作（如通过帮助台）来启用外部用户。 如果您不想允许服务进行随机外部协作，则可以[全面阻止成员邀请外部用户](../external-identities/delegate-invitations.md)。 此外，也可以在外部用户邀请中[允许或阻止特定域](../external-identities/allow-deny-list.md)。
- 应用注册 - 启用应用注册后，最终用户可以自行将应用程序加入并向应用程序授予对数据的访问权限。 应用注册的一个典型示例是用户能够让 Outlook 插件或语音助手（如 Alexa 和 Siri）阅读其电子邮件和日历或代表他们发送电子邮件。 如果客户决定关闭应用程序注册，则必须将 InfoSec 和 IAM 团队纳入例外管理（根据业务需求而需要的应用注册），因为这两个团队需要使用管理员帐户注册应用程序，并且大多数情况下，可能需要设计一个流程以使该流程具有可操作性。
- 管理门户 - 组织可以锁定 Azure 门户中的 Azure AD 边栏选项卡，这样，非管理员就无法访问 Azure 门户中的 Azure AD 管理，避免造成困惑。 在 Azure AD 管理门户中转到用户设置以限制访问：

![管理门户限制访问](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 非管理员仍可通过命令行和其他编程接口访问 Azure AD 管理接口。

#### <a name="group-settings"></a>组设置

**自助服务组管理/用户可以创建安全组/Microsoft 365 组。** 如果云中当前没有适用于组的自助服务计划，客户可以决定将其关闭，直到组准备好使用此功能。

#### <a name="groups-recommended-reading"></a>组方面的推荐读物

- [什么是 Azure Active Directory B2B 协作？](../external-identities/what-is-b2b.md)
- [将应用程序与 Azure Active Directory 集成](../develop/quickstart-register-app.md)
- [Azure Active Directory 中的应用、权限和许可。](../develop/quickstart-register-app.md)
- [在 Azure Active Directory 中使用组管理对资源的访问权限](./active-directory-manage-groups.md)
- [在 Azure Active Directory 中设置自助式应用程序访问管理](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>来自意外位置的流量

攻击者来自世界各个角落。 使用条件访问策略管理风险，并以位置作为条件。 使用条件访问策略的[位置条件](../conditional-access/location-condition.md)，可以阻止没有商业理由登录的位置的访问。您可以阻止在没有商业原因的地点进行登录的访问。

![创建新命名位置](./media/active-directory-ops-guide/active-directory-ops-img14.png)

如果可用，请使用安全信息和事件管理 (SIEM) 解决方案来分析和查找跨区域访问的模式。 如果你没有使用 SIEM 产品，或者该产品没有从 Azure AD 中提取身份验证信息，我们建议你使用 [Azure Monitor](../../azure-monitor/overview.md) 来确定跨区域的访问模式。

## <a name="access-usage"></a>访问使用情况

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD 日志存档并与事件响应计划集成

有权访问 Azure AD 的登录活动、审核和风险事件对于故障排除、使用情况分析和取证调查至关重要。 Azure AD 通过具有有限保留期的 REST API 提供对这些源的访问。 安全信息和事件管理 (SIEM) 系统或等效的存档技术，是长期存储审核和可支持性的关键。 要启用 Azure AD 日志的长期存储，则必须将其添加到现有的 SIEM 解决方案中或使用 [Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md)。 存档日志，可作为事件响应计划和调查的一部分使用。

#### <a name="logs-recommended-reading"></a>日志方面的推荐读物

- [Azure Active Directory 审核 API 参考](/graph/api/resources/directoryaudit)
- [Azure Active Directory 登录活动报告 API 参考](/graph/api/resources/signin)
- [使用证书通过 Azure AD 报告 API 获取数据](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph for Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Office 365 Management Activity API 参考](/office/office-365-management-api/office-365-management-activity-api-reference)
- [如何使用 Azure Active Directory Power BI 内容包](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>总结

安全的标识基础结构有 12 个方面。 此列表将帮助你进一步保护和管理凭据、定义身份验证体验、委派分配、衡量使用情况，并根据企业安全状况定义访问策略。

- 将所有者分配到关键任务。
- 实施解决方案来检测薄弱密码或泄漏的密码，提高密码管理和保护，并进一步保护用户对资源的访问。
- 管理设备的标识以随时随地保护你的资源。
- 实施无密码身份验证。
- 在整个组织中提供标准化的单一登录机制。
- 将应用从 AD FS 迁移到 Azure AD，以实现更好的安全性和更一致的可管理性。
- 使用组将用户分配到应用程序，以允许更大的灵活性和大规模管理。
- 配置基于风险的访问策略。
- 锁定旧身份验证协议。
- 检测并修正违法许可授予。
- 锁定用户和组设置。
- 支持长期存储 Azure AD 日志，以便进行故障排除、使用情况分析和取证调查。

## <a name="next-steps"></a>后续步骤

开始学习[身份管理操作检查和操作](active-directory-ops-guide-govern.md)。