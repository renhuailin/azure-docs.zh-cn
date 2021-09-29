---
title: 在自定义策略中定义 ID 令牌提示技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义 ID 令牌提示技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/16/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 39c21beb5f2f8f673210f8fc4c8c5bf0a7d13c2a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575069"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 ID 令牌提示技术配置文件

通过 Azure AD B2C，信赖方应用可将入站 JWT 作为 OAuth2 授权请求的一部分发送。 JWT 令牌可以由信赖方应用或标识提供者颁发，并且可以传递有关用户或授权请求的提示。 Azure AD B2C 验证签名、颁发者名称和令牌受众，并从入站令牌中提取声明。

## <a name="use-cases"></a>用例

可以使用此解决方案将数据发送到封装在单个 JWT 令牌中的 Azure AD B2C。 [通过电子邮件邀请注册的解决方案](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md)以 id_token_hint 为基础，系统管理员可以向用户发送签名邀请。 只有有权访问邀请电子邮件的用户才能在目录中创建帐户。

## <a name="token-signing-approach"></a>令牌签名方法

通过 id_token_hint，令牌颁发者（信赖方应用或标识提供者）会组合令牌，然后使用签名密钥对令牌进行签名，以证明令牌来自受信任的源。 签名密钥可以是对称的，也可以是非对称的。 对称加密（私钥加密）使用共享机密来签署和验证签名。 非对称加密（公钥加密）是使用私钥和公钥的加密系统。 私钥只为令牌颁发者所知，并用于对令牌进行签名。 公钥通过 Azure AD B2C 策略共享以验证令牌的签名。

## <a name="token-format"></a>令牌格式

id_token_hint 必须是有效的 JWT 令牌。 下表列出了必需的声明。 其他声明是可选的。

| 名称 | 声明 | 示例值 | 说明 |
| ---- | ----- | ------------- | ----------- |
| 目标受众 | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | 标识令牌的目标接收方。 受众是令牌颁发者定义的任意字符串。 Azure AD B2C 将验证此值，如果不匹配，则拒绝该令牌。  |
| 颁发者 | `iss` |`https://localhost` | 标识安全令牌服务（令牌颁发者）。 颁发者是令牌颁发者定义的任意 URI。 Azure AD B2C 将验证此值，如果不匹配，则拒绝该令牌。  |
| 过期时间 | `exp` | `1600087315` | 令牌失效的时间，以纪元时间表示。 Azure AD B2C 将验证此值，如果令牌已过期，则拒绝该令牌。|
| 生效时间 | `nbf` | `1599482515` | 令牌生效的时间，以纪元时间表示。 此时间通常与颁发令牌的时间相同。 Azure AD B2C 将验证此值，如果令牌生存期无效，则拒绝该令牌。 |

 以下令牌是有效 ID 令牌的示例：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `None`。 例如，IdTokenHint_ExtractClaims 技术配置文件的协议为 `None`：

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

技术配置文件是从 `GetClaims` 类型的业务流程步骤中调用的。

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>输出声明

OutputClaims 元素包含要从 JWT 令牌中提取的声明列表。 可能需要将策略中定义的声明名称映射到 JWT 令牌中定义的名称。 只要设置了 `DefaultValue` 属性，就还可以包含 JWT 令牌不会返回的声明。

## <a name="metadata"></a>Metadata

使用对称密钥时，以下元数据是相关的。 

| Attribute | 必需 | 描述 |
| --------- | -------- | ----------- |
| 颁发者 | 是 | 标识安全令牌服务（令牌颁发者）。 此值必须与 JWT 令牌声明中的 `iss` 声明相同。 | 
| IdTokenAudience | 是 | 标识令牌的目标接收方。 必须与 JWT 令牌声明中的 `aud` 声明相同。 | 

使用非对称密钥时，以下元数据是相关的。 

| Attribute | 必需 | 描述 |
| --------- | -------- | ----------- |
| METADATA| 是 | 指向令牌颁发者配置文档的 URL，也称为 OpenID 已知配置终结点。   |
| 颁发者 | 否 | 标识安全令牌服务（令牌颁发者）。 此值可用于覆盖元数据中配置的值，并且必须与 JWT 令牌声明中的 `iss` 声明相同。 |  
| IdTokenAudience | 否 | 标识令牌的目标接收方。 必须与 JWT 令牌声明中的 `aud` 声明相同。 |  

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="cryptographic-keys"></a>加密密钥

使用对称密钥时，CryptographicKeys 元素包含以下属性：

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| client_secret | 是 | 用于验证 JWT 令牌签名的加密密钥。|


## <a name="how-to-guide"></a>操作指南

### <a name="issue-a-token-with-symmetric-keys"></a>使用对称密钥颁发令牌

#### <a name="step-1-create-a-shared-key"></a>步骤 1。 创建一个共享密钥 

创建可用于对令牌进行签名的密钥。 例如，使用以下 PowerShell 代码生成密钥。

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

此代码创建机密字符串（如 `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=`）。

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>步骤 2。 将签名密钥添加到 Azure AD B2C

令牌颁发者使用的同一密钥需要在 Azure AD B2C 策略密钥中创建。  

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在概述页面上的“策略”下，选择“Identity Experience Framework” 。
1. 选择“策略密钥” 
1. 选择“手动”。
1. 使用 `IdTokenHintKey` 作为“名称”。  
   可能会自动添加前缀 `B2C_1A_`。
1. 在“机密”框中，输入之前生成的登录密钥。
1. 使用“加密”作为“密钥用法”。
1. 选择“创建” 。
1. 确认已创建密钥 `B2C_1A_IdTokenHintKey`。


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>步骤 3. 添加 ID 令牌提示技术配置文件

以下技术配置文件会验证令牌并提取声明。 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>步骤 4. 准备策略

完成[配置策略](#configure-your-policy)步骤。

#### <a name="step-5-prepare-the-code"></a>步骤 5。 准备代码  

[GitHub 示例](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key)是一个 ASP.NET Web 应用程序和控制台应用，可生成使用对称密钥签名的 ID 令牌。 


### <a name="issue-a-token-with-asymmetric-keys"></a>使用非对称密钥颁发令牌

借助非对称密钥，令牌使用 RSA 证书进行签名。 此应用程序托管 Azure AD B2C 用来验证 ID 令牌的签名的 Open ID Connect 元数据终结点和 JSON Web 密钥 (JWK) 终结点。

令牌颁发者必须提供以下终结点：

* `/.well-known/openid-configuration` - 一个已知配置终结点，其中包含有关令牌的相关信息，例如令牌颁发者名称和 JWK 终结点的链接。 
* `/.well-known/keys` - JSON Web 密钥 (JWK) 终结点，其中包含用于对密钥（带有证书的私钥部分）进行签名的公钥。

请参阅 [TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .NET MVC 控制器示例。

#### <a name="step-1-prepare-a-self-signed-certificate"></a>步骤 1。 准备自签名证书

如果你还没有证书，则可以在本操作指南中使用自签名证书。 在 Windows 上，可使用 PowerShell [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) cmdlet 来生成证书。

运行此 PowerShell 命令来生成自签名证书。 根据应用程序的需要修改 `-Subject` 参数，并修改 Azure AD B2C 租户名称。 还可调整 `-NotAfter` 日期，为证书指定不同的过期日期。

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>步骤 2。 添加 ID 令牌提示技术配置文件 

以下技术配置文件会验证令牌并提取声明。 将元数据 URI 更改为令牌颁发者已知配置终结点。  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item>
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>步骤 3. 准备策略

完成[配置策略](#configure-your-policy)步骤。

#### <a name="step-4-prepare-the-code"></a>步骤 4. 准备代码 

此 [GitHub 示例](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET Web 应用程序生成 ID 令牌，并托管在 Azure AD B2C 中使用“id_token_hint”参数所需的元数据终结点。


### <a name="configure-your-policy"></a>配置策略

对于对称和非对称方法，`id_token_hint` 技术配置文件会从 `GetClaims` 类型的业务流程步骤中进行调用，并需要指定信赖方策略的输入声明。

1. 将 IdTokenHint_ExtractClaims 技术配置文件添加到扩展策略。
1. 将以下业务流程步骤作为第一项添加到用户旅程。  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. 在信赖方策略中，重复在 IdTokenHint_ExtractClaims 技术配置文件中配置的相同输入声明。 例如：
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

根据你的业务需求，你可能需要添加令牌验证（如检查电子邮件地址的格式）。 为此，请添加调用[声明转换技术配置文件](claims-transformation-technical-profile.md)的业务流程步骤。 还要添加[自断言技术配置文件](self-asserted-technical-profile.md)以显示错误消息。 

### <a name="create-and-sign-a-token"></a>创建令牌并对其进行签名

GitHub 示例演示了如何创建此类令牌，颁发稍后以 `id_token_hint` 查询字符串参数形式发送的 JWT。 下面是一个包含 id_token_hint 参数的授权请求的示例
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>后续步骤

- 请在 Azure AD B2C 社区 GitHub 存储库中查看[通过邀请电子邮件注册](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md)解决方案。
