---
title: 使用自定义策略将 AD FS 添加为 SAML 标识提供者
titleSuffix: Azure AD B2C
description: 使用 SAML 协议和自定义策略设置 AD FS 2016 Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e344d85bbdac92aa372fc5d5e59ef90b11dfac6c
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095725"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 中的自定义策略将 AD FS 添加为 SAML 标识提供者

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文说明如何使用 Azure Active Directory B2C (Azure AD B2C) 中的 [自定义策略](custom-policy-overview.md) 来启用 AD FS 用户帐户的登录。 可以通过将 [SAML 标识提供者](identity-provider-generic-saml.md) 添加到自定义策略来启用登录。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>创建策略密钥

需要将你的证书存储在 Azure AD B2C 租户中。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 在“概述”页上选择“标识体验框架”。
5. 选择“策略密钥”，然后选择“添加”。
6. 对于“选项”，请选择 `Upload`。
7. 输入策略密钥的 **名称**。 例如，`SAMLSigningCert`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
8. 浏览并选择带有私钥的证书 .pfx 文件。
9. 单击“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 AD FS 帐户登录，则需要将该帐户定义为 Azure AD B2C 可以通过终结点进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过将 AD FS 帐户添加到策略扩展文件中的 **ClaimsProviders** 元素来将其定义为声明提供程序。 有关详细信息，请参阅 [定义 SAML 标识提供者](identity-provider-generic-saml.md)。

1. 打开 *TrustFrameworkExtensions.xml*。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 将替换 `your-AD-FS-domain` 为你的 AD FS 域的名称，并将 **identityProvider** 输出声明的值替换为你的 DNS (任意值，以指示你的域) 。

1. 找到 `<ClaimsProviders>` 部分并添加以下 XML 片段。 如果策略已包含 `SM-Saml-idp` 技术配置文件，请跳到下一步。 有关详细信息，请参阅[单一登录会话管理](custom-policy-reference-sso.md)。

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 保存文件。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>配置 AD FS 信赖方信任

若要在 Azure AD B2C 中使用 AD FS 作为标识提供者，需要使用 Azure AD B2C SAML 元数据创建 AD FS 信赖方信任。 以下示例显示 Azure AD B2C 技术配置文件的 SAML 元数据的 URL 地址：

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

请替换以下值：

- 将 your-tenant 替换为你的租户名称，例如 your-tenant.onmicrosoft.com。
- 将 your-policy 替换为你的策略名称。 例如，B2C_1A_signup_signin_adfs。
- **你的技术配置文件** ，其中包含 SAML 标识提供者技术配置文件的名称。 例如，Contoso-SAML2。

打开浏览器并导航到此 URL。 确保键入正确的 URL 并且你有权访问 XML 元数据文件。 要通过使用 AD FS 管理管理单元添加新的依赖方信任并手动配置设置，请在联合服务器上执行以下过程。 本地计算机上“管理员”中的成员身份或同等身份是完成此过程所需的最低要求。

1. 在服务器管理器中，选择 " **工具**"，然后选择 " **AD FS 管理**"。
2. 选择“添加信赖方信任”。
3. 在“欢迎”页上，选择“声明感知”，然后单击“启动”。
4. 在“选择数据源”页上，选择“导入有关依赖方在线或在本地网络上发布的数据”提供 Azure AD B2C 元数据 URL，然后单击“下一步”。
5. 在“指定显示名称”页上，输入一个“显示名称”，在“说明”下输入有关此信赖方信任的描述，然后单击“下一步”。
6. 在“选择访问控制策略”页上选择一个策略，然后单击“下一步”。
7. 在“准备好添加信任”页上，复查设置，然后单击“下一步”来保存信赖方信任的信息。
8. 在“完成”页上，单击“关闭”，此操作将自动显示“编辑声明规则”对话框。
9. 选择 " **添加规则**"。
10. 在“声明规则模板”中，选择“以声明方式发送 LDAP 特性”。
11. 提供“声明规则名称”。 有关“属性存储”，选择“选择 Active Directory”添加以下声明，然后单击“完成”和“确定”。

    | LDAP 属性 | 传出声明类型 |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | 电子邮件 |
    | Display-Name | name |

    请注意，这些名称将不会显示在 "传出声明类型" 下拉列表中。 需要在中手动键入。  (下拉列表实际上) 可编辑。

12.  根据证书类型，可能需要设置哈希算法。 在信赖方信任（B2C 演示）属性窗口上，选择“高级”选项卡并将“安全哈希算法”更改为 `SHA-256`，然后单击“确定”。
13. 在服务器管理器中，选择 " **工具**"，然后选择 " **AD FS 管理**"。
14. 选择所创建的信赖方信任，选择“从联合元数据更新”，然后单击“更新”。

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在 "**策略**" 下，选择 "**标识体验框架**"
1. 选择信赖方策略，例如 `B2C_1A_signup_signin` 。
1. 对于 " **应用程序**"，请选择 [之前注册](tutorial-register-applications.md)的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择 " **立即运行** " 按钮。

如果登录过程成功，浏览器将重定向到 `https://jwt.ms` ，后者显示 Azure AD B2C 返回的令牌的内容。
## <a name="troubleshooting-ad-fs-service"></a>AD FS 服务疑难解答  

AD FS 配置为使用 Windows 应用程序日志。 如果在 Azure AD B2C 中使用自定义策略将 AD FS 设置为 SAML 标识提供程序时遇到困难，可能需要检查 AD FS 事件日志：

1. 在 Windows **搜索栏** 中，键入 " **事件查看器**"，然后选择 " **事件查看器** 桌面" 应用。
1. 若要查看另一台计算机的日志，右键单击“事件查看器（本地）”。 选择“连接到另一台计算机”，并填写字段以完成“选择计算机”对话框。
1. 在“事件查看器”中，打开“应用程序和服务日志” 。
1. 选择 **AD FS**，然后选择 " **管理员**"。 
1. 若要查看有关某事件的详细信息，请双击该事件。  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>SAML 请求未通过预期的签名算法事件进行签名

此错误表示 Azure AD B2C 发送的 SAML 请求未使用 AD FS 中配置的预期签名算法进行签名。 例如，SAML 请求用签名算法进行签名 `rsa-sha256` ，但预期的签名算法为 `rsa-sha1` 。 若要解决此问题，请确保 Azure AD B2C 和 AD FS 都配置了相同的签名算法。

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>选项1：在 Azure AD B2C 中设置签名算法  

可以在 Azure AD B2C 中配置如何对 SAML 请求进行签名。 [XmlSignatureAlgorithm](identity-provider-generic-saml.md)元数据控制 `SigAlg` SAML 请求中 (查询字符串或 post 参数) 参数的值。 下面的示例将 Azure AD B2C 配置为使用 `rsa-sha256` 签名算法。

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>选项2：在 AD FS 中设置签名算法 

或者，你可以在 AD FS 中配置 SAML 请求签名算法的预期。

1. 在服务器管理器中，选择 " **工具**"，然后选择 " **AD FS 管理**"。
1. 选择之前创建的 **信赖方信任** 。
1. 选择 "**属性**"，然后选择 "**前进**"
1. 配置 " **安全哈希算法**"，然后选择 **"确定"** 保存更改。

::: zone-end
