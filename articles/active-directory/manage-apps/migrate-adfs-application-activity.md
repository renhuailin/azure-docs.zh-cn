---
title: 使用活动报表将 AD FS 应用移动到 Azure Active Directory
description: Active Directory 联合身份验证服务 (AD FS) 应用程序活动报表可让你快速地将应用程序从 AD FS 迁移到 Azure Active Directory (Azure AD)。 此 AD FS 迁移工具标识与 Azure AD 的兼容性，并提供迁移指南。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 01/14/2019
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: alamaral
ms.openlocfilehash: 00bc79bf0ab078e3a95c958b8c539f453a633e1c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614719"
---
# <a name="review-the-application-activity-report-in-azure-active-directory"></a>查看 Azure Active Directory 中的应用程序活动报表

许多组织使用 Active Directory 联合身份验证服务 (AD FS) 来提供云应用程序的单一登录。 将 AD FS 应用程序迁移到 Azure AD 进行身份验证有很大的优势，特别是在成本管理、风险管理、生产力、合规性和监管方面。 但是，了解与 Azure AD 兼容的应用程序和确定具体的迁移步骤可能很耗时。

利用 Azure 门户中的 AD FS 应用程序活动报表，你可以快速确定哪些应用程序能够迁移到 Azure AD。 它评估所有 AD FS 应用程序与 Azure AD 的兼容性，检查是否存在任何问题，并提供关于为迁移准备各个应用程序的指导。 通过 AD FS 应用程序活动报告，你可以：

* 发现 AD FS 应用程序并确定迁移范围。 AD FS 应用程序活动报表列出在过去 30 天内组织中具有活跃用户登录的所有 AD FS 应用程序。 该报表指示有关迁移到 Azure AD 的应用准备情况。 报表不会显示 AD FS 中的与 Microsoft 相关的信赖方，比如 Office 365。 例如，名为“urn:federation:MicrosoftOnline”的信赖方。

* 确定要迁移的这些应用程序的优先级。 获取过去 1 天、7 天或 30 天内登录到应用程序的唯一用户数，以帮助确定迁移应用程序的关键程度或风险。
* 运行迁移测试并解决问题。 报表服务会自动运行测试，以确定应用程序是否已准备好进行迁移。 结果在 AD FS 应用程序活动报表中显示为迁移状态。 如果 AD FS 配置与 Azure AD 配置不兼容，你将获得有关如何处理 Azure AD 中的配置的具体指导。

AD FS 应用程序活动数据适用于获得下列任意管理角色的用户：全局管理员、报表读者、安全读者、应用程序管理员或云应用程序管理员。

## <a name="prerequisites"></a>先决条件

* 你的组织当前必须使用 AD FS 来访问应用程序。
* 必须在 Azure AD 租户中启用 Azure AD Connect Health。
* 必须安装 Azure AD Connect Health for AD FS 代理。
* [详细了解 Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
* [开始设置 Azure AD Connect Health 并安装 AD FS 代理](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT]
>安装 Azure AD Connect Health 后，有几个原因会导致你看不到预期的所有应用程序。 AD FS 应用程序活动报表仅显示在过去 30 天内有用户登录的 AD FS 信赖方。 此外，这个报表不会显示与 Microsoft 相关的信赖方，如 Office 365。

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>发现可以迁移的 AD FS 应用程序

AD FS 应用程序活动报表可在 Azure 门户的 Azure AD 使用情况和见解报告下获得。 AD FS 应用程序活动报表会分析每个 AD FS 应用程序，以确定是否可以按原样迁移，还是需要进行进一步审阅。

1. 使用有权访问 AD FS 应用程序活动数据的管理员角色（全局管理员、报表读者、安全读者、应用程序管理员或云应用程序管理员）登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“Azure Active Directory”，然后选择“企业应用程序”。

3. 在“活动”下，选择“使用与见解”，然后选择“AD FS 应用程序活动”以打开组织中所有 AD FS 应用程序的列表。

   ![AD FS 应用程序活动](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. 对于 AD FS 应用程序活动列表中的每个应用程序，请查看“迁移状态”：

   * “准备迁移”意味着在 Azure AD 中完全支持 AD FS 应用程序配置，可以按原样迁移。

   * “需要审查”意味着你可以将应用程序的某些设置迁移到 Azure AD，但需要审查不能按原样迁移的设置。

   * “需要其他步骤”意味着 Azure AD 不支持应用程序的某些设置，所以无法在应用程序的当前状态下进行迁移。

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>评估要迁移的应用程序的准备情况

1. 在 AD FS 应用程序活动列表中，单击“迁移状态”列中的状态以打开“迁移详细信息”。 你会看到已通过的配置测试的摘要，以及任何潜在的迁移问题。

   ![迁移详细信息](media/migrate-adfs-application-activity/migration-details.png)

2. 单击消息以打开其他迁移规则详细信息。 有关已测试属性的完整列表，请参阅下面的 [AD FS 应用程序配置测试](#ad-fs-application-configuration-tests)表。

   ![迁移规则详细信息](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS 应用程序配置测试

下表列出了针对 AD FS 应用程序执行的所有配置测试。

|结果  |通过/警告/失败  |说明  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> 至少检测到 AdditionalAuthentication 的一个不可迁移规则。       | 通过/警告          | 信赖方包含用于提示进行多重身份验证 (MFA) 的规则。 要迁移到 Azure AD，需将这些规则转换为条件访问策略。 如果你使用的是本地 MFA，则建议迁移到 Azure AD MFA。 [了解有关条件访问的详细信息](../authentication/concept-mfa-howitworks.md)。        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> 信赖方的 AdditionalWSFedEndpoint 设置为 true。       | 通过/失败          | AD FS 中的信赖方允许多个 WS-Fed 断言终结点。Azure AD 目前仅支持一个这样的断言终结点。如果这种情况导致你无法进行迁移，请[告知我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)。     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> 信赖方已设置 AllowedAuthenticationClassReferences。       | 通过/失败          | 通过 AD FS 中的此设置，你可以指定应用程序是否配置为仅允许某些身份验证类型。 建议使用条件访问来实现此功能。 如果这种情况导致你无法进行迁移，请[告知我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)。  [了解有关条件访问的详细信息](../authentication/concept-mfa-howitworks.md)。          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | 通过/失败          | 通过 AD FS 中的此设置，你可以指定应用程序是否配置为忽略 SSO Cookie 并 **始终提示进行身份验证**。 在 Azure AD 中，可以使用条件访问策略来管理身份验证会话，以实现类似的行为。 [详细了解如何使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。          |
|Test-ADFSRPAutoUpdateEnabled <br> 信赖方已将 AutoUpdateEnabled 设置为 true       | 通过/警告          | AD FS 中的此设置允许你指定是否将 AD FS 配置为基于联合元数据中的更改自动更新应用程序。 Azure AD 目前尚不支持此支持，但不应阻止将应用程序迁移到 Azure AD。           |
|Test-ADFSRPClaimsProviderName <br> 信赖方启用了多个 ClaimsProviders       | 通过/失败          | AD FS 中的此设置将调用信赖方接受声明的标识提供者。 在 Azure AD 中，可以使用 Azure AD B2B 启用外部协作。 [详细了解 Azure AD B2C](../external-identities/what-is-b2b.md)。          |
|Test-ADFSRPDelegationAuthorizationRules      | 通过/失败          | 应用程序定义了自定义委派授权规则。 这是一个 WS-Trust 的概念，Azure AD 借助使用新式身份验证协议（例如 OpenID Connect 和 OAuth 2.0）支持这一概念。 [了解有关 Microsoft 标识平台的详细信息](../develop/v2-protocols-oidc.md)。          |
|Test-ADFSRPImpersonationAuthorizationRules       | 通过/警告          | 应用程序定义了自定义模拟授权规则。这是一个 WS-Trust 的概念，Azure AD 借助使用新式身份验证协议（例如 OpenID Connect 和 OAuth 2.0）支持这一概念。 [了解有关 Microsoft 标识平台的详细信息](../develop/v2-protocols-oidc.md)。          |
|Test-ADFSRPIssuanceAuthorizationRules <br> 至少检测到 IssuanceAuthorization 的一个不可迁移规则。       | 通过/警告          | 应用程序具有在 AD FS 中定义了自定义颁发授权规则。Azure AD 通过 Azure AD 条件访问支持此功能。 [了解有关条件访问的详细信息。](../conditional-access/overview.md) <br> 你还可以通过分配给应用程序的用户或组来限制对应用程序的访问。 [详细了解如何分配用户和组以访问应用程序](./assign-user-or-group-access-portal.md)。            |
|Test-ADFSRPIssuanceTransformRules <br> 至少检测到 IssuanceTransform 的一个不可迁移规则。       | 通过/警告          | 应用程序具有在 AD FS 中定义的自定义颁发转换规则。 Azure AD 支持自定义令牌中颁发的声明。 要了解详细信息，请参阅[为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。           |
|Test-ADFSRPMonitoringEnabled <br> 信赖方将 MonitoringEnabled 设置为 true。       | 通过/警告          | AD FS 中的此设置允许你指定是否将 AD FS 配置为基于联合元数据中的更改自动更新应用程序。 Azure AD 目前尚不支持此支持，但不应阻止将应用程序迁移到 Azure AD。           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | 通过/警告          | AD FS 根据 SAML 令牌中的 NotBefore 和 NotOnOrAfter 时间允许时间偏差。 默认情况下，Azure AD 会自动处理这种情况。          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> 信赖方将 RequestMFAFromClaimsProviders 设置为 true。       | 通过/警告          | AD FS 中的这项设置确定当用户来自不同声明提供程序时 MFA 的行为。 在 Azure AD 中，可以使用 Azure AD B2B 启用外部协作。 然后，你可以应用条件访问策略来保护来宾访问权限。 详细了解 [AZURE AD B2B](../external-identities/what-is-b2b.md) 和[条件访问](../conditional-access/overview.md)。          |
|Test-ADFSRPSignedSamlRequestsRequired <br> 信赖方已将 SignedSamlRequestsRequired 设置为 true       | 通过/失败          | 在 AD FS 中配置该应用程序以验证 SAML 请求中的签名。 Azure AD 接受签名的 SAML 请求；但 Azure AD 不会验证签名。 Azure AD 具有不同的方法来防范恶意调用。 例如，Azure AD 使用在应用程序中配置的回复 URL 来验证 SAML 请求。 Azure AD 只会将令牌发送到为应用程序配置的回复 URL。 如果这种情况导致你无法进行迁移，请[告知我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)。          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | 通过/警告         | 应用程序配置为使用自定义令牌生存期。 AD FS 默认值为一小时。Azure AD 利用条件访问支持此功能。 要了解详细信息，请参阅[使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。          |
|信赖方设置为加密声明。 Azure AD 支持加密声明       | 通过          | 使用 Azure AD 可以加密发送到应用程序的令牌。 要了解详细信息，请参阅[配置 AZURE AD SAML 令牌加密](./howto-saml-token-encryption.md)。          |
|EncryptedNameIdRequiredCheckResult      | 通过/失败          | 应用程序配置为加密 SAML 令牌中的 nameID 声明。使用 Azure AD 可以加密发送到应用程序的整个令牌。尚不支持加密特定声明。 要了解详细信息，请参阅[配置 AZURE AD SAML 令牌加密](./howto-saml-token-encryption.md)。         |

## <a name="check-the-results-of-claim-rule-tests"></a>检查声明规则测试的结果

如果已为 AD FS 中的应用程序配置了声明规则，则体验将为所有声明规则提供精细分析。 你将看到哪些声明规则可以迁移到 Azure AD，以及哪些声明规则需要进一步审阅。

1. 在 AD FS 应用程序活动列表中，单击“迁移状态”列中的状态以打开“迁移详细信息”。 你会看到已通过的配置测试的摘要，以及任何潜在的迁移问题。

2. 在“迁移规则详细信息”页上，展开结果以显示有关潜在迁移问题的详细信息，并获取其他指导。 有关已测试的所有声明规则的详细列表，请参阅下面的[查看声明规则测试结果](#check-the-results-of-claim-rule-tests)表。

   下面的示例显示了 IssuanceTransform 规则的迁移规则详细信息。 这个示例列出了在能够将应用程序迁移到 Azure AD 之前需要审阅和解决声明的哪些具体部分。

   ![迁移规则详细介绍了其他指导](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>声明规则测试

下表列出了对 AD FS 应用程序执行的所有声明规则测试。

|属性  |说明  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | 条件语句使用正则表达式来计算声明是否与特定模式匹配。要在 Azure AD 中实现类似的功能，可以使用预定义的转换，如 IfEmpty()、StartWith ()、Contains() 等等。 有关详细信息，请参阅[为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。          |
|UNSUPPORTED_CONDITION_CLASS      | 条件语句包含多个需要在运行颁发语句之前计算的条件。Azure AD 可以通过声明的转换函数（可在其中计算多个声明值）支持此功能。有关详细信息，请参阅[为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。          |
|UNSUPPORTED_RULE_TYPE      | 无法识别声明规则。 有关如何在 Azure AD 中配置链的详细信息，请参阅[为业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | 条件语句使用 Azure AD 中不支持的颁发者。目前，Azure AD 不能从 Active Directory 或 Azure AD 以外的存储中获取声明。 如果这个问题导致你无法迁移到 Azure AD，请[告知我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。         |
|UNSUPPORTED_CONDITION_FUNCTION      | 条件语句使用聚合函数发出或添加单个声明，无论匹配项的数目如何。在 Azure AD 中，你可以使用  IfEmpty()、StartWith()、Contains() 等函数来计算用户的属性，以确定要用于声明的值。有关详细信息，请参阅[为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。          |
|RESTRICTED_CLAIM_ISSUED      | 条件语句使用 Azure AD 中限制的声明。 你可以颁发受限声明，但不能修改其源，也不能应用任何转换。 有关更多信息，请参阅[自定义令牌中为 Azure AD 特定应用发出的声明](../develop/active-directory-claims-mapping.md)。          |
|EXTERNAL_ATTRIBUTE_STORE      | 颁发语句使用不同于 Active Directory 的属性存储。 目前，Azure AD 不能从 Active Directory 或 Azure AD 以外的存储中获取声明。 如果这个问题导致你无法迁移到 Azure AD，请[告知我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。          |
|UNSUPPORTED_ISSUANCE_CLASS      | 颁发语句使用 ADD 向传入声明集添加声明。 在 Azure AD 中，这可能配置为多个声明转换。有关详细信息，请参阅[为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-claims-mapping.md)。         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 此颁发语句使用正则表达式来转换要发出的声明的值。要在 Azure AD 中实现类似的功能，可以使用预定义的转换，如 Extract()、Trim()、ToLower 等等。 有关详细信息，请参阅[为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。          |

## <a name="troubleshooting"></a>故障排除

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>在报表中没有看到我的所有 AD FS 应用程序

 如果你已安装 Azure AD Connect Health，但仍看到安装提示，或在报表中无法看到所有 AD FS 应用程序，则可能是你的 AD FS 应用程序不活动，或者你的 AD FS 应用程序是 Microsoft 应用程序。

 AD FS 应用程序活动报表列出了组织中过去 30 天内具有活动用户登录的所有 AD FS 应用程序。 此外，报表不会显示 AD FS 中的与 Microsoft 相关的信赖方，比如 Office 365。 例如，名为“urn:federation:MicrosoftOnline”、“microsoftonline”、“microsoft:winhello:cert:prov:server”的信赖方不会显示在列表中。

## <a name="next-steps"></a>后续步骤

* [视频：如何使用 AD FS 活动报表来迁移应用程序](https://www.youtube.com/watch?v=OThlTA239lU)
* [使用 Azure Active Directory 管理应用程序](what-is-application-management.md)
* [管理对应用的访问权限](what-is-access-management.md)
* [Azure AD Connect 联合身份验证](../hybrid/how-to-connect-fed-whatis.md)
