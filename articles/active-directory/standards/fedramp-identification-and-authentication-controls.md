---
title: 配置标识和身份验证控件，以使用 Azure Active Directory 满足 FedRAMP High 影响级别
description: 有关如何配置标识和身份验证控件才能满足 FedRAMP High 影响级别的详细说明。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ea917523c33e73013824203d892d3931a0f2ee9
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110586144"
---
# <a name="configure-identification-and-authentication-controls-to-meet-fedramp-high-impact-level"></a>配置标识和身份验证控件以满足 FedRAMP High 影响级别

标识和身份验证是实现[联邦风险与授权管理计划](https://www.fedramp.gov/) (FedRAMP) High 影响级别的关键。

你可能需要在你的 Azure Active Directory (Azure AD) 租户中配置标识和身份验证 (IA) 系列的以下控件和控件增强功能列表。

|控制系列|说明|
| - | - |
| IA-02| 标识和身份验证（组织用户） |
| IA-03| 设备标识和身份验证 |
| IA-04| 标识符管理 |
| IA-05| 验证器管理 |
| IA-06| 验证器反馈 |
| IA-07| 加密模块身份验证 |
| IA-08| 标识和身份验证（非组织用户） |

下表中的每一行都提供了规范性指导，以帮助你制定组织对与控制或控制增强功能有关的所有共同责任的响应措施。

## <a name="configurations"></a>配置

| 控件 ID 和子部分| 客户责任和指导 |
| - | - |
| IA-02| 唯一地标识用户并对其进行身份验证，或代表用户进行处理。<p> Azure AD 直接对用户和服务主体对象进行唯一地标识。 Azure AD 提供多种身份验证方法，你可以配置遵循美国国家标准和技术研究院 (NIST) 身份验证保证级别 (AAL) 3 的方法。<p>标识符 <br> <li>用户：[使用 Microsoft Graph 中的用户：ID 属性](/graph/api/resources/users?view=graph-rest-1.0&preserve-view=true)<br><li>服务主体：[ServicePrincipal 资源类型：ID 属性](/graph/api/resources/serviceprincipal?view=graph-rest-1.0&preserve-view=true)<p>身份验证和多重身份验证<br> <li>[通过 Microsoft 身份平台实现 NIST 验证器的保证级别](nist-overview.md) |
| IA-02(1)<br>IA-02(3)| 对特权帐户的所有访问使用多重身份验证。 <p>为整个解决方案配置以下元素，确保对特权帐户的所有访问都需要执行多重身份验证。<p>配置条件访问策略，要求对所有用户进行多重身份验证。<br> 实施 Azure AD Privileged Identity Management，需要在使用之前使用多重身份验证激活特权角色分配。<p>设定 Privileged Identity Management 的激活要求后，将无法在无网络访问的情况下激活特权帐户，因此，本地访问权限始终无特权。<p>多重身份验证和 Privileged Identity Management<br> <li>[条件访问：要求对所有用户进行多重身份验证](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)<br> <li>[在 Privileged Identity Management 中配置 Azure AD 角色设置](../privileged-identity-management/pim-how-to-change-default-settings.md?tabs=new) |
| IA-02(2)<br>IA-02(4)| 对非特权帐户的所有访问执行多重身份验证<p>为整个解决方案配置以下元素，确保对非特权帐户的所有访问都需要执行 MFA。<p> 配置条件访问策略，要求对所有用户执行 MFA。<br> 通过 MDM（如 Microsoft Intune）、Microsoft Endpoint Manager (MEM) 或组策略对象 (GPO) 配置设备管理策略，以强制使用特定的身份验证方法。<br> 配置条件访问策略，以强制实施设备合规性检查。<p>Microsoft 建议使用多重加密硬件验证器（例如，FIDO2 安全密钥、具有硬件 TPM 的 Windows Hello 企业版或智能卡）以满足 AAL3 等级要求。 如果你的组织完全基于云，我们建议使用 FIDO2 安全密钥或 Windows Hello 企业版。<p>Windows Hello 企业版尚未在所需的 FIPS 140 安全级别进行验证，因此，在认可其达到 AAL3 之前，联邦客户需要进行风险评估和评价。 有关 Windows Hello 企业版 FIPS 140 验证的其他详细信息，请参阅 [Microsoft NIST AAL](nist-overview.md)。<p>有关 MDM 策略的指南因身份验证方法而略有不同，具体如下所示。 <p>智能卡/Windows Hello 企业版<br> [无密码策略 - 需要 Windows Hello 企业版或智能卡](/windows/security/identity-protection/hello-for-business/passwordless-strategy)<br> [要求将设备标记为合规](../conditional-access/require-managed-devices.md)<br> [条件访问 - 要求对所有用户执行 MFA](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)<p> 仅混合<br> [无密码策略 - 将用户帐户配置为禁止密码验证](/windows/security/identity-protection/hello-for-business/passwordless-strategy)<p> 仅智能卡<br>[创建规则以发送身份验证方法声明](/windows-server/identity/ad-fs/operations/create-a-rule-to-send-an-authentication-method-claim)<br>[配置身份验证策略](/windows-server/identity/ad-fs/operations/configure-authentication-policies)<p>FIDO2 安全密钥<br> [无密码策略 - 不包括密码凭据提供程序](/windows/security/identity-protection/hello-for-business/passwordless-strategy)<br> [要求将设备标记为合规](../conditional-access/require-managed-devices.md)<br> [条件访问 - 要求对所有用户执行 MFA](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)<p>身份验证方法<br> [Azure Active Directory 无密码登录（预览版） | FIDO2 安全密钥] (../authentication/concept-authentication-passwordless.md)<br> [无密码安全密钥登录 Windows - Azure Active Directory](../authentication/howto-authentication-passwordless-security-key-windows.md)<br> [ADFS：通过 Azure AD 和 Office 365 进行证书身份验证](/archive/blogs/samueld/adfs-certauth-aad-o365)<br> [Windows (Windows 10) 中智能卡登录的工作原理](/windows/security/identity-protection/smart-cards/smart-card-how-smart-card-sign-in-works-in-windows)<br> [Windows Hello 企业版 (Windows 10) 概述](/windows/security/identity-protection/hello-for-business/hello-overview)<p>其他资源：<br> [策略 CSP - Windows 客户端管理](/windows/client-management/mdm/policy-configuration-service-provider)<br> [在 Intune 中的 Windows 10 设备上使用 PowerShell 脚本](/mem/intune/apps/intune-management-extension)<br> [规划使用 Azure AD 的无密码身份验证部署](../authentication/howto-authentication-passwordless-deployment.md)<br> |
| IA-02(5)| 当多个用户有权访问共享或组帐户密码时，要求每个用户首先使用单独的验证器进行身份验证。<p>每个用户使用一个单独的帐户。 如果需要共享帐户，Azure AD 允许将多个验证器绑定到一个帐户，这样每个用户都有一个单独的身份验证器。 <p>资源<br><li>[工作原理：Azure AD 多重身份验证](../authentication/concept-mfa-howitworks.md)<br> <li>[管理 Azure AD 多重身份验证的身份验证方法](../authentication/howto-mfa-userdevicesettings.md) |
| IA-02(8)| 对特权帐户的网络访问实施防重放身份验证机制。<p>配置条件访问策略，要求对所有用户进行多重身份验证。 身份验证保证级别 2 和 3 的所有 Azure AD 身份验证方法都使用 nonce 或质询，可以抵御重放攻击。<p>参考<br> <li>[条件访问：要求对所有用户进行多重身份验证](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)<br> <li>[通过 Microsoft 身份平台实现 NIST 验证器的保证级别](nist-overview.md) |
| IA-02(11)| 实施 Azure AD 多重身份验证来远程访问客户部署的资源，从而使其中的一个验证因素由与获得访问权限的系统（设备符合 FIPS 140-2、NIAP 认证或 NSA 批准）隔离的设备提供。<p>请参阅 IA-02(1-4) 的指南。 根据 AAL3 考虑需要满足单独的设备要求的 Azure AD 身份验证方法包括：<p> FIDO2 安全密钥<br> <li>具有硬件 TPM（NIST 800-63B 部分 5.1.7.1 将 TPM 视为有效的“你拥有的内容”因素）的 Windows Hello 企业版<br> <li>智能卡<p>参考<br><li>[通过 Microsoft 身份平台实现 NIST 验证器的保证级别](nist-overview.md)<br> <li>[NIST 800-63B 部分 5.1.7.1](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| IA-02(12)| 接受并验证个人身份验证 (PIV) 凭据。如果客户未部署 PIV 凭据，则此控件不适用。<p>使用 Active Directory 联合身份验证服务 (AD FS) 配置联合身份验证，以接受将 PIV（证书身份验证）作为主要和多重身份验证方法，并在使用 PIV 时颁发多重身份验证 (MultipleAuthN) 声明。 使用 SupportsMFA 在 Azure AD 中配置联合域，以将源自 Azure AD 的多重身份验证请求定向到 AD FS。 此外，还可使用 PIV 在 Windows 设备上登录，随后利用集成的 Windows 身份验证和无缝单一登录。 Windows Server 和客户端会在用于身份验证时默认验证证书。 <p>资源<br><li>[什么是与 Azure AD 的联合？](../hybrid/whatis-fed.md)<br> <li>[配置 AD FS 以支持用户证书身份验证](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br> <li>[配置身份验证策略](/windows-server/identity/ad-fs/operations/configure-authentication-policies)<br> <li>[将 Azure AD 多重身份验证与 AD FS 配合使用来保护资源](../authentication/howto-mfa-adfs.md)<br><li>[Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings)<br> <li>[Azure AD Connect：无缝单一登录](../hybrid/how-to-connect-sso.md) |
| IA-03| 在建立连接之前实施设备标识和身份验证。<p>配置 Azure AD 来标识和验证 Azure AD 注册、Azure AD 加入和 Azure AD 混合加入设备的身份。<p> 资源<br><li>[什么是设备标识？](../devices/overview.md)<br> <li>[规划 Azure AD 设备部署](../devices/plan-device-deployment.md)<br><li>[通过条件性访问要求使用受管理设备进行云应用访问](../conditional-access/require-managed-devices.md) |
| IA-04<br>IA-04(4)| 在非活动状态时间达到 35 天后禁用帐户标识符，避免重复使用 2 年。通过唯一地标识每个个体（例如，承包商、外籍人士等）来管理单个标识符。<p>根据 AC-02 中定义的现有组织策略，在 Azure AD 中分配和管理单个帐户标识符和状态。 按照 AC-02 (3)，在非活动状态时间达到 35 天后自动禁用用户和设备帐户。 确保组织策略将所有保持禁用状态的帐户至少保留 2 年。 2 年后，可以将其删除。 <p>确定非活动状态<br> <li>[管理 Azure AD 中的非活动用户帐户](../reports-monitoring/howto-manage-inactive-user-accounts.md)<br> <li>[在 Azure AD 中管理旧设备](../devices/manage-stale-devices.md)<br> <li>[请参阅 AC-02 指南](fedramp-access-controls.md) |
| IA-05| 配置和管理信息系统验证器。<p>Azure AD 支持多种身份验证方法。 你可以使用现有的组织策略进行管理。 请参阅 IA-02 (1-4) 中有关验证器选择的指南。 允许用户合并注册 SSPR 和 Azure AD 多重身份验证，并要求用户至少注册两个可接受的多重身份验证方法，以便进行自我修正。 你可以使用身份验证方法 API 随时撤销用户配置的验证器。 <p>验证器强度/保护验证器内容<br> <li>[通过 Microsoft 身份平台实现 NIST 验证器的保证级别](nist-overview.md)<p>身份验证方法和合并注册<br> <li>[Azure Active Directory 中有哪些可用的身份验证和验证方法？](../authentication/concept-authentication-methods.md)<br> <li>[SSPR 和 Azure AD 多重身份验证的合并注册](../authentication/concept-registration-mfa-sspr-combined.md)<p>验证器撤销<br> <li>[Azure AD 身份验证方法 API 概述](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true) |
| IA-05(1)| 实现基于密码的身份验证要求。<p>根据 NIST SP 800-63B 部分 5.1.1：维护常用、应使用或已泄露密码的列表。<p>使用 Azure AD 密码保护时，默认的全局禁止密码列表会自动应用于 Azure AD 租户中的所有用户。 为了满足业务和安全需求，你可以在自定义的禁止密码列表中定义条目。 用户更改或重置密码时，系统会检查这些受禁密码列表以强制使用强密码。<p>强烈建议采用无密码策略。 此控件仅适用于密码验证器，因此，删除作为可用的身份验证器的密码将导致此控件不适用。<p>NIST 参考文档<br><li>[NIST 特殊发布 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)<br><li>[NIST 特殊发布 800-53 修订版 5](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf) - IA-5 - 控件增强功能 (1)<p>资源<br><li>[使用 Azure AD 密码保护来消除错误密码](../authentication/concept-password-ban-bad.md) |
| IA-05(2)| 实现基于 PKI 的身份验证要求。<p>通过 AD FS 与 Azure AD 进行联合，以实施基于 PKI 的身份验证。 默认情况下，AD FS 会验证证书，在本地缓存吊销数据，并将用户映射到 Active Directory 中经过身份验证的标识。 <p> 资源<br> <li>[什么是与 Azure AD 的联合？](../hybrid/whatis-fed.md)<br> <li>[配置 AD FS 以支持用户证书身份验证](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication) |
| IA-05(4)| 使用自动化工具来验证密码强度要求。 <p>Azure AD 实施自动化机制，可在创建时强制实施密码验证器强度。 此自动化机制还可以进行扩展，为本地 Active Directory 强制实施密码验证器强度。 NIST 800-53 的修订版 5 已撤消 IA-04 (4)，并将此要求合并到 IA-5 (1)。<p>资源<br> <li>[使用 Azure AD 密码保护来消除错误密码](../authentication/concept-password-ban-bad.md)<br> <li>[为 Active Directory 域服务实施的 Azure AD 密码保护](../authentication/concept-password-ban-bad-on-premises.md)<br><li>[NIST 特殊发布 800-53 修订版 5](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf) - IA-5 - 控件增强功能 (4) |
| IA-05(6)| 按照 FedRAMP High 影响级别中的规定保护验证器。<p>有关 Azure AD 如何保护验证器的详细信息，请参阅 [Azure AD 数据安全注意事项](https://aka.ms/aaddatawhitepaper)。 |
| IA-05(7)| 确保未加密的静态验证器（例如密码）没有嵌入到应用程序或访问脚本中，也没有存储到功能键上。<p>实现托管标识或服务主体对象（仅使用证书进行配置）。<p>资源<br><li>[什么是 Azure 资源的托管标识？](../managed-identities-azure-resources/overview.md)<br><li>[在门户中创建 Azure AD 应用程序和服务主体](../develop/howto-create-service-principal-portal.md) |
| IA-05(8)| 为在多个信息系统上有帐户的个人实施安全保护措施。<p>通过将所有应用程序连接到 Azure AD 来实现单一登录，而不是在多个信息系统上使用个人帐户。<p>[什么是 Azure 单一登录？](../manage-apps/what-is-single-sign-on.md) |
| IA-05(11)| 需要满足 FedRAMP High 影响级别要求的硬件令牌质量要求。<p>要求使用符合 AAL3 的硬件令牌。<p>[通过 Microsoft 身份平台实现 NIST 验证器的保证级别](https://azure.microsoft.com/resources/microsoft-nist/) |
| IA-05(13)| 使缓存的验证器强制过期。<p>当网络不可用时，系统使用缓存的验证器对本地计算机进行身份验证。 若要限制使用缓存的验证器，请将 Windows 设备配置为禁用缓存的验证器。 如果此操作不可行或不切实际，请使用以下补偿控件：<p>使用为 Office 应用程序强制实施的应用程序限制配置条件访问会话控件。<br> 使用其他应用程序的应用程序控件配置条件访问。<p>资源<br> <li>[要缓存的以前交互式登录数量](/windows/security/threat-protection/security-policy-settings/interactive-logon-number-of-previous-logons-to-cache-in-case-domain-controller-is-not-available)<br> <li>[条件访问策略中的会话控件：应用程序强制实施的限制](../conditional-access/concept-conditional-access-session.md)<br><li>[条件访问策略中的会话控件：条件访问应用程序控制](../conditional-access/concept-conditional-access-session.md) |
| IA-06| 在身份验证期间隐藏身份验证反馈信息。<p>默认情况下，Azure AD 会隐藏所有验证器反馈。<p>
| IA-07| 实施对符合适用联邦法律的加密模块进行身份验证的机制。<p>FedRAMP High 影响级别要求 AAL3 验证器。 符合 AAL3 的 Azure AD 支持的所有验证器都提供根据需要对访问模块的操作员进行身份验证的机制。 例如，在具有硬件 TPM 的 Windows Hello 企业版部署中，可配置 TPM 所有者的授权级别。<p> 资源<br><li>有关详细信息，请参阅 IA-02（2 和 4）。<br> <li>[通过 Microsoft 身份平台实现 NIST 验证器的保证级别](nist-overview.md) <br> <li>[TPM 组策略设置](/windows/security/information-protection/tpm/trusted-platform-module-services-group-policy-settings) |
| IA-08| 信息系统唯一地标识非组织用户（或代表非组织用户进行处理的进程）并进行身份验证。<p>Azure AD 使用联邦身份凭证与访问管理 (FICAM) 批准的协议，唯一地标识托管在组织租户或外部目录中的非组织用户，并对其进行身份验证。<p>资源<br><li>[什么是 Azure Active Directory 中的 B2B 协作？](../external-identities/what-is-b2b.md)<br> <li>[与 B2B 标识提供者的直接联合](../external-identities/direct-federation.md)<br> <li>[B2B 来宾用户的属性](../external-identities/user-properties.md) |
| IA-08(1)<br>IA-08(4)| 接受并验证其他联邦机构颁发的 PIV 凭据。符合 FICAM 颁发的配置文件。<p>将 Azure AD 配置为通过联合（OIDC、SAML）接受 PIV 凭据或通过集成 Windows 身份验证在本地接受 PIV 凭据。<p>资源<br> <li>[什么是与 Azure AD 的联合？](../hybrid/whatis-fed.md)<br> <li>[配置 AD FS 以支持用户证书身份验证](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><li>[什么是 Azure Active Directory 中的 B2B 协作？](../external-identities/what-is-b2b.md)<br> <li>[与 B2B 标识提供者的直接联合](../external-identities/direct-federation.md) |
| IA-08(2)| 仅接受 FICAM 批准的凭据。<p>Azure AD 支持达到 NIST AAL 1、2、3 级的验证器。 限制使用与所访问系统的安全类别相对应的身份验证器。 <p>Azure AD 支持多种身份验证方法。<p>资源<br> <li>[Azure Active Directory 中有哪些可用的身份验证和验证方法？](../authentication/concept-authentication-methods.md)<br> <li>[Azure AD 身份验证方法策略 API 概述](/graph/api/resources/authenticationmethodspolicies-overview?view=graph-rest-beta&preserve-view=true)<br> <li>[通过 Microsoft 身份平台实现 NIST 验证器的保证级别](https://azure.microsoft.com/resources/microsoft-nist/) |

## <a name="next-steps"></a>后续步骤

- [配置访问控制](fedramp-access-controls.md)
- [配置标识和身份验证控件](fedramp-identification-and-authentication-controls.md)
- [配置其他控件](fedramp-other-controls.md)
