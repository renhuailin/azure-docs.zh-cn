---
title: Azure 安全基准 V2 - 标识管理
description: Azure 安全基准 V2 标识管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 33f5dff65fa7ad8274051f784f2e61dc8366d389
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368845"
---
# <a name="security-control-v2-identity-management"></a>安全控制 V2：标识管理

标识管理涵盖了使用 Azure Active Directory 建立安全标识和访问控制的控件。 这包括将单一登录、强身份验证、托管标识（和服务主体）用于应用程序、条件访问和帐户异常监视。

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| IM-1 | 16.1、16.2、16.4、16.5 | IA-2、IA-8、AC-2、AC-3 |

Azure Active Directory (Azure AD) 是 Azure 的默认标识和访问管理服务。 你应该在 Azure AD 上标准化，以便控制你的组织在以下方面的标识和访问管理：
- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、PaaS 和 SaaS 应用程序。

- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。

在组织的云安全做法中，应优先处理 Azure AD 保护事宜。 Azure AD 提供标识安全分数，让你可以根据 Microsoft 的最佳做法建议来评估标识安全状况。 使用评分来估计你的配置与最佳做法建议的匹配程度，并改善你的安全状况。

注意：Azure AD 支持外部标识提供者，这些提供者允许没有 Microsoft 帐户的用户使用其外部标识登录到其应用程序和资源。

- [Azure AD 中的租户](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [如何创建和配置 Azure AD 实例](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [定义 Azure AD 租户](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [对应用程序使用外部标识提供者](../../active-directory/external-identities/identity-providers.md)

- [Azure AD 中的标识安全评分是什么](../../active-directory/fundamentals/identity-secure-score.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| IM-2 | 空值 | AC-2、AC-3、IA-2、IA-4、IA-9 |

对于非人工帐户（例如服务或自动化），请使用 Azure 托管标识，而不是创建功能更强大的人工帐户来访问资源或执行代码。 Azure 托管标识可以向支持 Azure AD 身份验证的 Azure 服务和资源进行身份验证。 身份验证是通过预定义的访问授权规则启用的，避免了在源代码或配置文件中使用硬编码的凭据。 

对于不支持托管标识的服务，则请使用 Azure AD 在资源级别创建权限受限的服务主体。  建议使用证书凭据配置服务主体，并回退到客户端机密。 在这两种情况下，都可以将 Azure Key Vault 与 Azure 托管标识结合使用，以便运行时环境（例如 Azure 函数）可以从密钥保管库中检索凭据。

- [Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)

- [支持 Azure 资源托管标识的服务](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)

- [使用证书创建服务主体](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

使用 Azure Key Vault 进行安全主体注册：authentication#authorize-a-security-principal-to-access-key-vault

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 进行应用程序访问

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| IM-3 | 4.4 | IA-2、IA-4 |

Azure AD 提供对 Azure 资源、云应用程序和本地应用程序的标识和访问管理。 标识和访问管理适用于企业标识（例如员工）以及外部标识（例如合作伙伴、供应商和提供商）。

使用 Azure AD 单一登录 (SSO) 管理你的组织在本地和云中的数据和资源，并对其进行安全的访问。 将你的所有用户、应用程序和设备连接到 Azure AD，以便实现无缝的安全访问，并实现更好的可见性和控制。 

- [了解 Azure AD 的应用程序 SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| IM-4 | 4.2、4.4、4.5、11.5、12.11、16.3 | AC-2、AC-3、IA-2、IA-4 |

Azure AD 支持通过多重身份验证 (MFA) 和强无密码方法进行强身份验证控制。  
- 多重身份验证：启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议来设置你的 MFA。 可以基于登录条件和风险因素，对所有用户、特选用户或单个用户强制执行 MFA。 

- 无密码身份验证：有三个无密码身份验证选项可用：Windows Hello for Business、Microsoft Authenticator 应用和本地身份验证方法（例如智能卡）。 

对于管理员和特权用户，请确保使用最高级别的强身份验证方法，然后将相应的强身份验证策略推广到其他用户。

如果仍使用传统的基于密码的身份验证进行 Azure AD 身份验证，请注意，纯云帐户（直接在 Azure 中创建的用户帐户）具有默认的基线密码策略。 混合帐户（来自本地 Active Directory 的用户帐户）遵循本地密码策略。 使用基于密码的身份验证时，Azure AD 提供了密码保护功能，以防止用户设置容易猜出的密码。 Microsoft 提供了基于遥测进行更新的受禁密码的全局列表，客户可以根据自己的需求（例如品牌、文化参考等）来补充列表。 此密码保护可用于纯云帐户和混合帐户。 

注意：仅基于密码凭据的身份验证容易遭受常见的攻击方法攻击。 为了提高安全性，请使用强身份验证，例如 MFA 和强密码策略。 对于可能具有默认密码的第三方应用程序和市场服务，应在初次设置服务期间更改这些设置。 

- [如何在 Azure 中启用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory 的无密码身份验证选项简介](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD 默认密码策略](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [使用 Azure AD 密码保护来消除错误密码](../../active-directory/authentication/concept-password-ban-bad.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| IM-5 | 4.8、4.9、16.12、16.13 | AC-2、AC-3、AC-7、AU-6 |

Azure AD 提供了以下数据源： 
-   登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。

-   审核日志 - 对于通过 Azure AD 中的各种功能所做的所有更改，可以通过日志为其提供可跟踪性。 所记录的更改审核日志的示例包括添加或删除用户、应用、组、角色和策略。

-   风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

-   已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可与 Azure Monitor、Azure Sentinel 或第三方 SIEM 系统集成。

Azure 安全中心还可针对某些可疑活动（例如失败的身份验证尝试次数太多，以及帐户已在订阅中遭到弃用）发出警报。 

Azure 高级威胁防护 (ATP) 是一种安全解决方案，可使用本地 Active Directory 信号来识别、检测和调查高级威胁、遭到入侵的标识和恶意的内部操作。

- [Azure AD 中的审核活动报告](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何查看 Azure AD 风险登录](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何确定标记为存在风险活动的 Azure AD 用户](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../../security-center/security-center-identity-access.md)

- [Azure 安全中心的威胁情报保护模块中的警报](../../security-center/alerts-reference.md)

- [如何将 Azure 活动日志集成到 Azure Monitor 中](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [连接 Azure AD Identity Protection 的数据](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure 高级威胁防护](/azure-advanced-threat-protection/what-is-atp)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| IM-6 | 空值 | AC-2、AC-3 |

基于用户定义的条件，使用 Azure AD 条件访问进行更精细的访问控制，例如，要求从特定 IP 范围登录的用户使用 MFA。 还可以通过 Azure AD 条件访问策略对不同的用例使用精细的身份验证会话管理。 

- [Azure 条件访问概述](../../active-directory/conditional-access/overview.md)

- [常见条件访问策略](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用条件访问配置身份验证会话管理](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据透露

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| IM-7 | 18.1, 18.7 | IA-5 |

执行 Azure DevOps 凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议你将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

对于 GitHub，你可以使用原生的机密扫描功能来识别代码中的凭据或其他形式的机密。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub 机密扫描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8：保护用户对旧版应用程序的访问

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| IM-8 | 14.6 | AC-2、AC-3、SC-11 |

确保为旧版应用程序和它们存储和处理的数据提供新式访问控制和会话监视。 虽然通常使用 VPN 来访问旧版应用程序，但它们通常只有基本的访问控制和有限的会话监视。

使用 Azure AD 应用程序代理，你可以将旧版本地应用程序发布到采用单一登录 (SSO) 的远程用户，同时使用 Azure AD 条件访问显式验证远程用户和设备的可信度。 

另外，Microsoft Cloud App Security 是一种云访问安全代理 (CASB) 服务，其提供的控件可用于监视用户的应用程序会话并阻止操作（适用于旧版本地应用程序和云软件即服务 (SaaS) 应用程序）。 

- [Azure AD 应用程序代理](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App 安全最佳做法](/cloud-app-security/best-practices)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)