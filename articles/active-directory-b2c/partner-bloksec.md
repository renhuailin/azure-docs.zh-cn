---
title: 为 Azure Active Directory B2C 配置 BlokSec 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 BlokSec 集成以进行无密码身份验证
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 7/15/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 80d2ad6d011633a3db6a63a37f04db5d22f517ba
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179617"
---
# <a name="tutorial-configure-azure-active-directory-b2c-with-bloksec-for-passwordless-authentication"></a>教程：为 Azure Active Directory B2C 配置 BlokSec 以进行无密码身份验证

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"



::: zone-end

本示例教程介绍如何将 Azure Active Directory (AD) B2C 身份验证与 [BlokSec](https://bloksec.com/) 集成。 BlokSec 通过为客户提供无密码身份验证和无令牌多重身份验证 (MFA) 来简化最终用户登录体验。 BlokSec 可保护客户免受以标识为中心的网络攻击，例如密码填充、网络钓鱼和中间人攻击。

## <a name="scenario-description"></a>方案描述

BlokSec 集成包括以下组件：

- Azure AD B2C – 配置为任何 B2C 应用程序的授权服务器/标识提供者。

- BlokSec 分散式身份识别路由器 – 充当希望应用 BlokSec 的 DIaaS™ 的服务的网关将身份验证和授权请求路由到最终用户的个人标识提供者 (PIdP) 应用程序;配置为 OpenID Connect (OIDC) 标识提供者的 OpenID Azure AD B2C。

- 基于 BlokSec SDK 的移动应用 - 在分散式身份验证方案中充当用户的 PIdP。 如果你的组织不想使用 BlokSec SDK 开发自己的移动应用程序，可以使用可免费下载的 [BlokSec yuID](https://play.google.com/store/apps/details?id=com.bloksec) 应用程序。
以下体系结构图体现了实施详情。

![图像显示了体系结构图](./media/partner-bloksec/partner-bloksec-architecture-diagram.png)

|步骤| 说明|
|:---------------|:----------------|
|1.| 用户尝试登录到 Azure AD B2C 应用程序，并转接到 Azure AD B2C 的合并登录和注册策略。|
|2.| Azure AD B2C 使用 OIDC 授权代码流将用户重定向到 BlokSec 分散式身份识别路由器。|
|3.| BlokSec 分散式路由器向用户的移动应用发送推送通知，包括身份验证和授权请求的所有上下文详细信息。|
|4.| 用户审核身份验证质询，如果接受，系统会提示用户进行生物测量，例如指纹或面部扫描（在设备上可用）来证明用户的身份。|
|5.| 使用用户的唯一数字密钥对响应进行数字签名。 最终身份验证响应提供拥有、存在和同意证明。 响应将返回到 BlokSec 分散式身份识别路由器。|
|6.| BlokSec 分散式身份识别路由器根据存储在分布式账本中的用户的不可变唯一公钥验证数字签名，然后将身份验证结果回复到 Azure AD B2C。|
|7.| 根据身份验证结果授予/拒绝用户的访问权限。|

## <a name="onboard-to-bloksec"></a>加入到 BlokSec

通过填写[表单](https://bloksec.com/request-a-demo/)，使用 BlokSec 请求演示租户。 在消息字段中，表明你要加入 Azure AD B2C。 从应用商店下载并安装免费的 BlokSec yuID 移动应用。 准备好演示租户后，你将收到一封电子邮件。 在安装了 BlokSec 应用程序的移动设备上，选择链接以将管理员帐户注册到 yuID 应用。

::: zone pivot="b2c-user-flow"
## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

- BlokSec [试用帐户](https://bloksec.com/)。

- 如果尚未获取，可以[注册](./tutorial-register-applications.md) Web 应用[并启用 ID 令牌隐式授权](./tutorial-register-applications.md#enable-id-token-implicit-grant)。
::: zone-end

::: zone pivot="b2c-custom-policy"
## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

- BlokSec [试用帐户](https://bloksec.com/)。

- 如果尚未获取，可以[注册](./tutorial-register-applications.md) Web 应用[并启用 ID 令牌隐式授权](./tutorial-register-applications.md#enable-id-token-implicit-grant)。

- 完成 [Azure Active Directory B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)中的步骤。
::: zone-end

### <a name="part-1---create-an-application-registration-in-bloksec"></a>第 1 部分 - 在 BlokSec 中创建应用程序注册

1. 登录到 BlokSec 管理门户。 加入 BlokSec 时收到的帐户注册电子邮件中会包含一个链接。

2. 在主仪表板上，选择“添加应用程序”>“创建自定义”

3. 按如下所示填写应用程序详细信息并提交：  

   |属性  |值  |
   |---------|---------|
   |  名称         |Azure AD B2C 或所需的应用程序名称|
   |SSO 类型         | OIDC|
   |徽标 URI     |[https://bloksec.io/assets/AzureB2C.png](https://bloksec.io/assets/AzureB2C.png) 指向你选择的图像的链接|
   |重定向 URI     | https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp <BR>例如：……“https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp” <BR><BR>如果使用自定义域，请输入 https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp 。 <BR> 将 your-domain-name 替换为你的自定义域，将 your-tenant-name 替换为你的租户名称。         |
   |注销后重定向 URI  |https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/{policy}/oauth2/v2.0/logout   <BR> [发送注销请求](./openid-connect.md#send-a-sign-out-request)。 |

4. 保存后，选择新创建的 Azure AD B2C 来打开应用程序配置，然后选择生成应用密码。

>[!NOTE]
>之后将需要应用程序 ID 和应用程序密码，以在 Azure AD B2C 中配置标识提供者。

::: zone pivot="b2c-user-flow"

### <a name="part-2---add-a-new-identity-provider-in-azure-ad-b2c"></a>第 2 部分 - 在 Azure AD B2C 中添加新的标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/#home)。

2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。

3. 选择 Azure 门户左上角的所有服务，搜索并选择 Azure AD B2C 

4. 导航至“仪表板”>“Azure Active Directory B2C”>“标识提供者”

5. 选择新的 OpenID Connect 提供程序

6. 选择“添加”

### <a name="part-3---configure-an-identity-provider"></a>第 3 部分 - 配置标识提供者

1. 选择“标识提供者类型”>“OpenID Connect”

2. 填写用于设置标识提供者的表单：

|属性  |值  |
|:---------|:---------|
|名称     |输入 BlokSec yuID - 无密码或选择的名称|
|元数据 URL|https://api.bloksec.io/oidc/.well-known/openid-configuration|         
|客户端 ID|第 1 部分中捕获的 BlokSec 管理 UI 中的应用程序 ID|
|客户端机密|第 1 部分中捕获的 BlokSec 管理 UI 中的应用程序密码|
|范围|OpenID 电子邮件配置文件|
|响应类型|代码|
|域提示|yuID|

3. 选择“确定”

4. 选择“映射此标识提供者的声明”。

5. 填写用于映射标识提供者的表单：

|属性  |Value  |
|:---------|:---------|
|用户 ID|sub|
|显示名称|name|
|给定名称|given_name|
|Surname|family_name|
|电子邮件|电子邮件|

6. 选择“保存”以完成新 OIDC 标识提供者的设置。  

### <a name="part-4---user-registration"></a>第 4 部分 - 用户注册

1. 使用之前提供的凭据登录到 BlokSec 管理控制台。

2. 导航至之前创建的 Azure AD B2C 应用程序。 选择右上角的齿轮图标，然后选择创建帐户。  

3. 在创建帐户窗体中输入用户的信息，记下帐户名称，然后选择“提交”。  

用户提供电子邮件地址后将收到一封帐户注册电子邮件。 让用户追随安装了 BlokSec yuID 应用的移动设备上的注册链接，

### <a name="part-5---create-a-user-flow-policy"></a>第 5 部分 - 创建用户流策略

现在，你应该会看到 BlokSec 作为新 OIDC 标识提供者列在 B2C 标识提供者中。  

1. 在 Azure AD B2C 租户中的“策略”下，选择“用户流”。  

2. 选择新建用户流

3. 选择“注册和登录” > “版本” > “创建”。

4. 为策略输入一个“名称”。

5. 在标识提供者部分，选择新创建的 BlokSec 标识提供者。  

6. 为本地帐户选择“无”以禁用基于电子邮件和密码的身份验证。

7. 选择“运行用户流”

8. 在窗体中，输入回复 URL，例如， https://jwt.ms

9. 浏览器将重定向到 BlokSec 登录页面。 输入用户注册期间注册的帐户名称。 用户的移动设备（安装了 BlokSec yuID）将收到一条推送通知；打开通知后，用户将收到身份验证质询

10. 接受身份验证质询后，浏览器会将用户重定向到回复 URL。  

## <a name="next-steps"></a>后续步骤 

有关更多信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end

::: zone pivot="b2c-custom-policy"

>[!NOTE]
>在 Azure Active Directory B2C 中，自定义策略主要用于解决复杂的情况[](./user-flow-overview.md)。 在大多数情况下，建议使用内置用户流[](./user-flow-overview.md)。

### <a name="part-2---create-a-policy-key"></a>第 2 部分 - 创建策略密钥

存储之前在 Azure AD B2C 租户中记录的客户端密码。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。

3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。

4. 在“概述”页上选择“标识体验框架”。

5. 选择“策略密钥”，然后选择“添加”。

6. 对于“选项”，请选择 `Manual`。

7. 输入策略密钥的 **名称**。 例如，`BlokSecAppSecret` 。 前缀 `B2C_1A_` 会自动添加到密钥名称。

8. 在“机密”中，输入前面记录的应用程序机密。

9. 在“密钥用法”处选择 `Signature`。

10. 选择“创建”。

### <a name="part-3---configure-bloksec-as-an-identity-provider"></a>第 3 部分 - 将 BlokSec 配置为标识提供者

要使用户能够使用 BlokSec 分散式身份识别登录，需将 BlokSec 定义为 Azure AD B2C 可通过终结点与之通信的声明提供程序。 终结点提供一组声明，Azure AD B2C 使用生物测量（例如指纹或面部扫描）验证特定用户是否通过其设备上可用的身份验证，从而证明用户的身份。

要将 BlokSec 定义为声明提供程序，可以将其添加至策略的扩展文件中的 ClaimsProvider 元素

1. 打开 `TrustFrameworkExtensions.xml`。

2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下面添加。

3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>bloksec</Domain>
      <DisplayName>BlokSec</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="BlokSec-OpenIdConnect">
          <DisplayName>BlokSec</DisplayName>
          <Description>Login with your BlokSec decentriled identity</Description>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://api.bloksec.io/oidc/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the BlokSec Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://api.bloksec.io/oidc</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_BlokSecAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 将 **client_id** 设置为应用程序注册中的应用程序 ID。

5. 保存文件。

### <a name="part-4---add-a-user-journey"></a>第 4 部分 - 添加用户旅程

此时，标识提供者已设置，但还不能在任何登录页中使用。 如果你没有自己的自定义用户旅程，请创建现有模板用户旅程的副本，否则，请继续执行下一步。  

1. 从初学者包中打开 `TrustFrameworkBase.xml` 文件。

2. 找到并复制包含 ID=`SignUpOrSignIn` 的 UserJourney 元素的完整内容。

3. 打开 `TrustFrameworkExtensions.xml` 并找到 UserJourney元素。 如果该元素不存在，请添加一个。

4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。

5. 重命名用户旅程的 ID。 例如，ID=`CustomSignUpSignIn`。  

### <a name="part-5---add-the-identity-provider-to-a-user-journey"></a>第 5 部分 - 将标识提供者添加到用户旅程

目前你已拥有用户旅程，请将新的标识提供者添加到用户旅程。 首先添加一个登录按钮，然后将该按钮链接到某个操作。 该操作是你之前创建的技术配置文件。  

1. 在用户旅程中，查找包含 Type=`CombinedSignInAndSignUp` 或 Type=`ClaimsProviderSelection` 的业务流程步骤元素。 这通常是第一个业务流程步骤。 ClaimsProviderSelections 元素包含用户可以用来登录的标识提供者列表。 元素的顺序将决定向用户显示的登录按钮的顺序。 添加 ClaimsProviderSelection XML 元素。 将 TargetClaimsExchangeId 的值设置为易记名称。

2. 在下一个业务流程步骤中，添加 ClaimsExchange 元素。 将 ID 设为目标声明交换 ID 的值。 将 **TechnicalProfileReferenceId** 的值更新为先前创建的技术配置文件的 ID。

下面的 XML 演示了使用标识提供者进行用户旅程的前两个业务流程步骤：

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="BlokSecExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="BlokSecExchange" TechnicalProfileReferenceId="BlokSec-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>第 6 部分 - 配置信赖方策略

信赖方策略（例如 [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)）指定 Azure AD B2C 将执行的用户旅程。 在信赖方内查找 DefaultUserJourney 元素。 更新 ReferenceId，使其与已在其中添加标识提供者的用户旅程 ID 匹配。

在以下示例中，对于 `CustomSignUpOrSignIn` 用户旅程，将 ReferenceId 设置为 `CustomSignUpOrSignIn`。  
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>第 7 部分 - 上传自定义策略

1. 登录 [Azure 门户](https://portal.azure.com/#home)。

2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。

3. 在 [Azure 门户](https://portal.azure.com/#home)中，搜索并选择 Azure AD B2C。

4. 在策略下，选择 Identity Experience Framework。
选择“上传自定义策略”，然后上传已更改的两个策略文件，其顺序为：先上传扩展策略（例如 `TrustFrameworkExtensions.xml`），然后上传信赖方策略（例如 `SignUpSignIn.xml`）。

### <a name="part-8---test-your-custom-policy"></a>第 8 部分 - 测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。

2. 对于“应用程序”，请选择[前面注册](./tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。

3. 选择“立即运行”按钮。

4. 在注册或登录页面中，选择“Google”以使用 Google 帐户登录。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。

## <a name="next-steps"></a>后续步骤 

有关更多信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end