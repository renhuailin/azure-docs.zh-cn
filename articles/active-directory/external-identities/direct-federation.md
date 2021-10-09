---
title: 与适用于 B2B 的 SAML/WS-Fed 标识提供者 (IdP) 联合 - Azure AD
description: 直接与 SAML 或 WS-Fed 标识提供者联合，使来宾可以登录到 Azure AD 应用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8a535f7c74e32e59918badbda7747f24a1756f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623981"
---
# <a name="federation-with-samlws-fed-identity-providers-for-guest-users-preview"></a>与适用于来宾用户的 SAML/WS-Fed 标识提供者联合（预览版）

> [!NOTE]
>- Azure Active Directory 中的“直接联合”现在称为“SAML/WS-Fed 标识提供者 (IdP) 联合”。
>- SAML/WS-Fed IdP 联合是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何与其标识提供者 (IdP) 支持 SAML 2.0 或 WS-Fed 协议的任何组织建立联合。 与合作伙伴的 IdP 建立联合时，该域中的新来宾用户可以使用其自己的 IdP 管理的组织帐户登录到 Azure AD 租户，并开始与你合作。 来宾用户无需创建单独的 Azure AD 帐户。

> [!IMPORTANT]
> - 我们删除了要求身份验证 URL 域与目标域相匹配或来自允许的 IdP 的限制。 有关详细信息，请参阅[步骤 1：确定合作伙伴是否需要更新其 DNS 文本记录](#step-1-determine-if-the-partner-needs-to-update-their-dns-text-records)。
>-  现在，我们建议合作伙伴将基于 SAML 或 WS-Fed 的 IdP 的受众设置为租户受众。 请参阅下面的 [SAML 2.0](#required-saml-20-attributes-and-claims) 和 [WS-Fed](#required-ws-fed-attributes-and-claims) 必需属性和声明部分。

## <a name="when-is-a-guest-user-authenticated-with-samlws-fed-idp-federation"></a>来宾用户何时使用 SAML/WS-Fed IdP 联合进行身份验证？

与组织的 SAML/WS-Fed IdP 建立联合后，邀请的任何新来宾用户都将使用 SAML/WS-Fed IdP 进行身份验证。 特别要注意的是，建立联合并不会更改已兑换邀请的来宾用户的身份验证方法。 下面是一些示例：

 - 如果来宾用户已经兑换了邀请，而你随后与其组织的 SAML/WS-Fed IdP 建立了联合，则这些来宾用户将继续使用在建立联合之前使用的相同身份验证方法。
 - 如果与组织的 SAML/WS-Fed IdP 建立联合并邀请来宾用户，然后合作伙伴组织稍后转到 Azure AD，则已兑换邀请的来宾用户将继续使用联合的 SAML/WS-Fed IdP，前提是租户中的联合策略已存在。
 - 如果删除与组织的 SAML/WS-Fed IdP 的联合，则当前使用 SAML/WS-Fed IdP 的任何来宾用户都将无法登录。

在上述任何方案中，都可以通过[重置来宾用户的兑换状态](reset-redemption-status.md)来更新其身份验证方法。

SAML/WS-Fed IdP 联合与域命名空间（如 contoso.com 和 fabrikam.com）相关联。 使用 AD FS 或第三方 IdP 建立联合时，组织会将一个或多个域命名空间与这些 IdP 相关联。

## <a name="end-user-experience"></a>最终用户体验 

使用 SAML/WS-Fed IdP 联合，来宾用户使用其自己的组织帐户登录到 Azure AD 租户。 当用户访问共享资源并提示登录时，他们会重定向到其 IdP。 成功登录后，用户会返回到 Azure AD 来访问资源。 联合用户的刷新令牌有效期为 12 小时，这是 Azure AD 中[传递刷新令牌的默认长度](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties)。 如果联合 IdP 启用了 SSO，用户将体验 SSO，并在初始身份验证后不会看到任何登录提示。

## <a name="sign-in-endpoints"></a>登录终结点

SAML/WS-Fed IdP 联合来宾用户现在可以使用[公共终结点](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint)（也就是不包含租户上下文的常规应用 URL）登录到多租户或 Microsoft 第一方应用。 在登录过程中，来宾用户选择“登录选项”，然后选择“登录到组织” 。 然后，用户需要键入组织的名称并继续使用自己的凭据登录。

SAML/WS-Fed IdP 联合来宾用户还可以使用包含租户信息的应用程序终结点，例如：

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

还可以通过添加租户信息，为来宾用户提供指向应用程序或资源的直接链接，例如 `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-set-up-samlws-fed-idp-federation-with-azure-ad-verified-domains"></a>能否使用经 Azure AD 验证的域设置 SAML/WS-Fed IdP 联合？
否，我们会阻止 Azure AD 验证域的 SAML/WS-Fed IdP 联合，以支持原生 Azure AD 托管域功能。 如果尝试使用 Azure AD 中经 DNS 验证的域设置 SAML/WS-Fed IdP 联合，则会在 Azure 门户或 PowerShell 中看到错误。

### <a name="can-i-set-up-samlws-fed-idp-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>是否可以与非管理的（经电子邮件验证）租户所在的域建立 SAML/WS-Fed IdP 联合？ 
是，可以使用 Azure AD 中未经 DNS 验证的域（包括非托管的（经电子邮件验证的或“病毒性的”）Azure AD 租户）设置 SAML/WS-Fed IdP 联合。 当用户使用当前不存在的域兑换 B2B 邀请或执行 Azure AD 的自助注册时，将创建此类租户。 如果未验证域且租户尚未完成[管理员接管](../enterprise-users/domains-admin-takeover.md)，则可以与该域建立联合。

### <a name="how-many-federation-relationships-can-i-create"></a>我可以创建多少联合关系？
目前最多支持 1000 个联合关系。 此限制包括[内部联合](/powershell/module/msonline/set-msoldomainfederationsettings)和 SAML/WS-Fed IdP 联合。

### <a name="can-i-set-up-federation-with-multiple-domains-from-the-same-tenant"></a>能否设置与同一租户中多个域的联合？
目前，我们不支持同一个租户中多个域的 SAML/WS-Fed IdP 联合。

### <a name="do-i-need-to-renew-the-signing-certificate-when-it-expires"></a>签名证书过期时是否需要续签该证书？
如果在 IdP 设置中指定元数据 URL，Azure AD 将在签名证书过期时自动续订该证书。 但是，如果出于任何原因在过期之前轮换证书，或未提供元数据 URL，Azure AD 将无法续订该证书。 在这种情况下，你将需要手动更新签名证书。

### <a name="if-samlws-fed-idp-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同时启用了 SAML/WS-Fed IdP 联合和电子邮件一次性密码身份验证，那么哪个方法优先？
与合作伙伴组织建立 SAML/WS-Fed IdP 联合时，对于该组织中的新来宾用户而言，它将优先于电子邮件一次性密码身份验证。 如果来宾用户在建立 SAML/WS-Fed IdP 联合之前使用一次性密码身份验证兑换了邀请，则他们将继续使用一次性密码身份验证。

### <a name="does-samlws-fed-idp-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>是否由于部分同步的租户导致了 SAML/WS-Fed IdP 联合地址登录问题？
否。在此场景中，应使用[电子邮件一次性密码](one-time-passcode.md)功能。 “部分同步的租户”指的是合作伙伴 Azure AD 租户，其中本地用户标识未完全同步到云。 其标识尚不存在于云中但尝试兑换 B2B 邀请的来宾将无法登录。 使用一次性密码功能，此来宾可以登录。 SAML/WS-Fed IdP 联合功能可以解决以下情况：来宾具有其自己的 IdP 托管的组织帐户，但组织没有 Azure AD。

### <a name="once-samlws-fed-idp-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>为组织配置 SAML/WS-Fed IdP 联合后，是否需要向每位来宾发送单独邀请，并且需要来宾兑换邀请？
建立 SAML/WS-Fed IdP 联合并不会更改已兑换邀请的来宾用户的身份验证方法。 可以通过[重置来宾用户的兑换状态](reset-redemption-status.md)来更新其身份验证方法。

### <a name="is-there-a-way-to-send-a-signed-request-to-the-saml-identity-provider"></a>是否有方法向 SAML 标识提供者发送已签名的请求？
目前，Azure AD SAML/WS-Fed 联合功能不支持向 SAML 标识提供者发送已签名的身份验证令牌。

## <a name="step-1-determine-if-the-partner-needs-to-update-their-dns-text-records"></a>步骤 1：确定合作伙伴是否需要更新其 DNS 文本记录

根据合作伙伴的 IdP，合作伙伴可能需要更新其 DNS 记录以启用与你的联合。 使用以下步骤确定是否需要 DNS 更新。

1. 如果合作伙伴的 IdP 是其中一种允许的 IdP，则不需要进行 DNS 更改（此列表可能会更改）：

     - accounts.google.com
     - pingidentity.com
     - login.pingone.com
     - okta.com
     - oktapreview.com
     - okta-emea.com
     - my.salesforce.com
     - federation.exostar.com
     - federation.exostartest.com
     - idaptive.app
     - idaptive.qa

2. 如果 IdP 不是上一步中列出的允许的提供者之一，请检查合作伙伴的 IdP 身份验证 URL，以查看域是否与目标域或目标域中的主机匹配。 换言之，为 `fabrikam.com` 设置联合时：

     - 如果身份验证 URL 是 `https://fabrikam.com` 或 `https://sts.fabrikam.com/adfs`（同一域中的主机），则不需要更改 DNS。
     - 如果身份验证 URL 为 `https://fabrikamconglomerate.com/adfs`  或  `https://fabrikam.com.uk/adfs`，则域与 fabrikam.com 域不匹配，因此，合作伙伴需要为其 DNS 配置添加身份验证 URL 的文本记录。

    > [!IMPORTANT]
    > 以下步骤存在一个已知问题。 目前，将 DNS 文本记录添加到联合 IdP 域不会取消阻止身份验证。 我们正在积极解决此问题。

3. 如果根据上一步需要进行 DNS 更改，则要求合作伙伴向其域的 DNS 记录中添加 TXT 记录，如以下示例所示：

   `fabrikam.com.  IN   TXT   DirectFedAuthUrl=https://fabrikamconglomerate.com/adfs`

## <a name="step-2-configure-the-partner-organizations-idp"></a>步骤 2：配置合作伙伴组织的 IdP

接下来，合作伙伴组织需要使用所需的声明和信赖方信任配置其 IdP。

> [!NOTE]
> 为了说明如何为联合配置 SAML/WS-Fed IdP，我们将使用 Active Directory 联合身份验证服务 (AD FS) 作为示例。 请参阅文章[使用 AD FS 配置 SAML/WS-Fed IdP 联合](direct-federation-adfs.md)，其中提供了有关如何将 AD FS 配置为 SAML 2.0 或 WS-Fed IdP 以准备联合的示例。

### <a name="saml-20-configuration"></a>SAML 2.0 配置

可以将 Azure AD B2B 配置为与使用 SAML 协议的 IdP 联合，具体要求如下。 有关在 SAML IdP 和 Azure AD 之间建立信任的详细信息，请参阅[使用 SAML 2.0 标识提供者 (IdP) 进行单一登录](../hybrid/how-to-connect-fed-saml-idp.md)。  

> [!NOTE]
> 在 Azure AD 中，不能对 SAML/WS-Fed IdP 联合的目标域进行 DNS 验证。 有关详细信息，请参阅[常见问题](#frequently-asked-questions)部分。

#### <a name="required-saml-20-attributes-and-claims"></a>SAML 2.0 的必需属性和声明
下表显示了必须在第三方 IdP 中配置的特定属性和声明的要求。 若要建立联合，必须从 IdP 的 SAML 2.0 响应中接收以下属性。 可以通过链接到联机安全令牌服务 XML 文件或手动输入来配置这些属性。

来自 IdP 的 SAML 2.0 响应的必需属性：

|Attribute  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`https://login.microsoftonline.com/<tenant ID>/`（建议的租户受众。）将 `<tenant ID>` 替换为要设置联合的 Azure AD 租户的租户 ID。<br><br>`urn:federation:MicrosoftOnline`（此值即将弃用。）          |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |


IdP 颁发的 SAML 2.0 令牌的必需声明：

|Attribute  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed 配置

可以将 Azure AD B2B 配置为与使用 WS-Fed 协议的 IdP 联合，具体要求如下。 目前，已通过 Azure AD 对两个 WS-Fed 提供者的兼容性进行测试，包括 AD FS 和 Shibboleth。 有关通过 Azure AD 在 WS-Fed 兼容的提供者之间建立信赖方信任的详细信息，请参阅 [Azure AD 标识提供者兼容性文档](https://www.microsoft.com/download/details.aspx?id=56843)中提供的“使用 WS 协议的 STS 集成论文”。

> [!NOTE]
> 在 Azure AD 上，不能对联合的目标域进行 DNS 验证。 有关详细信息，请参阅[常见问题](#frequently-asked-questions)部分。

#### <a name="required-ws-fed-attributes-and-claims"></a>WS-Fed 的必需属性和声明

下表显示了必须在第三方 WS-Fed IdP 中配置的特定属性和声明的要求。 若要建立联合，必须从 IdP 的 WS-Fed 消息中接收以下属性。 可以通过链接到联机安全令牌服务 XML 文件或手动输入来配置这些属性。

来自 IdP 的 WS-Fed 消息中的必需属性：
 
|Attribute  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`https://login.microsoftonline.com/<tenant ID>/`（建议的租户受众。）将 `<tenant ID>` 替换为要与其联合的 Azure AD 租户的租户 ID。<br><br>`urn:federation:MicrosoftOnline`（此值即将弃用。）          |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 颁发的 WS-Fed 令牌的必需声明：

|Attribute  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-3-configure-samlws-fed-idp-federation-in-azure-ad"></a>步骤 3：在 Azure AD 中配置 SAML/WS-Fed IdP 联合

接下来，将配置与 Azure AD 中的步骤 1 中配置的 IdP 的联合。 可以使用 Azure AD 门户或 PowerShell。 联合策略生效之前可能需要 5-10 分钟。 在此期间，不要尝试兑换联合域的邀请。 需要以下属性：

- 合作伙伴 IdP 的颁发者 URI
- 合作伙伴 IdP 的被动身份验证终结点（仅支持 https）
- 证书

### <a name="to-configure-federation-in-the-azure-ad-portal"></a>在 Azure AD 门户中配置联合

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”。 
2. 选择“外部标识” > “所有标识提供者”。
3. 选择“新 SAML/WS 联合身份验证 IdP”。

    ![显示用于添加新 SAML 或 WS-Fed IdP 的按钮的屏幕截图](media/direct-federation/new-saml-wsfed-idp.png)

4. 在“新的 SAML/WS-Fed IdP”页上，在“标识提供者协议”下，选择“SAML”或“WS-FED”。

    ![显示 SAML 或 WS-Fed IdP 页上的“分析”按钮的屏幕截图](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 输入合作伙伴组织的域名，该域名将是联合的目标域名
6. 可以上传元数据文件以填充元数据详细信息。 如果选择手动输入元数据，请输入以下信息：
   - 合作伙伴 IdP 的域名
   - 合作伙伴 IdP 的实体 ID
   - 合作伙伴 IdP 的被动请求者终结点
   - 证书
   > [!NOTE]
   > 元数据 URL 是可选的，但我们强烈建议使用。 如果提供元数据 URL，Azure AD 可以在签名证书过期时自动续订该证书。 如果出于任何原因在过期之前轮换证书，或未提供元数据 URL，Azure AD 将无法续订该证书。 在这种情况下，你将需要手动更新签名证书。

7. 选择“保存”。 

### <a name="to-configure-samlws-fed-idp-federation-in-azure-ad-using-powershell"></a>在 Azure AD 中使用 PowerShell 配置 SAML/WS-Fed IdP 联合

1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 如果需要详细步骤，本快速入门提供了 [PowerShell 模块](b2b-quickstart-invite-powershell.md#prerequisites)指导。
2. 运行以下命令：

   ```powershell
   Connect-AzureAD
   ```

3. 根据登录提示使用托管的全局管理员帐户登录。
4. 运行以下命令，并替换联合元数据文件中的值。 对于 AD FS Server 和 Okta，联合文件为 federationmetadata.xml，例如：`https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`。 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-4-test-samlws-fed-idp-federation-in-azure-ad"></a>步骤 4：在 Azure AD 中测试 SAML/WS-Fed IdP 联合
现在，邀请新的 B2B 来宾用户来测试联合设置。 有关详细信息，请参阅[在 Azure 门户中添加 Azure AD B2B 协作用户](add-users-administrator.md)。
 
## <a name="how-do-i-edit-a-samlws-fed-idp-federation-relationship"></a>如何编辑 SAML/WS-Fed IdP 联合关系？

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”
4. 在“SAML/WS-Fed 标识提供者”下，选择提供者。
5. 在“标识提供者详细信息”窗格中，更新值。
6. 选择“保存”。


## <a name="how-do-i-remove-federation"></a>如何删除联合？

可以删除联合设置。 如果这样做，已兑换邀请的联合来宾用户将无法登录。 但可以通过[重置这些用户的兑换状态](reset-redemption-status.md)，让这些用户能够再次访问你的资源。 要在 Azure AD 门户中删除与 IdP 的联合，请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”。
2. 选择“外部标识”。
3. 选择“所有标识提供者”。
4. 选择标识提供者，然后选择“删除”。
5. 选择“是”以确认删除。 

要使用 PowerShell 删除与标识提供者的联合，请执行以下操作：

1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行以下命令：

   ```powershell
   Connect-AzureAD
   ```

3. 根据登录提示使用托管的全局管理员帐户登录。
4. 输入以下命令：

   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>后续步骤

详细了解外部用户使用各种标识提供程序登录时的[邀请兑换体验](redemption-experience.md)。
