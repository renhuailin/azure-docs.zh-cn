---
title: 保护在 Azure AD B2C 中用作 API 连接器的 API
titleSuffix: Azure AD B2C
description: 保护在 Azure AD B2C 中用作 API 连接器的自定义 RESTful API。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f77c099425aac4f6484db8745e036a6dd1833ed4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578938"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-b2c"></a>保护在 Azure AD B2C 中用作 API 连接器的 API 

在 Azure AD B2C 用户流中集成 REST API 时，必须通过身份验证保护你的 REST API 终结点。 REST API 身份验证可确保只有具备适当凭据的服务（例如，Azure AD B2C）可以调用该终结点。 本文探讨如何保护 REST API。 


## <a name="prerequisites"></a>先决条件

完成[演练：向注册用户流添加 API 连接器](add-api-connector.md)指南中的步骤。

::: zone pivot="b2c-user-flow"

可以使用 HTTP 基本身份验证或 HTTPS 客户端证书身份验证来保护 API 终结点。 对于这两种情况，都要提供凭据，供 Azure AD B2C 在调用 API 终结点时使用。 然后，API 终结点将检查凭据并执行授权决策。

::: zone-end

## <a name="http-basic-authentication"></a>HTTP 基本身份验证

HTTP 基本身份验证在 [RFC 2617](https://tools.ietf.org/html/rfc2617) 中进行定义。 基本身份验证的工作方式如下：Azure AD B2C 使用 `Authorization` 标头中的客户端凭据（`username` 和 `password`）发送 HTTP 请求。 凭据的格式是 base64 编码的字符串 `username:password`。 然后，API 负责检查这些值以执行其他授权决策。

::: zone pivot="b2c-user-flow"

若要为 API 连接器配置 HTTP 基本身份验证，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure AD B2C”。
3. 选择“API 连接器”，然后选择要配置的“API 连接器” 。
4. 对于“身份验证类型”，请选择“基本”。 
5. 提供 REST API 终结点的“用户名”和“密码”。 
    :::image type="content" source="media/add-api-connector/api-connector-config.png" alt-text="为 API 连接器提供基本身份验证配置。":::
6. 选择“保存”。

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-rest-api-username-and-password-policy-keys"></a>添加 REST API 用户名和密码策略密钥

要使用 HTTP 基本身份验证配置 REST API 技术配置文件，请创建以下加密密钥来存储用户名和密码：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加” 。
1. 对于“选项”，请选择“手动” 。
1. 键入 RestApiUsername 作为名称 。
    可能会自动添加前缀 *B2C_1A_* 。
1. 在“密钥”框中，输入 REST API 用户名。
1. 对于“密钥用法”，请选择“加密” 。
1. 选择“创建”。
1. 再次选择“策略密钥”。
1. 选择 **添加** 。
1. 对于“选项”，请选择“手动” 。
1. 键入 RestApiPassword 作为名称 。
    可能会自动添加前缀 *B2C_1A_* 。
1. 在“密钥”框中，输入 REST API 密钥。
1. 对于“密钥用法”，请选择“加密” 。
1. 选择“创建”。

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>将 REST API 技术配置文件配置为使用 HTTP 基本身份验证

创建所需的密钥后，配置 REST API 技术配置文件元数据来引用凭据。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件， 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找到 `<Metadata>` 元素。
1. 将 AuthenticationType 更改为 `Basic`。
1. 将 AllowInsecureAuthInProduction 更改为 `false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

以下 XML 代码片段是已配置 HTTP 基本身份验证的 RESTful 技术配置文件示例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="https-client-certificate-authentication"></a>HTTPS 客户端证书身份验证

客户端证书身份验证是一种基于证书的相互身份验证，其中客户端 Azure AD B2C 向服务器提供客户端证书来证明自己的身份。 此操作在 SSL 握手期间发生。 API 负责验证属于有效客户端（例如 Azure AD B2C）的证书，并执行授权决策。 客户端证书是一种 X.509 数字证书。 

> [!IMPORTANT]
> 在生产环境中，证书必须由证书颁发机构进行签名。

### <a name="create-a-certificate"></a>创建证书

#### <a name="option-1-use-azure-key-vault-recommended"></a>选项 1：使用 Azure 密钥保管库（建议）

若要创建证书，可以使用 [Azure 密钥保管库](../key-vault/certificates/create-certificate.md)，其中提供了适用于自签名证书的选项，以及与证书颁发者提供程序相集成以便为证书签名的选项。 建议的设置包括：
- **使用者**：`CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **内容类型**：`PKCS #12`
- **生存期操作类型**：`Email all contacts at a given percentage lifetime` 或 `Email all contacts a given number of days before expiry`
- **密钥类型**：`RSA`
- **密钥大小**：`2048`
- 可导出的私钥：`Yes`（以便能够导出 `.pfx` 文件）

然后，你可以[导出该证书](../key-vault/certificates/how-to-export-certificate.md)。

#### <a name="option-2-prepare-a-self-signed-certificate-using-powershell-module"></a>选项 2：使用 PowerShell 模块准备自签名证书

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

::: zone pivot="b2c-user-flow"

### <a name="configure-your-api-connector"></a>配置 API 连接器

若要为 API 连接器配置客户端证书身份验证，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure AD B2C”。
3. 选择“API 连接器”，然后选择要配置的“API 连接器” 。
4. 对于“身份验证类型”，请选择“证书”。 
5. 在“上传证书”框中，选择包含私钥的证书 .pfx 文件。
6. 在“输入密码”框中，键入证书的密码。
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="为 API 连接器提供证书身份验证配置。":::
7. 选择“保存”。

### <a name="perform-authorization-decisions"></a>执行授权决策 
API 必须根据发送的客户端证书实现授权，以便保护 API 终结点。 对于 Azure 应用服务和 Azure Functions，请参阅[配置 TLS 相互身份验证](../app-service/app-service-web-configure-tls-mutual-auth.md)来了解如何通过 API 代码启用和验证证书。  也可以将 Azure API 管理用作任何 API 服务前面的层，以根据所需值[检查客户端证书属性](
../api-management/api-management-howto-mutual-certificates-for-clients.md)。

### <a name="renewing-certificates"></a>续订证书
建议针对证书过期时间设置提醒警报。 当所使用的证书即将到期时，需要生成新的证书并重复上述步骤。 为了“滚动”使用新证书，API 服务可以在部署新证书期间继续暂时接受新旧证书。 

若要将新证书上传到现有 API 连接器，请在“API 连接器”下选择该 API 连接器，然后单击“上传新证书”。  Azure AD B2C 将自动使用最近上传的未过期且已超过开始日期的证书。

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text=" API 连接器已存在，请向其提供新证书。":::

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-a-client-certificate-policy-key"></a>添加客户端证书策略密钥

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加” 。
1. 在“选项”框中，选择“上传”。 
1. 在“名称”框中，键入 RestApiClientCertificate 。
    前缀 *B2C_1A_* 会自动添加。
1. 在“文件上传”框中，选择包含私钥的证书 .pfx 文件。
1. 在“密码”框中，键入证书的密码。
1. 选择“创建”。

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>将 REST API 技术配置文件配置为使用客户端证书身份验证

创建所需的密钥后，配置 REST API 技术配置文件元数据来引用客户端证书。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件， 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找到 `<Metadata>` 元素。
1. 将 AuthenticationType 更改为 `ClientCertificate`。
1. 将 AllowInsecureAuthInProduction 更改为 `false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

以下 XML 代码片段是已配置 HTTP 客户端证书的 RESTful 技术配置文件示例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 持有者身份验证 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

持有者令牌身份验证在 [OAuth2.0 授权框架：持有者令牌用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) 中进行定义。 在持有者令牌身份验证中，Azure AD B2C 使用授权标头中的令牌发送 HTTP 请求。

```http
Authorization: Bearer <token>
```

持有者令牌是一种不透明字符串。 它可以是 JWT 访问令牌，也可以是 REST API 要求 Azure AD B2C 在授权标头中发送的任何字符串。 Azure AD B2C 支持以下类型：

- **持有者令牌**。 为了能够在 Restful 技术配置文件中发送持有者令牌，策略需要先获取持有者令牌，然后在 Restful 技术配置文件中使用它。  
- **静态持有者令牌**。 当 REST API 颁发长期访问令牌时使用此方法。 若要使用静态持有者令牌，请创建策略密钥，并从 RESTful 技术配置文件引用该策略密钥。 


## <a name="using-oauth2-bearer"></a>使用 OAuth2 持有者  

以下步骤演示了如何使用客户端凭据来获取持有者令牌并将它传递到 REST API 调用的授权标头中。  

### <a name="define-a-claim-to-store-the-bearer-token"></a>定义声明以存储持有者令牌

声明在 Azure AD B2C 策略执行过程中提供数据的临时存储。 [声明架构](claimsschema.md)是发出声明的位置。 必须将访问令牌存储在声明中供稍后使用。 

1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到 ClaimsSchema 元素。  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>获取访问令牌 

可通过以下几种方式之一获取访问令牌：[从联合身份验证标识提供程序](idp-pass-through-user-flow.md)获取、调用会返回访问令牌的 REST API、使用 [ROPC 流](../active-directory/develop/v2-oauth-ropc.md)、或者使用[客户端凭据流](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。 客户端凭据流通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。

#### <a name="acquiring-an-azure-ad-access-token"></a>获取 Azure AD 访问令牌 

下面的示例使用 REST API 技术配置文件和作为 HTTP 基本身份验证传递的客户端凭据向 Azure AD 令牌终结点发出请求。 有关详细信息，请参阅 [Microsoft 标识平台和 OAuth 2.0 客户端凭据流](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。 

你需要先注册应用程序，然后技术配置文件才能与 Azure AD 进行交互以获取访问令牌。 Azure AD B2C 依赖于 Azure AD 平台。 你可以在 Azure AD B2C 租户中或在你管理的任何 Azure AD 租户中创建应用。 注册应用程序：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD 或 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧菜单中，选择“Azure Active Directory”  。 或者选择“所有服务”，搜索并选择“Azure Active Directory”。 
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如 Client_Credentials_Auth_app。
1. 在“支持的帐户类型”下，选择“仅此组织目录中的帐户” 。
1. 选择“注册”。
1. 记录“应用程序(客户端) ID”。

对于客户端凭据流，需要创建应用程序机密。 客户端密码也称为应用程序密码。 应用程序将使用该机密来获取访问令牌。

1. 在“Azure AD - 应用注册”页中，选择已创建的应用程序，例如 Client_Credentials_Auth_app。
1. 在左侧菜单中“管理”下，选择“证书和机密”。 
1. 选择“新建客户端机密”。
1. 在“说明”框中输入客户端机密的说明。 例如，*clientsecret1*。
1. 在“过期时间”下，选择机密持续生效的时间，然后选择“添加”。
1. 记录密码的 **值**，以便在客户端应用程序代码中使用。 退出此页面后，此机密值永不再显示。 在应用程序的代码中将此值用作应用程序机密。

#### <a name="create-azure-ad-b2c-policy-keys"></a>创建 Azure AD B2C 策略密钥

需要存储前面在 Azure AD B2C 租户中记录的客户端 ID 和客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥 `SecureRESTClientId` 的“名称”。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中输入前面记录的客户端 ID。
1. 在“密钥用法”处选择 `Signature`。
1. 选择“创建”。
1. 使用以下设置创建另一个策略密钥：
    - **名称**：`SecureRESTClientSecret`。
    - **机密**：输入前面记录的客户端机密

对于 ServiceUrl，将 your-tenant-name 替换为 Azure AD 租户的名称。 有关所有可用选项，请参阅 [RESTful 技术配置文件](restful-technical-profile.md)参考。

```xml
<TechnicalProfile Id="REST-AcquireAccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>将 REST 技术配置文件更改为使用持有者令牌身份验证

若要在自定义策略中支持持有者令牌身份验证，请通过以下方式修改 REST API 技术配置文件：

1. 在工作目录中，打开 *TrustFrameworkExtensions.xml* 扩展策略文件。
1. 搜索包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 节点。
1. 找到 `<Metadata>` 元素。
1. 将 AuthenticationType 更改为 Bearer，如下所示 ：
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 将 UseClaimAsBearerToken 更改为 bearerToken 或添加此设置，如下所示 。 bearerToken 是将从其检索持有者令牌的声明（来自 `REST-AcquireAccessToken` 的输出声明）的名称。

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 请确保将上述使用的声明添加为输入声明：

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

添加上述片段后，技术配置文件应与以下 XML 代码类似：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>使用静态 OAuth2 持有者 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>添加 OAuth2 持有者令牌策略密钥

若要为 REST API 技术配置文件配置 OAuth2 持有者令牌，请从 REST API 所有者获取访问令牌。 然后，创建以下加密密钥来存储持有者令牌。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加” 。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`RestApiBearerToken`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密。
1. 在“密钥用法”处选择 `Encryption`。
1. 选择“创建”。

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>将 REST API 技术配置文件配置为使用持有者令牌政策密钥

创建所需的密钥后，配置 REST API 技术配置文件元数据来引用持有者令牌。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件， 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找到 `<Metadata>` 元素。
1. 将 AuthenticationType 更改为 `Bearer`。
1. 将 AllowInsecureAuthInProduction 更改为 `false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

以下 XML 代码片段是已配置持有者令牌身份验证的 RESTful 技术配置文件示例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end


## <a name="api-key-authentication"></a>API 密钥身份验证

::: zone pivot="b2c-user-flow"

某些服务使用“API 密钥”机制，要求调用方包含一个唯一密钥作为 HTTP 头或 HTTP 查询参数，以模糊处理在开发过程中对 HTTP 端点的访问。 对于 [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)，可以通过在 API 连接器的“终结点 URL”中将 `code` 添加为查询参数来实现此目的。 例如 `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>。 

在生产环境中不应单独使用此机制。 因此，始终需要基本身份验证或证书身份验证的配置。 如果不希望出于开发目的实现任何身份验证方法（不建议），可以在 API 连接器配置中选择“基本”身份验证，并对 `username` 和 `password` 使用临时值，API 在实现适当的授权时可忽略这些值。

::: zone-end

::: zone pivot="b2c-custom-policy"

API 密钥是用于对要访问 REST API 终结点的用户进行身份验证的唯一标识符。 该密钥是在自定义 HTTP 标头中发送的。 例如，[Azure Functions HTTP 触发器](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)使用 `x-functions-key` HTTP 标头来标识请求者。  

### <a name="add-api-key-policy-keys"></a>添加 API 密钥策略密钥

要为 REST API 技术配置文件配置 API 密钥身份验证，请创建以下加密密钥来存储 API 密钥：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加” 。
1. 对于“选项”，请选择“手动” 。
1. 键入“RestApiKey”作为名称 。
    可能会自动添加前缀 *B2C_1A_* 。
1. 在“机密”框中，输入 REST API 密钥。
1. 对于“密钥用法”，请选择“加密” 。
1. 选择“创建”。


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>将 REST API 技术配置文件配置为使用 API 密钥身份验证

创建必要的密钥后，配置 REST API 技术配置文件元数据来引用凭据。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件， 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找到 `<Metadata>` 元素。
1. 将 AuthenticationType 更改为 `ApiKeyHeader`。
1. 将 AllowInsecureAuthInProduction 更改为 `false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

加密密钥的 Id 定义 HTTP 标头。 在此示例中，API 密钥作为 x-functions-key 发送。

以下 XML 代码片段是已配置为通过 API 密钥身份验证调用 Azure 函数的 RESTful 技术配置文件示例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="next-steps"></a>后续步骤

::: zone pivot="b2c-user-flow"
- 参考我们的[示例](api-connector-samples.md#api-connector-rest-api-samples)帮助自己入门。
::: zone-end

::: zone pivot="b2c-custom-policy"
- 详细了解自定义策略参考中的 [Restful 技术配置文件](restful-technical-profile.md)元素。
::: zone-end
