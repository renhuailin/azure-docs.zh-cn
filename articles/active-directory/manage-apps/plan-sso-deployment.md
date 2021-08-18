---
title: 规划 Azure Active Directory 单一登录部署
description: 帮助你在组织中规划、部署和管理 SSO 的指南。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: eacb1d03ce4d7148543948f4bf107d8a99181a0e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744111"
---
# <a name="plan-a-single-sign-on-deployment"></a>计划单一登录部署

单一登录 (SSO) 是指只需使用单个用户帐户登录一次，就能访问用户所需的所有应用程序和资源。 使用 SSO，用户可以访问全部所需的应用程序，而无需再次进行身份验证。

## <a name="benefits-of-sso"></a>SSO 的优点

当用户在 Azure Active Directory (Azure AD) 中登录到应用程序时，单一登录 (SSO) 可以增加安全性和便利性。

许多组织依赖于软件即服务 (SaaS) 应用程序（如 Microsoft 365、Box 和 Salesforce）来提高最终用户生产力。 从历史上看，IT 人员需要在每个 SaaS 应用程序中单独创建和更新用户帐户，而用户需要记住每个 SaaS 应用程序的密码。

Azure 市场有 3000 多个预先集成了 SSO 连接的应用程序，因此你可以轻松地将它们集成到租户中。

## <a name="licensing"></a>许可

- **Azure AD 许可** - 适用于预先集成的 SaaS 应用程序的 SSO 是免费的。 但是，你可能需要根据目录中的对象数和要部署的功能购买额外的许可证。 有关许可证要求的完整列表，请参阅 [Azure Active Directory 定价](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。
- **应用程序许可** - 你需要根据业务需求购买适用于 SaaS 应用程序的许可证。 与应用程序所有者合作，确定分配到应用程序的用户是否具有与其在应用程序中的角色相对应的许可证。 如果 Azure AD 基于角色来管理自动预配，则在 Azure AD 中分配的角色必须与在应用程序中拥有的许可证数量一致。 如果在应用程序中拥有的许可证数量不正确，则可能会导致在用户的预配/更新过程中出现错误。

## <a name="plan-your-sso-team"></a>规划 SSO 团队

- **让合适的利益干系人参与** - 技术项目的失败通常是由于在影响、结果和责任方面存在不匹配的预期。 为了避免这些问题，请[确保让合适的利益干系人参与](../fundamentals/active-directory-deployment-plans.md)，并确保利益干系人了解其角色。
- **规划通信** - 通信对于任何新服务的成功都至关重要。 主动与用户沟通他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。 查看有关[最终用户将如何访问已启用 SSO 的应用程序](end-user-experiences.md)的选项，并根据你的选择编写通信内容。

## <a name="plan-your-sso-protocol"></a>规划 SSO 协议

基于联合身份验证协议的 SSO 实现可以改善安全性、可靠性和最终用户体验，实现起来也更容易。 许多应用程序都已预先集成到 Azure AD 中，并且[有相关的分步指南可供使用](../saas-apps/tutorial-list.md)。 你可以在我们的 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/)中找到它们。 有关每种 SSO 方法的详细信息，可参阅[单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)一文。

可通过两种主要方式让用户单一登录到应用：

- **使用联合单一登录**：Azure AD 通过使用用户的 Azure AD 帐户让用户向应用程序进行身份验证。 支持 SAML 2.0、WS 联合身份验证或 OpenID Connect 等协议的应用程序都支持此方法，而且此方法是内容最丰富的单一登录模式。 建议将联合 SSO 和 Azure AD 配合使用（如果应用程序支持它），而不是使用基于密码的 SSO 和 ADFS。

- **使用基于密码的单一登录**：用户在首次访问应用程序时使用用户名和密码登录。 首次登录后，Azure AD 会为应用程序提供用户名和密码。 基于密码的单一登录允许使用 Web 浏览器扩展插件或移动应用安全存储和重放应用程序的密码。 此选项利用应用程序提供的现有登录过程，允许管理员管理密码，不需要用户知道密码。

### <a name="considerations-for-federation-based-sso"></a>基于联合身份验证的 SSO 的注意事项

- **使用 OpenID Connect 和 OAuth** - 如果你要连接的应用程序支持它，请使用 OIDC/OAuth 2.0 方法来启用对该应用程序的 SSO。 此方法需要的配置较少，支持更丰富的用户体验。 有关详细信息，请参阅 [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)、[OpenID Connect 1.0](../develop/v2-protocols-oidc.md) 和 [Azure Active Directory 开发人员指南](../develop/index.yml)。
- **基于 SAML 的 SSO 的终结点配置** - 如果你使用 SAML，则开发人员在配置应用程序之前需要特定的信息。 有关详细信息，请参阅[配置基于 SAML 的单一登录](configure-saml-single-sign-on.md)。
- **基于 SAML 的 SSO 的证书管理** - 当你为应用程序启用联合 SSO 时，Azure AD 会创建默认情况下有效期为三年的证书。 可以根据需要自定义该证书的到期日期。 请确保已做好相关流程的准备，以便在证书到期之前续订证书。 若要了解详细信息，请参阅 [Azure AD - 管理证书](./manage-certificates-for-federated-single-sign-on.md)。

### <a name="considerations-for-password-based-sso"></a>基于密码的 SSO 的注意事项

如果将 Azure AD 用于基于密码的 SSO，则需要部署一个浏览器扩展，从而安全地检索凭据并填充登录表单。 请定义一种机制，以便通过[支持的浏览器](../user-help/my-apps-portal-end-user-access.md)大规模部署扩展。 选项包括：

- [Internet Explorer 的组策略](my-apps-deployment-plan.md)
- [Internet Explorer 的 Configuration Manager](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Chrome、Firefox、Microsoft Edge 或 IE 的用户驱动型下载和配置](../user-help/my-apps-portal-end-user-access.md)

若要了解详细信息，请参阅[如何配置密码单一登录](./configure-password-single-sign-on-non-gallery-applications.md)。

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>捕获不在库中的应用程序的登录表单元数据

Microsoft 支持在 Web 应用程序上捕获元数据以进行密码保管（捕获用户名字段和密码字段）。 请在配置应用程序以捕获表单元数据的过程中导航到登录 URL。 要求应用程序所有者提供确切的登录 URL。 此信息在登录过程中使用（在登录期间将 Azure AD 凭据映射到应用程序）。

若要了解详细信息，请参阅[什么是使用 Azure AD 进行的应用程序访问和 SSO？- 基于密码的 SSO](./what-is-single-sign-on.md)。

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>表明表单中的元数据需要重新捕获的迹象

当应用程序更改其 HTML 布局时，你可能需要重新捕获元数据，以便针对所做的更改进行调整。 指示 HTML 布局发生更改的常见症状包括：

- 用户报告了在单击应用程序时会“卡”在登录页中
- 用户报告了用户名或密码未填充

#### <a name="shared-accounts"></a>共享的帐户

从登录的角度来看，使用共享帐户的应用程序并未不同于对单个用户使用密码 SSO 的库应用程序。 但是，在规划和配置要使用共享帐户的应用程序时，还需要执行一些其他的步骤：

1. 与应用程序业务用户合作以记录以下内容：
   1. 组织中要使用应用程序的用户集
   1. 应用程序中与用户集关联的现有凭据集
1. 对于用户集和凭据的每个组合，请根据你的需求在云中或本地创建安全组。
1. 重置共享凭据。 将应用部署到 Azure AD 中以后，个人不需要共享帐户的密码。 由于 Azure AD 会存储密码，因此请考虑将其设置为很长且很复杂的密码。
1. 在应用程序支持的情况下配置密码的自动滚动更新。 这样，即使是进行了初始设置的管理员也不会知道共享帐户的密码。

## <a name="plan-your-authentication-method"></a>规划身份验证方法

选择正确的身份验证方法是设置 Azure AD 混合标识解决方案至关重要的第一个决定。 请实现通过使用 Azure AD Connect（还可在云中预配用户）配置的身份验证方法。

若要选择身份验证方法，需要考虑时间、现有基础结构、复杂性和实现所选内容的成本。 这些因素对每个组织都不同，并可能随时间变化。 你应选择最符合你的特定方案的身份验证方法。 有关详细信息，请参阅[为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法](../hybrid/choose-ad-authn.md)。

## <a name="plan-your-security-and-governance"></a>规划安全和治理

由于随着 BYOD 设备和云应用程序的急剧增加，网络外围变得漏洞越来越多、效率日益低下，因此，标识成为新的安全关注与投资的中心点。

### <a name="plan-access-reviews"></a>规划访问评审

[访问评审](../governance/create-access-review.md)可以使组织有效地管理组成员身份、对企业应用程序的访问权限，以及角色分配。 应该对定期评审用户访问权限一事进行规划，确保仅相应人员能够持续进行访问。

设置访问评审时要针对其进行规划的一些关键主题包括：

1. 确定符合业务需求的访问评审频率。 该频率可以是每周一次、每月一次、每年一次，或视需要而定。

1. 创建用于代表应用访问报表审阅者的组。 你需要确保最熟悉应用及其目标用户和用例的利益干系人是访问评审的参与者

1. 完成访问评审包括撤销不再需要访问权限的用户的应用访问权限。 规划如何处理来自被拒绝用户的潜在支持请求。 被删除的用户会在 Azure AD 中保持被删除状态 30 天，在此期间，管理员可以根据需要还原这些用户。 30 天后，该用户将被永久删除。 使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地永久删除最近删除的用户。

### <a name="plan-auditing"></a>规划审核

Azure AD 提供[包含技术和业务见解的报表](../reports-monitoring/overview-reports.md)。

安全报表和活动报表都可用。 安全报表会显示标记为有风险的用户和有风险的登录。活动报表会详述登录活动并提供所有登录的审核线索，帮助你了解用户在组织中的行为。 你可以使用报表来管理风险、提高工作效率和监视符合性。

| 报告类型 | 访问评审 | 安全报表 | 登录报表 |
|-------------|---------------|------------------|----------------|
| 用于评审 | 应用程序权限和使用情况。 | 可能已遭入侵的帐户 | 谁在访问应用程序 |
| 可能的操作 | 审核访问权限；撤销权限 | 撤销访问权限；强制安全重置 | “撤销访问权限” |

Azure AD 会将大多数审核数据保留 30 天，并通过 Azure 管理门户或 API 提供数据，供你下载到分析系统。

### <a name="consider-using-microsoft-cloud-application-security"></a>考虑使用 Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) 是一种云访问安全代理 (CASB) 解决方案。 它可以让你了解云应用和服务，提供用于识别和防御网络威胁的复杂分析，并且可以让你控制数据传输方式。

部署 MCAS 可以让你：

- 使用 Cloud Discovery 映射并确定组织使用的云环境和云应用。
- 批准和取消批准云中的应用
- 使用那些可利用提供程序 API 且易于部署的应用连接器，以便查看和管控你连接到的应用
- 使用条件访问应用控制保护来实时查看和控制云应用中的访问和活动
- 通过设置策略并不断对其进行微调来实现持续控制。

Microsoft Cloud Application Security (MCAS) 会话控制适用于任何操作系统上任何主要平台中的任何浏览器。 也可阻止或允许移动应用和桌面应用。 通过与 Azure AD 进行原生集成，在 Azure AD 中配置了 SAML 的任何应用或在 Azure AD 中配置了单一登录的 Open ID Connect 应用均可受支持，包括[多个特色应用](/cloud-app-security/proxy-intro-aad)：

有关 MCAS 的信息，请参阅 [Microsoft Cloud App Security 概述](/cloud-app-security/what-is-cloud-app-security)。 MCAS 是基于用户的订阅服务。 可以在 [MCAS 许可数据表](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)中查看许可详细信息。

### <a name="use-conditional-access"></a>使用条件访问

使用条件访问，可以针对云应用自动进行基于条件的访问控制决策。

完成第一因素身份验证后将强制执行条件访问策略。 因此，条件访问不会用作防范拒绝服务 (DoS) 攻击等情况的第一道防线，但可以使用这些事件的信号来确定访问权限。 例如，可以使用登录风险级别、请求的位置，等等。 有关条件访问的详细信息，请参阅[概述](../conditional-access/plan-conditional-access.md)和[部署计划](../conditional-access/plan-conditional-access.md)。

## <a name="azure-sso-technical-requirements"></a>Azure SSO 技术要求

以下部分详细说明了配置特定应用程序的要求，包括所需的环境、终结点、声明映射、所需的属性、证书以及所使用的协议。 在 [Azure AD 门户](https://portal.azure.com/)中配置 SSO 时需要此信息。

### <a name="authentication-mechanism-details"></a>身份验证机制详细信息

对于所有预先集成的 SaaS 应用程序，Microsoft 均提供了教程，因此你不需要此信息。 如果应用程序不在我们的应用程序市场/库中，你可能需要收集以下数据片段：

- **由应用程序用于 SSO 的当前标识提供者（如果适用）** - 例如：AD FS、PingFederate、Okta
- **目标应用程序支持的协议** - 例如 SAML 2.0、OpenID Connect、OAuth、基于表单的身份验证、WS-Fed、WS-Trust
- **使用 Azure AD 配置的协议** - 例如，SAML 2.0 或 1.1、OpenID Connect、OAuth、基于表单的身份验证、WS-Fed

### <a name="attribute-requirements"></a>属性要求

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在一组预先配置的属性和属性映射。 某些应用可管理其他类型的对象，例如组。 请规划从 Azure AD 到应用程序的用户属性映射，并根据业务需求[自定义默认属性映射](../app-provisioning/customize-application-attributes.md)。

### <a name="certificate-requirements"></a>证书要求

应用程序的证书必须是最新的，否则会存在用户无法访问应用程序的风险。 大多数 SaaS 应用程序证书的有效期是 36 个月。 你可以在应用程序边栏选项卡中更改该证书持续时间。 请确保记录过期情况并了解如何管理证书续订。

有两种管理证书的方法。

- **证书自动滚动更新** - Microsoft 支持 [Azure AD 中的签名密钥滚动更新](../develop/active-directory-signing-key-rollover.md)。 虽然这是用于管理证书的首选方法，但并非所有 ISV 都支持此方案。

- **手动更新** - 每个应用程序都有自己的可根据定义方式过期的证书。 在应用程序的证书过期之前，请创建新的证书并将其发送到 ISV。 此信息可从联合元数据拉取。 [在此处阅读有关联合元数据的详细信息。](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>实现 SSO

使用以下阶段来规划和部署组织中的解决方案：

### <a name="user-configuration-for-sso"></a>SSO 的用户配置

- **标识测试用户**

   与应用所有者联系，要求他们在应用程序中创建至少三个测试用户。 确保要用作主标识符的信息已正确填充并且与 Azure AD 中提供的属性匹配。 大多数情况下，这将映射到基于 SAML 的应用程序的“NameID”。 对于 JWT 令牌，它是“preferred_username”。

   在 Azure AD 中创建用户（可以将其作为基于云的用户手动创建，或者使用 Azure AD Connect 同步引擎从本地同步该用户）。 请确保信息与要发送到应用程序的声明匹配。

- **配置 SSO**

   在[应用程序列表](../saas-apps/tutorial-list.md)中，找到并打开适用于你的应用程序的 SSO 教程，然后按照此教程的步骤操作，以便成功配置 SaaS 应用程序。

   如果找不到你的应用程序，请参阅[自定义应用程序文档](./configure-saml-single-sign-on.md)。 这将引导你完成添加不位于 Azure AD 库中的应用程序的过程。

   或者，你可以根据 [Microsoft 的指南文档](../develop/active-directory-claims-mapping.md)，对企业应用程序使用在 SAML 令牌中颁发的声明。 请确保这会映射到你预期在应用程序的 SAML 响应中收到的内容。 如果在配置过程中遇到问题，请使用有关[如何调试 SSO 集成](./debug-saml-sso-issues.md)的指南。

### <a name="provide-sso-change-communications-to-end-users"></a>向最终用户提供 SSO 更改通信

实现通信计划。 请确保你让最终用户知道即将进行更改、何时收到该更改、现在该做什么，以及如何寻求帮助。

### <a name="verify-end-user-scenarios-for-sso"></a>验证 SSO 的最终用户方案

你可以使用以下测试用例在公司拥有的设备和个人设备上执行测试，以确保 SSO 配置按预期方式工作。 以下方案假设用户要导航到应用程序 URL，并完成服务提供商启动的身份验证流（SP 启动的身份验证流）。

| 方案 | SP 启动的身份验证流的预期结果（用户方） |
|----------|---------------------------------------------------|
| 在公司网络上通过 IE 登录到应用程序。 | 出现集成 Windows 身份验证 (IWA)，无其他提示。 |
| 在公司网络外部通过新的登录尝试使用 IE 登录到应用程序。 | AD FS 服务器上基于表单的提示。 用户成功登录，浏览器提示进行 MFA。 |
| 在公司网络外部通过当前会话使用 IE 登录到应用程序，并且从未执行 MFA。 | 用户不会收到有关第一因素的提示。 用户会收到 MFA 提示。 |
| 在公司网络外部通过当前会话使用 IE 登录到应用程序，并且已在此会话中执行 MFA。 | 用户不会收到有关第一因素的提示。 用户不会收到 MFA。 用户通过 SSO 登录到应用程序中。 |
| 在公司网络外部通过当前会话使用 Chrome/Firefox/Safari 登录到应用程序，并且已在此会话中执行 MFA。 | 用户不会收到有关第一因素的提示。 用户不会收到 MFA。 用户通过 SSO 登录到应用程序中。 |
| 在公司网络外部通过新的登录尝试使用 Chrome/Firefox/Safari 登录到应用程序中。 | AD FS 服务器上基于表单的提示。 用户成功登录，浏览器提示进行 MFA。 |
| 在公司网络上通过当前会话使用 Chrome/Firefox 登录到应用程序。 | 用户不会收到有关第一因素的提示。 用户不会收到 MFA。 用户通过 SSO 登录到应用程序中。 |
| 通过新的登录尝试使用移动应用登录到应用程序。 | AD FS 服务器上基于表单的提示。 用户成功登录，ADAL 客户端提示进行 MFA。 |
| 未经授权的用户尝试通过登录 URL 登录到应用程序。 | AD FS 服务器上基于表单的提示。 用户无法使用第一因素登录。 |
| 经过授权的用户尝试登录，但输入的密码不正确。 | 用户导航到应用程序 URL，收到“用户名/密码不正确”错误。 |
| 经过授权的用户单击电子邮件中的链接，并且已经过身份验证。 | 用户单击 URL 就会登录到应用程序，不会有额外的提示。 |
| 经过授权的用户单击电子邮件中的链接，并且尚未经过身份验证。 | 用户单击 URL，系统会提示其使用第一因素进行身份验证。 |
| 经过授权的用户通过新的登录尝试使用移动应用（SP 启动的）登录到应用程序。 | AD FS 服务器上基于表单的提示。 用户成功登录，ADAL 客户端提示进行 MFA。 |
| 未经授权的用户尝试通过登录 URL（SP 启动的）登录到应用程序。 | AD FS 服务器上基于表单的提示。 用户无法使用第一因素登录。 |
| 经过授权的用户尝试登录，但输入的密码不正确。| 用户导航到应用程序 URL，收到“用户名/密码不正确”错误。 |
| 经过授权的用户先注销，然后再次登录。 | 如果配置了注销 URL，用户会从所有服务中注销，系统会提示其进行身份验证。 |
| 经过授权的用户先注销，然后再次登录。 | 如果未配置注销 URL，系统会使用现有 Azure AD 浏览器会话中的现有令牌将用户自动重新登录。 |
| 经过授权的用户单击电子邮件中的链接，并且已经过身份验证。 | 用户单击 URL 就会登录到应用程序，不会有额外的提示。 |
| 经过授权的用户单击电子邮件中的链接，并且尚未经过身份验证。 | 用户单击 URL，系统会提示其使用第一因素进行身份验证。 |

## <a name="manage-sso"></a>管理 SSO

此部分概述了成功管理 SSO 所需遵循的要求和建议。

### <a name="required-administrative-roles"></a>必需的管理角色

始终使用具有可供完成 Azure Active Directory 中所需任务的最少权限的角色。 Microsoft 建议你[查看可用的不同角色](../roles/permissions-reference.md)，并且选择正确的 Azure AD 角色 (role) 来满足你对此应用程序的每个角色 (persona) 的需求。 可能需要暂时应用某些角色，在部署完成后再将其删除。

| 角色| 角色 | Azure AD 角色（如果需要） |
|--------|-------|-----------------------------|
| 咨询台管理员 | 第 1 层支持 | 无 |
| 标识管理员 | 在问题影响 Azure AD 时进行配置和调试 | 全局管理员 |
| 应用程序管理员 | 在应用程序中进行用户证明，为用户配置权限 | 无 |
| 基础结构管理员 | 证书滚动更新所有者 | 全局管理员 |
| 企业主/利益干系人 | 在应用程序中进行用户证明，为用户配置权限 | 无 |

建议使用 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) 来管理角色，以便为具有目录权限的用户提供其他的审核、控制和访问评审。

### <a name="sso-certificate-lifecycle-management"></a>SSO 证书生命周期管理

请务必确定正确的角色和电子邮件通讯组列表，它们的任务是在 Azure AD 和配置了单一登录的应用程序之间管理签名证书的生命周期。 下面是我们建议你采用的一些关键角色：

- 在应用程序中更新用户属性的所有者
- 随时准备提供应用程序中断/修复支持的待命所有者
- 受到密切监视的电子邮件通讯组列表（用于接收与证书相关的更改通知）

证书的最长生存期为三年。 建议你制定一个关于如何处理 Azure AD 与应用程序之间的证书更改的流程。 这有助于防止或最大程度地减少因证书过期或强制执行证书滚动更新而导致的服务中断。

### <a name="rollback-process"></a>回退过程

完成基于测试用例的测试后，即可迁移到应用程序的生产环境。 迁移到生产环境意味着你将在生产租户中实现已规划且已测试的配置，并将其推出到用户。 但是，必须规划好当部署无法按规划进行时要怎样做。 如果 SSO 配置在部署过程中失败，则必须了解如何应对任何服务中断以及如何降低对用户的影响。

应用程序中的身份验证方法的可用性将决定你的最佳策略。 请始终确保你有详细的文档，以便应用所有者确切地了解如何在部署遇到问题时回到原始登录配置状态。

- **如果应用支持多个标识提供者**（例如，LDAP 和 AD FS 和 Ping），请不要在推出期间删除现有的 SSO 配置， 而应在迁移过程中禁用它，因为你以后可能需要将它切换回来。

- **如果应用不支持多个 IDP**，但允许用户使用基于表单的身份验证（用户名/密码）登录，请确保在新 SSO 配置推出失败的情况下，用户可以转而使用此方法。

### <a name="access-management"></a>访问管理

建议在管理对资源的访问权限时选择缩放的方法。 常见方法包括：通过 Azure AD Connect 进行同步以利用本地组、[基于用户属性在 Azure AD 中创建动态组](../enterprise-users/groups-dynamic-membership.md)，或在资源所有者管理的 Azure AD 中创建[自助服务组](../enterprise-users/groups-self-service-management.md)。

### <a name="monitor-security"></a>监视安全性

建议设置一个定期操作计划，以便查看 SaaS 应用安全性的不同方面，并执行任何必需的补救措施。

### <a name="troubleshooting"></a>故障排除

以下链接提供故障排除方案。 你可能需要为支持人员创建具体的指南，其中包含这些方案和修复方案问题的步骤。

#### <a name="consent-issues"></a>同意问题

- [意外的同意错误](./application-sign-in-unexpected-user-consent-prompt.md)

- [“用户同意”错误](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>登录问题

- [从自定义门户登录时出现问题](./application-sign-in-other-problem-access-panel.md)

- [从我的应用进行登录时出现问题](./application-sign-in-other-problem-access-panel.md)

- [应用程序登录页上的错误](./application-sign-in-problem-application-error.md)

- [登录到 Microsoft 应用程序时出现问题](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications"></a>应用程序的 SSO 问题

- [应用程序的密码 SSO 问题](./troubleshoot-password-based-sso.md)

- [登录到基于 SAML 的单一登录配置的应用时出现问题](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)

## <a name="next-steps"></a>后续步骤

[调试基于 SAML 的 SSO](./debug-saml-sso-issues.md)

[通过 PowerShell 为应用进行的声明映射](../develop/active-directory-claims-mapping.md)

[自定义在 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)

[单一登录 SAML 协议](../develop/single-sign-on-saml-protocol.md)

[单一注销 SAML 协议](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md)（面向外部用户，例如合作伙伴和供应商）

[Azure AD 条件访问](../conditional-access/overview.md)

[Azure 标识保护](../identity-protection/overview-identity-protection.md)

[SSO 访问](./what-is-single-sign-on.md)

[应用程序 SSO 教程](../saas-apps/tutorial-list.md)

[白皮书下载 - 单一登录部署计划](https://aka.ms/SSODeploymentPlan)
