---
title: Azure AD 身份验证和授权错误代码
description: 了解 Azure AD 安全令牌服务 (STS) 返回的 AADSTS 错误代码。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 07/28/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8492b35fae2d2c2d716330002d5f889ec693f8c5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353374"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD 身份验证和授权错误代码

想要查找有关 Azure Active Directory (Azure AD) 安全令牌服务 (STS) 返回的 AADSTS 错误代码的信息？ 请阅读本文档来查找 AADSTS 错误说明、修复方法和一些建议的解决方法。

> [!NOTE]
> 本文中的信息属于初步信息，随时可能更改。 遇到了问题或者找不到所需的内容？ 请创建 GitHub 问题，或查看[面向开发人员的支持和帮助选项](./developer-support-help-options.md)来了解其他可以获得帮助和支持的方法。
>
> 本文档是为开发者和管理员提供的指导，但决不应当被客户自己使用。 错误代码可能会随时更改，以便提供更详细的错误消息，以在开发者构建应用程序时为其提供帮助。 依赖于文本或错误代码的应用程序随着时间的推移将会损坏。

## <a name="handling-error-codes-in-your-application"></a>处理应用程序中的错误代码

[OAuth 2.0 规范](https://tools.ietf.org/html/rfc6749#section-5.2)介绍如何在身份验证期间使用错误响应的 `error` 部分处理错误。 

下面是一个错误响应示例：

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| 参数         | 说明    |
|-------------------|----------------|
| `error`       | 一个错误码字符串，可用来对发生的错误类型进行分类，并应当用来响应错误。 |
| `error_description` | 帮助开发人员识别身份验证错误根本原因的特定错误消息。 绝对不要使用此字段来响应代码中的错误。 |
| `error_codes` | 可帮助诊断的 STS 特定错误代码列表。  |
| `timestamp`   | 发生错误的时间。 |
| `trace_id`    | 可帮助诊断的请求唯一标识符。 |
| `correlation_id` | 可帮助跨组件诊断的请求唯一标识符。 |
| `error_uri` |  指向错误查找页面的链接，该页面中包含有关错误的其他信息。  这仅供开发人员使用，不向用户提供。  仅当错误查找系统具有相关错误的其他信息时才提供 - 并非所有错误都提供了其他信息。|

`error` 字段有多个可能值 - 请查看协议文档链接和 OAuth 2.0 规范来详细了解特定错误（例如，[设备代码流](v2-oauth2-device-code.md)中的 `authorization_pending`）以及如何响应它们。  下面列出了一些常见错误：

| 错误代码         | 说明        | 客户端操作    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 协议错误，例如，缺少必需的参数。 | 修复并重新提交请求。|
| `invalid_grant`    | 某些身份验证材料（身份验证代码、刷新令牌、访问令牌、PKCE 质询）无效、无法分析、缺失或在其他方面无法使用 | 尝试对 `/authorize` 终结点发出新请求来获取新的授权代码。  考虑查看和验证应用程序对协议的使用。 |
| `unauthorized_client` | 经过身份验证的客户端无权使用此权限授予类型。 | 客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现这种情况。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| `invalid_client` | 客户端身份验证失败。  | 客户端凭据无效。 若要修复，应用程序管理员应更新凭据。   |
| `unsupported_grant_type` | 授权服务器不支持权限授予类型。 | 更改请求中的授权类型。 这种类型的错误应该只在开发过程中发生，并且应该在初始测试过程中检测到。 |
| `invalid_resource` | 目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 | 这表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。  在开发过程中，这通常表示错误地设置了测试租户，或者在所请求范围的名称中有拼写错误。 |
| `interaction_required` | 请求需要用户交互。 例如，需要额外的身份验证步骤。 | 请以交互方式用同一资源重试请求，以便用户能够完成所需的任何质询。  |
| `temporarily_unavailable` | 服务器暂时繁忙，无法处理请求。 | 重试请求。 客户端应用程序可向用户说明，其响应由于临时状况而延迟。 |

## <a name="lookup-current-error-code-information"></a>查找当前错误代码信息
错误代码和消息可能会更改。  有关最新信息，请查看 [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) 页，以查找 AADSTS 错误说明、修复程序和一些建议的解决方法。  

例如，如果收到错误代码“AADSTS50058”，则在 [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) 中搜索“50058”。  还可以通过将错误代码编号添加到 URL [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) 来直接链接到特定错误。

## <a name="aadsts-error-codes"></a>AADSTS 错误代码

| 错误 | 说明 |
|---|---|
| AADSTS16000 | SelectUserAccount - 这是 Azure AD 引发的中断，使得 UI 允许用户从多个有效 SSO 会话中进行选择。 此错误相当常见。如果指定了 `prompt=none`，可能会在应用程序中返回此错误。 |
| AADSTS16001 | UserAccountSelectionInvalid - 如果用户单击会话选择逻辑已拒绝的某个磁贴，则会出现此错误。 触发此错误时，用户可以从更新的磁贴/会话列表中进行选择或选择另一个帐户进行恢复。 此错误的原因可能是代码缺陷或出现争用状况。 |
| AADSTS16002 | AppSessionSelectionInvalid - 不符合应用指定的 SID 要求。  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - 指示尚未显式将用户添加到租户。 |
| AADSTS17003 | CredentialKeyProvisioningFailed - Azure AD 无法预配用户密钥。 |
| AADSTS20001 | WsFedSignInResponseError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS20012 | WsFedMessageInvalid - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS20033 | FedMetadataInvalidTenantName - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS28002 | 请求访问令牌时，为输入参数范围“{scope}”提供的值无效。 请指定有效范围。 |
| AADSTS28003 | 当使用提供的授权代码请求访问令牌时，为输入参数范围提供的值不能为空。 请指定有效范围。|
| AADSTS40008 | OAuth2IdPUnretryableServerError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS40010 | OAuth2IdPRetryableServerError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS50000 | TokenIssuanceError - 登录服务出现问题。 请[开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)以解决此问题。 |
| AADSTS50001 | InvalidResource - 资源已禁用或不存在。 请检查应用代码，确保为尝试访问的资源指定了确切的资源 URL。  |
| AADSTS50002 | NotAllowedTenant - 由于租户中的代理访问权限受限，登录失败。 如果这是你自己的租户策略，可以更改受限的租户设置来解决此问题。 |
| AADSTS500021 | 拒绝访问“{tenant}”租户。 AADSTS500021 指示已配置租户限制功能，并且用户正尝试访问标头 `Restrict-Access-To-Tenant` 指定的允许租户列表中没有的租户。 有关详细信息，请参阅[使用租户限制管理对 SaaS 云应用程序的访问](../manage-apps/tenant-restrictions.md)。|
| AADSTS50003 | MissingSigningKey - 由于缺少签名密钥或证书，登录失败。 这可能是因为应用中未配置任何签名密钥。 若要了解详细信息，请参阅“故障排除”一文以了解错误 [AADSTS50003](/troubleshoot/azure/active-directory/error-code-aadsts50003-cert-or-key-not-configured)。 如果仍然出现问题，请联系应用所有者或应用管理员。 |
| AADSTS50005 | DevicePolicyError - 用户尝试从条件访问策略目前不支持的平台登录到设备。 |
| AADSTS50006 | InvalidSignature - 由于签名无效，签名验证失败。 |
| AADSTS50007 | PartnerEncryptionCertificateMissing - 未找到此应用的合作伙伴加密证书。 请向 Microsoft [开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)以解决此问题。 |
| AADSTS50008 | InvalidSamlToken - SAML 断言在令牌中缺失或配置错误。 请联系联合提供者。 |
| AADSTS50010 | AudienceUriValidationFailed - 由于未配置令牌受众，应用的受众 URI 验证失败。 |
| AADSTS50011 | InvalidReplyTo - 回复地址缺失、配置错误或者与为应用配置的回复地址不匹配。  作为一种解决方法，请确保将此缺失的回复地址添加到 Azure Active Directory 应用程序，或者让有权在 Active Directory 中管理你的应用程序的人为你执行此操作。 若要了解详细信息，请参阅“故障排除”一文以了解错误 [AADSTS50011](/troubleshoot/azure/active-directory/error-code-aadsts50011-reply-url-mismatch)。|
| AADSTS50012 | AuthenticationFailed - 身份验证由于以下原因之一而失败：<ul><li>未授权签名证书的使用者名称</li><li>找不到与已授权使用者名称匹配的受信任颁发机构策略</li><li>证书链无效</li><li>签名证书无效</li><li>未在租户中配置策略</li><li>未授权签名证书的指纹</li><li>客户端断言包含无效的签名</li></ul> |
| AADSTS50013 | InvalidAssertion - 多种原因导致断言无效 - 令牌颁发者与令牌有效时间范围内的 API 版本不匹配 - 已过期 - 格式不正确 - 断言中的刷新令牌不是主要刷新令牌。 |
| AADSTS50014 | GuestUserInPendingState - 用户兑换处于挂起状态。 尚未完全创建来宾用户帐户。 |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - 用户需要法定年龄组许可。 |
| AADSTS50017 | CertificateValidationFailed - 证书验证失败，原因如下：<ul><li>在受信任的证书列表中找不到颁发证书</li><li>找不到所需的 CrlSegment</li><li>在受信任的证书列表中找不到颁发证书</li><li>在没有对应 CRL 分发点的情况下配置了增量 CRL 分发点</li><li>由于超时问题，无法检索有效的 CRL 段</li><li>无法下载 CRL</li></ul>请联系租户管理员。 |
| AADSTS50020 | UserUnauthorized - 未授权用户调用此终结点。 |
| AADSTS50027 | InvalidJwtToken - 以下原因导致 JWT 令牌无效：<ul><li>不包含 nonce 声明和子声明</li><li>使用者标识符不匹配</li><li>idToken 声明中存在重复声明</li><li>意外的颁发者</li><li>意外的受众</li><li>不在有效的时间范围内 </li><li>令牌格式不正确</li><li>颁发者的外部 ID 令牌未通过签名验证。</li></ul> |
| AADSTS50029 | URI 无效 - 域名包含无效字符。 请联系租户管理员。 |
| AADSTS50032 | WeakRsaKey - 指示错误的用户尝试使用弱 RSA 密钥。 |
| AADSTS50033 | RetryableError - 指示与数据库操作不相关的暂时性错误。 |
| AADSTS50034 | UserAccountNotFound - 若要登录到此应用程序，必须将帐户添加到目录中。 |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - 原则中缺少用于生成成对标识符的盐。 请联系租户管理员。 |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - 使用者与客户端断言中的颁发者声明不匹配。 请联系租户管理员。 |
| AADSTS50049 | NoSuchInstanceForDiscovery - 未知或无效的实例。 |
| AADSTS50050 | MalformedDiscoveryRequest - 请求格式不正确。 |
| AADSTS50053 | 两种不同的原因会导致此错误： <br><ul><li>IdsLocked - 帐户已锁定，因为用户尝试使用不正确的用户 ID 或密码登录的次数过多。 用户因反复登录尝试而被阻止。 请参阅[修正风险并对用户解除阻止](../identity-protection/howto-identity-protection-remediate-unblock.md)。</li><li>也有可能是由于其来自涉及恶意活动的 IP 地址而导致登录遭到阻止。</li></ul> <br>要确定导致此错误的失败原因，请登录到 [Azure 门户](https://portal.azure.com)。  导航到你的 Azure AD 租户，然后选择“监视” -> “登录”。找到登录错误代码为 50053 的失败用户登录，并查看失败原因   。|
| AADSTS50055 | InvalidPasswordExpiredPassword - 密码已过期。 用户的密码已过期，因此其登录名或会话已结束。 他们有机会重置密码，或者可能要求管理员通过[使用 Azure Active Directory 重置用户密码](../fundamentals/active-directory-users-reset-password-azure-portal.md)来重置密码。 |
| AADSTS50056 | 密码无效或为 null：密码在此用户的目录中不存在。 应要求用户再次输入其密码。 |
| AADSTS50057 | UserDisabled - 用户帐户处于禁用状态。 Active Directory 中支持此帐户的用户对象已禁用。 管理员可以通过 [Powershell](/powershell/module/activedirectory/enable-adaccount) 重新启用此帐户 |
| AADSTS50058 | UserInformationNotProvided - 会话信息不足以进行单一登录。 这意味着用户未登录。 这是一个常见的错误，如果用户未经过身份验证并且尚未登录，则预期会出现此错误。</br>如果在用户之前已登录的 SSO 上下文中遇到此错误，这表示 SSO 会话未找到或无效。</br>如果指定了 prompt=none，则可能会在应用程序中返回此错误。 |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - 在请求中未找到租户标识信息，或者任何提供的凭据未隐式指定此信息。 用户可以联系租户管理员来帮助解决此问题。 |
| AADSTS50061 | SignoutInvalidRequest - 无法完成注销。 请求无效。 |
| AADSTS50064 | CredentialAuthenticationError - 用户名或密码凭据验证失败。 |
| AADSTS50068 | SignoutInitiatorNotParticipant - 注销失败。 发起注销的应用不是当前会话中的参与者。 |
| AADSTS50070 | SignoutUnknownSessionIdentifier - 注销失败。 注销请求指定了与现有会话不匹配的名称标识符。 |
| AADSTS50071 | SignoutMessageExpired - 注销请求已过期。 |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - 用户需要注册双重身份验证（交互式）。 |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - 需要强身份验证，用户未通过 MFA 质询。 |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - 由于管理员做了配置更改，或者你已移到新位置，用户必须使用多重身份验证来访问资源。 请使用针对资源的新授权请求重试。 |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - 由于管理员做了配置更改，或者用户已移到新位置，该用户需要使用多重身份验证。 |
| AADSTS50085 | 刷新令牌需要社交 IDP 登录。 请让用户尝试使用用户名和密码再次登录 |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - 强身份验证期间出现暂时性错误。 请重试。 |
| AADSTS50088 | 已达到电信 MFA 呼叫限制。 请过几分钟重试。 |
| AADSTS50089 | 由于流令牌过期，身份验证失败。 预期 - 身份验证代码、刷新令牌和会话会随着时间的推移而过期，或者由用户或管理员撤销。应用将请求用户进行新登录。 |
| AADSTS50097 | DeviceAuthenticationRequired - 必须使用设备身份验证。 |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - JWT 签名无效。 |
| AADSTS50105 | EntitlementGrantsNotFound - 未向已登录用户分配已登录应用的角色。 请将该用户分配到该应用。 若要了解详细信息，请参阅“故障排除”一文以了解错误 [AADSTS50105](/troubleshoot/azure/active-directory/error-code-aadsts50105-user-not-assigned-role)。 |
| AADSTS50107 | InvalidRealmUri - 请求的联合领域对象不存在。 请联系租户管理员。 |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - JWT 标头有问题。 请联系租户管理员。 |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - 声明转换包含无效的输入参数。 请联系租户管理员来更新策略。 |
| AADSTS501241 | 转换 ID“{transformId}”中缺少必填的输入“{paramName}”。 Azure AD 尝试向应用程序生成 SAML 响应时，将返回此错误。 NameID 声明或 NameIdentifier 在 SAML 响应中是必填内容，如果 Azure AD 无法获取 NameID 声明的源属性，它将返回此错误。 对于解决方案，确保在“Azure 门户”>“Azure Active Directory”>“企业应用程序”>“选择应用程序”>“单一登录”>“用户标识符和声明”>“唯一用户标识符(Name ID)”中添加声明规则。  |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - 密码重置或密码注册条目导致登录中断。 |
| AADSTS50126 | InvalidUserNameOrPassword - 无效的用户名或密码导致验证凭据时出错。 |
| AADSTS50127 | BrokerAppNotInstalled - 用户需要安装中转站应用才能访问此内容。 |
| AADSTS50128 | 域名无效 - 未在请求中找到或提供的任何凭据均未暗示任何租户标识信息。 |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - 需要加入工作区才能注册设备。 |
| AADSTS50131 | ConditionalAccessFailed - 指示各种条件访问错误，例如，Windows 设备状态不正确，请求因活动可疑、访问策略和安全策略决策而被阻止。 |
| AADSTS50132 | SsoArtifactInvalidOrExpired - 会话由于密码过期或最近更改了密码而无效。 |
| AADSTS50133 | SsoArtifactRevoked - 会话由于密码过期或最近更改了密码而无效。 |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - 错误的数据中心。 若要授权 OAuth 2.0 设备流中的应用发起的请求，授权方必须与原始请求位于同一数据中心。 |
| AADSTS50135 | PasswordChangeCompromisedPassword - 由于帐户风险，需要更改密码。 |
| AADSTS50136 | RedirectMsaSessionToApp - 检测到单个 MSA 会话。 |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - 由于缺少外部刷新令牌，会话无效。 |
| AADSTS50140 | KmsiInterrupt - 此错误是由于用户登录时出现“使我保持登录状态”中断而发生的。 这是登录流的预期部分，其中会询问用户是否希望保持登录到当前浏览器，以便以后更轻松地登录。 有关详细信息，请参阅[现已推出新的 Azure AD 登录和“使我保持登录状态”体验！](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/the-new-azure-ad-sign-in-and-keep-me-signed-in-experiences/m-p/128267)。 你可以[开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)并提供相关性 ID、请求 ID 和错误代码，以获取更多详细信息。|
| AADSTS50143 | 会话不匹配 - 会话无效，因为不同的资源导致用户租户与域提示不匹配。 [开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)并提供相关性 ID、请求 ID 和错误代码，以获取更多详细信息。 |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - 用户的 Active Directory 密码已过期。 为用户生成新密码，或者让用户使用自助重置工具重置其密码。 |
| AADSTS50146 | MissingCustomSigningKey - 需要为此应用配置特定于应用的签名密钥。 没有为此应用程序配置签名密钥，或者密钥已过期或尚未生效。 |
| AADSTS50147 | MissingCodeChallenge - 代码质询参数的大小无效。 |
| AADSTS501481 | Code_Verifier 与授权请求中提供的 code_challenge 不匹配。|
| AADSTS50155 | DeviceAuthenticationFailed - 此用户的设备身份验证失败。 |
| AADSTS50158 | ExternalSecurityChallenge - 不符合外部安全质询。 |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - 外部提供程序发送的声明不够，或者向外部提供程序请求的声明缺失。 |
| AADSTS50166 | ExternalClaimsProviderThrottled - 无法将请求发送到声明提供程序。 |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - 客户端能够通过 Windows 10 帐户扩展获取 SSO 令牌，但在请求中找不到令牌，或提供的令牌已过期。 |
| AADSTS50169 | InvalidRequestBadRealm - 领域不是当前服务命名空间的已配置领域。 |
| AADSTS50170 | MissingExternalClaimsProviderMapping - 缺少外部控制映射。 |
| AADSTS50173 | FreshTokenNeeded - 所提供的授权因被撤销而过期，需要一个新的身份验证令牌。 管理员或用户撤销了该用户的令牌，导致后续的令牌刷新失败，需要重新进行身份验证。 让用户重新登录。 |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - 直通用户不支持外部质询。 |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - 直通用户不支持会话控制。 |
| AADSTS50180 | WindowsIntegratedAuthMissing - 需要 Windows 集成身份验证。 为租户启用无缝 SSO。 |
| AADSTS50187 | DeviceInformationNotProvided - 服务无法执行设备身份验证。 |
| AADSTS50194 | 应用程序“{appId}”({appName})未配置为多租户应用程序。 在“{time}”之后创建的此类应用程序不支持使用 /common 终结点。 使用特定于租户的终结点，或将应用程序配置为多租户。 |
| AADSTS50196 | LoopDetected - 检测到客户端循环。 检查应用的逻辑，以确保实现了令牌缓存，并且正确处理了错误情况。  该应用在太短的时间内发出了太多相同请求，表明它处于错误状态或滥用请求令牌。 |
| AADSTS50197 | ConflictingIdentities - 找不到用户。 请尝试再次登录。 |
| AADSTS50199 | CmsiInterrupt - 出于安全原因，此请求需要用户确认。  由于这是“interaction_required”错误，因此客户端应进行交互式身份验证。之所以发生这种情况，是因为系统 Web 视图已用于请求本机应用程序的令牌，必须提示用户询问此应用是否确实为他们要登录的应用。 要避免此提示，重定向 URI 应是以下安全列表的一部分： <br />http://<br />https://<br />msauth://（仅限 iOS）<br />msauthv2://（仅限 iOS）<br />chrome-extension://（仅限桌面 Chrome 浏览器） |
| AADSTS51000 | RequiredFeatureNotEnabled - 已禁用该功能。 |
| AADSTS51001 | DomainHintMustbePresent - 必须使用本地安全标识符或本地 UPN 提供域提示。 |
| AADSTS51004 | UserAccountNotInDirectory - 目录中不存在该用户帐户。 |
| AADSTS51005 | TemporaryRedirect - 等效于 HTTP 状态 307，表示请求的信息位于 location 标头中指定的 URI 处。 如果收到此状态，请遵循与响应关联的 location 标头操作。 如果原始请求方法是 POST，则重定向的请求也会使用 POST 方法。 |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - 需要 Windows 集成身份验证。 用户已使用缺少集成 Windows 身份验证声明的会话令牌登录。 请求用户重新登录。 |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - 用户未许可访问 LinkedIn 资源。 |
| AADSTS53000 | DeviceNotCompliant - 条件访问策略需要合规的设备，该设备不合规。 用户必须使用已批准的 MDM 提供程序（例如 Intune）注册其设备。 |
| AADSTS53001 | DeviceNotDomainJoined - 条件访问策略需要已加入域的设备，而该设备未加入域。 让用户使用已加入域的设备。 |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - 使用的应用不是批准用于条件访问的应用。 用户需使用可用的获批准应用列表中的某个应用才能获取访问权限。 |
| AADSTS53003 | BlockedByConditionalAccess - 条件访问策略已阻止访问。 访问策略不允许令牌颁发。 |
| AADSTS53004 | ProofUpBlockedDueToRisk - 在访问此内容之前，用户需要完成多重身份验证注册过程。 用户应注册多重身份验证。 |
| AADSTS53011 | 用户因主租户的风险而被阻止。 |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS54005 | OAuth2 授权代码已兑换，请使用新的有效代码重试，或使用现有的刷新令牌。 |
| AADSTS65001 | DelegationDoesNotExist - 用户或管理员尚未许可将应用程序与 ID X 配合使用。请发送针对该用户和资源的交互式授权请求。 |
| AADSTS65002 | 必须通过预授权在第一方应用程序“{applicationId}”与第一方资源“{resourceId}”之间配置同意 - 由 Microsoft 拥有和操作的应用程序必须先获得 API 所有者的批准，然后才能请求该 API 的令牌。  租户中的某位开发人员可能正在尝试重用 Microsoft 拥有的应用 ID。 此错误会导致他们无法模拟 Microsoft 应用程序来调用其他 API。 他们必须移动到在 https://portal.azure.com 中注册的另一个应用 ID。|
| AADSTS65004 | UserDeclinedConsent - 用户已拒绝许可访问该应用。 让用户重试登录并许可应用|
| AADSTS65005 | MisconfiguredApplication - 应用所需的资源访问列表不包含可以通过资源来发现的应用，或者客户端应用请求访问的资源未在其必需的资源访问列表中指定，或者 Graph 服务返回了错误的请求，或者资源找不到。 如果应用支持 SAML，则原因可能是使用错误的标识符（实体）配置了应用。 若要了解详细信息，请参阅“故障排除”一文以了解错误 [AADSTS650056](/troubleshoot/azure/active-directory/error-code-aadsts650056-misconfigured-app)。 |
| AADSTS650052 | 应用需要访问你的组织 `\"{organization}\"` 尚未订阅或启用的服务 `(\"{name}\")`。 若要查看服务订阅的配置，请与 IT 管理员联系。 |
| AADSTS650054 |  应用程序请求访问已删除或不再可用的资源的权限。 确保应用调用的所有资源都存在于你正在操作中的租户中。 |
| AADSTS650056 | 应用程序配置不正确。 这可能由以下原因之一造成：在客户端的应用程序注册中，客户端没有在所请求的权限中列出针对“{name}”的任何权限。 或者，管理员未在租户中表示同意。 或者，检查请求中的应用程序标识符，确保它与配置的客户端应用程序标识符匹配。 或者，检查请求中的证书，确保该证书有效。 请联系你的管理员来修复配置或者代表租户来表示同意。 客户端应用 ID：{id}。 请联系你的管理员来修复配置或者代表租户来表示同意。|
| AADSTS650057 | 资源无效。 客户端已请求访问某个资源，但该资源未在客户端应用程序注册中的所需权限中列出。 客户端应用 ID：{appId}({appName})。 请求中的资源值：{resource}。 资源应用 ID：{resourceAppId}。 应用注册中的有效资源列表：{regList}。 |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - 身份验证失败。 刷新令牌无效。 该错误的可能原因如下：<ul><li>令牌绑定标头为空</li><li>令牌绑定哈希不匹配</li></ul> |
| AADSTS70001 | UnauthorizedClient - 应用程序处于禁用状态。 若要了解详细信息，请参阅“故障排除”一文以了解错误 [AADSTS70001](/troubleshoot/azure/active-directory/error-code-aadsts70001-app-not-found-in-directory)。 |
| AADSTS70002 | InvalidClient - 验证凭据时出错。 指定的 client_secret 与此客户端的预期值不匹配。 请更正 client_secret，然后重试。 有关详细信息，请参阅[使用授权代码请求访问令牌](v2-oauth2-auth-code-flow.md#redeem-a-code-for-an-access-token)。 |
| AADSTS70003 | UnsupportedGrantType - 应用返回了不受支持的授权类型。 |
| AADSTS700030 | 证书无效 - 证书中的使用者名称未获授权。 令牌证书中的 SubjectNames/SubjectAlternativeNames（最多 10 个）为：{certificateSubjects}。 |
| AADSTS70004 | InvalidRedirectUri - 应用返回了无效的重定向 URI。 客户端指定的重定向地址与配置的任何地址或者 OIDC 批准列表中的任何地址都不匹配。 |
| AADSTS70005 | UnsupportedResponseType - 由于以下原因，应用返回了不受支持的响应类型：<ul><li>没有为应用启用响应类型“token”</li><li>响应类型“id_token”需要“OpenID”作用域 - 编码的 wctx 中包含不支持的 OAuth 参数值</li></ul> |
| AADSTS700054 | 没有为应用程序启用响应类型“id_token”。  应用程序从授权终结点请求了 ID 令牌，但未启用 ID 令牌隐式授权。  转到“Azure 门户”>“Azure Active Directory”>“应用注册”>“选择你的应用程序”>“身份验证”>在“隐式授权和混合流”下，确保“ID 令牌”处于选中状态。|
| AADSTS70007 | UnsupportedResponseMode - 请求令牌时，应用返回了不受支持的 `response_mode` 值。  |
| AADSTS70008 | ExpiredOrRevokedGrant - 刷新令牌由于非活动状态而过期。 该令牌是在 XXX 颁发的，并在特定的时间内处于非活动状态。 |
| AADSTS700084 | 刷新令牌已颁发给单页应用 (SPA)，因此具有固定、有限的生命周期 {time}，该生命周期无法延长。 刷新令牌现在已经过期，SPA 必须将新的登录请求发送到登录页面。 该令牌于 {issueDate} 颁发。|
| AADSTS70011 | InvalidScope - 应用请求的范围无效。 |
| AADSTS70012 | MsaServerError - 对 MSA（使用者）用户进行身份验证时发生服务器错误。 重试。 如果仍然失败，请[开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - OAuth 2.0 设备流错误。 授权处于挂起状态。 设备将重试轮询请求。 |
| AADSTS70018 | BadVerificationCode - 由于用户为设备代码流键入了错误的用户代码，验证码无效。 授权未获批准。 |
| AADSTS70019 | CodeExpired - 验证码已过期。 让用户重试登录。 |
| AADSTS70043 | 由于条件访问进行登录频率检查，刷新令牌已过期或无效。 该令牌是在 {issueDate} 颁发的，此请求的最大允许生存期为 {time}。 |
| AADSTS75001 | BindingSerializationError - SAML 消息绑定期间出错。 |
| AADSTS75003 | UnsupportedBindingError - 应用返回了与不受支持的绑定相关的错误（无法通过 HTTP POST 以外的绑定发送 SAML 协议响应）。 |
| AADSTS75005 | Saml2MessageInvalid - Azure AD 不支持应用针对 SSO 所发送的 SAML 请求。 若要了解详细信息，请参阅“故障排除”一文以了解错误 [AADSTS75005](/troubleshoot/azure/active-directory/error-code-aadsts75005-not-a-valid-saml-request)。 |
| AADSTS7500514 | 找不到支持的 SAML 响应类型。 支持的响应类型为“Response”（在 XML 命名空间“'urn:oasis:names:tc:SAML:2.0:protocol”中）或“Assertion”（在 XML 命名空间“urn:oasis:names:tc:SAML:2.0:assertion”中）。 应用程序错误 - 开发人员会处理此错误。|
| AADSTS750054 | 必须在针对 SAML 重定向绑定的 HTTP 请求中将SAMLRequest 或 SAMLResponse 提供为查询字符串参数。 若要了解详细信息，请参阅“故障排除”一文以了解错误 [AADSTS750054](/troubleshoot/azure/active-directory/error-code-aadsts750054-saml-request-not-present)。 |
| AADSTS75008 | RequestDeniedError - 由于 SAML 请求的目标不符合预期，来自应用的请求被拒绝。 |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - 用户在服务中用于身份验证的身份验证方法与请求的身份验证方法不匹配。 若要了解详细信息，请参阅“故障排除”一文以了解错误 [AADSTS75011](/troubleshoot/azure/active-directory/error-code-aadsts75011-auth-method-mismatch)。 |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 身份验证请求包含无效的 NameIdPolicy。 |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - 身份验证代理无法连接到 Active Directory。 确保代理服务器是需要验证其密码的用户所在的 AD 林的成员，并且能够连接到 Active Directory。 |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - 密码验证请求超时。确保 Active Directory 可用，并且可以响应代理的请求。 |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - 处理来自身份验证代理的响应时发生未知的错误。 重试请求。 如果仍旧失败，请[开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)，获取有关该错误的更多详细信息。 |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - 身份验证代理无法验证用户的密码。 检查代理日志以了解更多信息，并验证 Active Directory 是否按预期方式运行。 |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - 身份验证代理无法解密密码。 |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - 用户尝试在允许的时间（在 AD 中指定）以外登录。 |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - 由于运行身份验证代理的计算机与 AD 之间存在时间偏差，身份验证尝试无法完成。 解决时间同步问题。 |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos 身份验证尝试失败。 |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - 不支持该身份验证包。 |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - 找不到授权标头。 |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - 未为租户启用无缝 SSO。 |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - 无法验证用户的 Kerberos 票证。 |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - 由于用户的 Kerberos 票证已过期或无效，无缝 SSO 失败。 |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - 无法根据用户的 Kerberos 票证中的信息找到用户对象。 |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - 尝试登录到 Azure AD 的用户不同于已登录到设备的用户。 |
| AADSTS90002 | InvalidTenantName - 在数据存储中找不到该租户名称。 请确保租户 ID 正确。 |
| AADSTS90004 | InvalidRequestFormat - 请求格式不正确。 |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - 无法完成请求。 由于不能同时使用标识符和登录提示，请求无效。  |
| AADSTS90006 | ExternalServerRetryableError - 服务暂时不可用。|
| AADSTS90007 | InvalidSessionId - 错误的请求。 无法分析传递的会话 ID。 |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - 用户或管理员尚未许可使用该应用程序。 最起码，应用程序需要通过指定登录并读取用户个人资料权限来访问 Azure AD。 |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - 应用程序正在请求自身的令牌。 仅当指定的资源使用基于 GUID 的应用程序 ID 时，才支持此方案。 |
| AADSTS90010 | NotSupported - 无法创建该算法。 |
| AADSTS9001023 |不支持通过 /common 或 /consumers 终结点的授予类型。 请使用 /organizations 或特定于租户的终结点。|
| AADSTS90012 | RequestTimeout - 请求超时。 |
| AADSTS90013 | InvalidUserInput - 用户的输入无效。 |
| AADSTS90014 | MissingRequiredField - 如果预期的字段在凭据中不存在，则可能会显示此错误代码。 |
| AADSTS900144 | 请求正文必须包含以下参数：“{name}”。  开发人员错误 - 应用尝试在没有必需的或正确的身份验证参数的情况下登录。|
| AADSTS90015 | QueryStringTooLong - 查询字符串过长。 |
| AADSTS90016 | MissingRequiredClaim - 访问令牌无效。 缺少必需的声明。 |
| AADSTS90019 | MissingTenantRealm - Azure AD 无法确定请求中的租户标识符。 |
| AADSTS90020 | SAML 1.1 断言缺少用户的 ImmutableID。 开发人员错误 - 应用尝试在没有必需的或正确的身份验证参数的情况下登录。|
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - 主体名称格式无效，或者不符合预期的 `name[/host][@realm]` 格式。 主体名称是必需的，而主机和领域是可选的，可设置为 null。 |
| AADSTS90023 | InvalidRequest - 身份验证服务请求无效。 |
| AADSTS9002313 | InvalidRequest - 请求格式错误或无效。 - 这里的问题是对某个终结点的请求出了问题。 对此问题的建议是获取发生的错误的 fiddler 跟踪，并查看请求的格式是否确实正确。 |
| AADSTS90024 | RequestBudgetExceededError - 发生了暂时性错误。 重试。 |
| AADSTS90027 | 我们无法从 MSA 租户上的此 API 版本颁发令牌。 请与应用程序供应商联系，因为他们需要使用协议版本 2.0 来支持此操作。|
| AADSTS90033 | MsodsServiceUnavailable - Microsoft Online Directory Service (MSODS) 不可用。 |
| AADSTS90036 | MsodsServiceUnretryableFailure - MSODS 托管的 WCF 服务发生意外的不可重试错误。 请[开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)，获取有关该错误的更多详细信息。 |
| AADSTS90038 | NationalCloudTenantRedirection - 指定的租户“Y”属于国家/地区云“X”。 当前云实例“Z”未与 X 联合。返回了云重定向错误。 |
| AADSTS90043 | NationalCloudAuthCodeRedirection - 已禁用该功能。 |
| AADSTS900432 | 跨云请求不支持机密客户端。|
| AADSTS90051 | InvalidNationalCloudId - 国家云标识符包含无效的云标识符。 |
| AADSTS90055 | TenantThrottlingError - 传入的请求过多。 此异常是针对阻止的租户引发的。 |
| AADSTS90056 | BadResourceRequest - 若要兑换访问令牌的代码，应用应该向 `/token` 终结点发送 POST 请求。 另外，在此之前，应该提供授权代码，并在发往 `/token` 终结点的 POST 请求中发送此代码。 有关 OAuth 2.0 授权代码流的概述，请参阅此文：[../azuread-dev/v1-protocols-oauth-code.md](../azuread-dev/v1-protocols-oauth-code.md)。 将用户定向到 `/authorize` 终结点，该终结点会返回 authorization_code。 通过向 `/token` 终结点发布请求，用户可以获取访问令牌。 在 Azure 门户中登录，并检查“应用注册”>“终结点”以确认是否正确配置了两个终结点。 |
| AADSTS90072 | PassThroughUserMfaError - 用户登录时所用的外部帐户在其登录到的租户中不存在；因此，该用户无法满足该租户的 MFA 要求。 如果用户已同步，但是 Active Directory 和 Azure AD 之间的 ImmutableID (sourceAnchor) 属性不匹配，也可能会发生此错误。 必须先将该帐户作为外部用户添加到该租户中。 请注销并使用其他 Azure AD 用户帐户登录。 |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - 服务尝试处理 WS 联合身份验证消息时出错。 消息无效。 |
| AADSTS90082 | OrgIdWsFederationNotSupported - 目前不支持对该请求使用所选的身份验证策略。 |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - 此站点不允许来宾帐户。 |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - 由于尚未预配公司对象，服务无法颁发令牌。 |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - 用户 DA 令牌已过期。 |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - 从 URI 创建 WS 联合身份验证消息时出错。 |
| AADSTS90090 | GraphRetryableError - 服务暂时不可用。 |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Graph 返回了针对请求的禁止访问错误代码。 |
| AADSTS90094 | AdminConsentRequired - 需要管理员许可。 |
| AADSTS900382 | 跨云请求不支持机密客户端。 |
| AADSTS90095  | AdminConsentRequiredRequestAccess - 在管理员同意工作流体验中，当告知用户需要请求管理员同意时出现的中断。  |
| AADSTS90099 | 应用程序“{appId}”({appName}) 未在租户“{tenant}”中获得授权。 应用程序必须获得访问客户租户的授权，然后合作伙伴委托的管理员才能使用应用程序。 若要为应用程序授权，请提供预先许可或执行相应的合作伙伴中心 API。 |
| AADSTS900971| 未提供回复地址。|
| AADSTS90100 | InvalidRequestParameter - 参数为空或无效。 |
| AADSTS901002 | AADSTS901002：不支持“resource”请求参数。 |
| AADSTS90101 | InvalidEmailAddress - 提供的数据不是有效的电子邮件地址。 电子邮件地址必须采用 `someone@example.com` 格式。 |
| AADSTS90102 | InvalidUriParameter - 值必须是有效的绝对 URI。 |
| AADSTS90107 | InvalidXml - 请求无效。 请确保数据不包含无效字符。|
| AADSTS90114 | InvalidExpiryDate - 批量令牌过期时间戳会导致颁发过期的令牌。 |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - 用户代码为 null 或为空。|
| AADSTS90120 | InvalidDeviceFlowRequest - 请求已获授权或被拒绝。 |
| AADSTS90121 | InvalidEmptyRequest - 无效的空请求。|
| AADSTS90123 | IdentityProviderAccessDenied - 由于标识或声明颁发提供者拒绝了请求，无法颁发该令牌。 |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - `/common` 或 `/consumers` 终结点不支持该资源。 请改用 `/organizations` 或特定于租户的终结点。 |
| AADSTS90125 | DebugModeEnrollTenantNotFound - 用户不在系统中。 请务必正确输入用户名。 |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - 此终结点不支持该用户类型。 系统无法从用户名推断用户的租户。 |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - `/common` 或 `/consumers` 终结点不支持该应用程序。 请改用 `/organizations` 或特定于租户的终结点。 |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - 发生了不可重试的错误。|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - 未为用户主体配置 NGC ID 密钥。 |
| AADSTS130005 | NgcInvalidSignature - NGC 密钥签名验证失败。|
| AADSTS130006 | NgcTransportKeyNotFound - 设备上未配置 NGC 传输密钥。 |
| AADSTS130007 | NgcDeviceIsDisabled - 设备已禁用。 |
| AADSTS130008 | NgcDeviceIsNotFound - 找不到 NGC 密钥引用的设备。 |
| AADSTS135010 | KeyNotFound |
| AADSTS135011 |  在身份验证期间使用的设备已禁用。|
| AADSTS140000 | InvalidRequestNonce - 未提供请求 nonce。 |
| AADSTS140001 | InvalidSessionKey - 会话密钥无效。|
| AADSTS165004 | 实际消息内容特定于运行时。 有关详细信息，请查看返回的异常消息。 |
| AADSTS165900 | InvalidApiRequest - 请求无效。 |
| AADSTS220450 | UnsupportedAndroidWebViewVersion - 不支持 Chrome WebView 版本。 |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - 资源未配置为接受仅限设备的令牌。 |
| AADSTS240001 | BulkAADJTokenUnauthorized - 未授权用户在 Azure AD 中注册设备。 |
| AADSTS240002 | RequiredClaimIsMissing - 无法将 id_token 用作 `urn:ietf:params:oauth:grant-type:jwt-bearer` 授予。|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - 租户管理员已配置了阻止此请求的安全策略。 检查在租户级别定义的安全策略来确定你的请求是否满足策略要求。 |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - 应用程序中找不到目录/租户。 如果应用程序尚未由租户管理员安装，或者尚未获得租户中的任何用户同意，则可能会发生这种情况。 可能错误配置了应用程序的标识符值，或者将身份验证请求发送到了错误的租户。 |
| AADSTS700020 | InteractionRequired - 访问权限授予需要交互。 |
| AADSTS700022 | InvalidMultipleResourcesScope - 为输入参数范围提供的值无效，因为它包含多个资源。 |
| AADSTS700023 | InvalidResourcelessScope - 请求访问令牌时，为输入参数范围提供的值无效。 |
| AADSTS7000215 | 提供的客户端密码无效。 开发人员错误 - 应用尝试在没有必需的或正确的身份验证参数的情况下登录。|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided - 提供的客户端密钥已过期。 访问 Azure 门户，为你的应用创建新密钥，或者考虑使用证书凭据提高安全性：[https://aka.ms/certCreds](./active-directory-certificate-credentials.md) |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant - 提供的授权代码是用于其他租户的，因此已被拒绝。 兑换 OAuth2 授权代码时所针对的租户必须是获取该代码时所针对的租户（根据情况使用 /common 或 {tenant ID} 进行指定） |
| AADSTS1000000 | UserNotBoundError - 绑定 API 要求 Azure AD 用户同时使用外部 IDP 进行身份验证，但尚未执行此操作。 |
| AADSTS1000002 | BindCompleteInterruptError - 绑定已成功完成，但必须通知用户。 |
| AADSTS100007 | AAD 区域仅支持使用适用于 Microsoft 基础结构租户中的 1P 应用或 3P 应用的 SN+I，对 MSI 或来自 MSAL 的请求进行身份验证。|
| AADSTS1000031 | 目前无法访问应用程序 {appDisplayName}。 请与您的管理员联系。 |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled - 应用程序处于禁用状态。 |
| AADSTS7000114| 不允许使用应用程序“appIdentifier”进行应用程序代理调用。|
| AADSTS7500529 | 值“SAMLId-Guid”不是有效的 SAML ID - Azure AD 使用此属性填充返回的响应的 InResponseTo 属性。 ID 的开头不能是数字，因此常见的策略是在 GUID 的字符串表示形式前面加上类似于“id”的字符串。 例如，id6c1c178c166d486687be4aaf5e482730 是有效的 ID。 |

## <a name="next-steps"></a>后续步骤

* 遇到了问题或者找不到所需的内容？ 请创建 GitHub 问题，或查看[面向开发人员的支持和帮助选项](./developer-support-help-options.md)来了解其他可以获得帮助和支持的方法。
