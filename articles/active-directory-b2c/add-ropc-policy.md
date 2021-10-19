---
title: 设置资源所有者密码凭据流
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中设置资源所有者密码凭据 (ROPC) 流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8a64c0e4a2c6ee080288bd0a700defc4adf598d2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708603"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置资源所有者密码凭据流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

在 Azure Active Directory B2C (Azure AD B2C) 中，资源所有者密码凭据 (ROPC) 流是一种 OAuth 标准身份验证流。 在此流中，应用程序（也称为信赖方）为令牌交换有效凭据。 凭据包括用户 ID 和密码。 返回的令牌为 ID 令牌、访问令牌和刷新令牌。

## <a name="ropc-flow-notes"></a>ROPC 流备注

在 Azure Active Directory B2C (Azure AD B2C) 中支持以下选项：

- **本机客户端**：当代码在用户端设备上运行时，将在身份验证期间进行用户交互。 设备可以是在本机操作系统（如 Android 或 iOS）中运行的移动应用程序。
- **公共客户端流**：只有通过应用程序收集的用户凭据才会在 API 调用中发送。 不会发送应用程序的凭据。
- **添加新声明**：可更改 ID 令牌内容以添加新的声明。

不支持以下流：

- **服务器到服务器**：标识保护系统在交互过程中需要从调用方（本地客户端）收集的可靠 IP 地址。 在服务器端 API 调用中，仅使用服务器的 IP 地址。 如果超过了失败身份验证的动态阈值，则标识保护系统可能将重复的 IP 地址识别为攻击者。
- **机密客户端流**：应用程序客户端 ID 已验证，但应用程序机密未验证。

使用 ROPC 流时，请考虑以下事项：

- 当需要用户交互的身份验证流发生任何中断时，ROPC 不起作用。 例如，当密码已过期或需要更改时、当需要[多重身份验证](multi-factor-authentication.md)时，或者在登录期间需要收集更多信息（例如，用户同意）时。
- ROPC 仅支持本地帐户。 用户无法使用[联合身份提供程序](add-identity-provider.md)（如 Microsoft、Google+、Twitter、AD-FS 或 Facebook）登录。
- [会话管理](session-behavior.md)（包括[使我保持登录状态 (KMSI)](session-behavior.md#enable-keep-me-signed-in-kmsi)）不适用。


## <a name="register-an-application"></a>注册应用程序

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>创建资源所有者用户流

1. 以 Azure AD B2C 租户的全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 确保正在使用包含 Azure AD B2C 租户的目录：
    1. 在门户工具栏中选择“目录 + 订阅”图标。
    1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“用户流”，然后选择“新建用户流”。
1. 选择“使用资源所有者密码凭据(ROPC)登录”。
1. 在“版本”下，确保选中“预览版”，然后选择“创建”。  
1. 提供用户流名称，例如 ROPC_Auth。
1. 在“应用程序声明”下，单击“显示更多”。
1. 选择应用程序所需的应用程序声明，例如“显示名称”、“电子邮件”和“标识提供者”。
1. 选择“确定”，然后选择“创建” 。

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>创建资源所有者策略

1. 打开 *TrustFrameworkExtensions.xml* 文件。
2. 如果尚不存在该文件，则添加 **ClaimsSchema** 元素及其子元素作为 **BuildingBlocks** 元素下的第一个元素：

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. 添加 **ClaimsSchema** 之后，再将 **ClaimsTransformations** 元素及其子元素添加到 **BuildingBlocks** 元素：

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. 找到 **DisplayName** 为 `Local Account SignIn` 的 **ClaimsProvider** 元素，并添加以下技术配置文件：

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    将 client_id 的 DefaultValue 替换为在先决条件教程中创建的 ProxyIdentityExperienceFramework 应用程序的应用程序 ID 。 将 resource_id 的 DefaultValue 替换为同样在先决条件教程中创建的 IdentityExperienceFramework 应用程序的应用程序 ID 。

5. 将以下 **ClaimsProvider** 元素及其技术配置文件添加到 **ClaimsProviders** 元素：

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. 将 **UserJourneys** 元素及其子元素添加到 **TrustFrameworkPolicy** 元素：

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略” 。
8. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
9. 单击“上载” 。

## <a name="create-a-relying-party-file"></a>创建信赖方文件

接下来，更新用于启动创建的用户旅程的信赖方文件：

1. 在工作目录中创建 *SignUpOrSignin.xml* 文件的副本，并将其重命名为 *ROPC_Auth.xml*。
2. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更改为唯一的值。 策略 ID 是策略的名称。 例如，**B2C_1A_ROPC_Auth**。
3. 将 **DefaultUserJourney** 中 **ReferenceId** 属性的值更改为 `ResourceOwnerPasswordCredentials`。
4. 将 **OutputClaims** 元素更改为仅包含以下声明：

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略” 。
6. 启用“覆盖策略(若存在)”，然后浏览到 ROPC_Auth.xml 文件并选中该文件。
7. 单击“上载” 。


::: zone-end

## <a name="test-the-ropc-flow"></a>测试 ROPC 流

使用最喜欢的 API 开发应用程序来生成 API 调用，然后查看响应以调试策略。 使用以下信息作为 POST 请求的正文，构造一个与此示例类似的调用：

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- 将 `<tenant-name>` 替换为 Azure AD B2C 租户的名称。
- 将 `B2C_1A_ROPC_Auth` 替换为资源所有者密码凭据策略的全名。

| 密钥 | Value |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- 将 `user-account` 替换为租户中某个用户帐户的名称。
- 将 `password1` 替换为该用户帐户的密码。
- 将 `application-id` 替换为 *ROPC_Auth_app* 注册中的应用程序 ID。
- 如果想要接收刷新令牌，则 *Offline_access* 是可选的。

实际的 POST 请求如以下示例所示：

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

脱机访问的成功响应如以下示例所示：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>兑换刷新令牌

构造一个与此处所示示例类似的 POST 调用。 使用下表中的信息作为请求的正文：

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- 将 `<tenant-name>` 替换为 Azure AD B2C 租户的名称。
- 将 `B2C_1A_ROPC_Auth` 替换为资源所有者密码凭据策略的全名。

| 密钥 | 值 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- 将 `application-id` 替换为 *ROPC_Auth_app* 注册中的应用程序 ID。
- 将 `refresh-token` 替换为上一个响应中发回的 **refresh_token**。

成功响应如以下示例所示：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="troubleshooting"></a>疑难解答

### <a name="the-provided-application-is-not-configured-to-allow-the-oauth-implicit-flow"></a>未将提供的应用程序配置为允许“OAuth”隐式流

* 症状 - 你运行 ROPC 流，并收到以下消息：“AADB2C90057: 提供的应用程序未配置为允许“OAuth”隐式流”。
* 可能的原因 - 不允许对应用程序使用隐式流。
* 解决方法：在 Azure AD B2C 中创建[应用注册](#register-an-application)时，需要手动编辑应用程序清单并将 `oauth2AllowImplicitFlow` 属性的值设置为 `true`。 配置 `oauth2AllowImplicitFlow` 属性后，更改可能需要几分钟（通常不超过五分钟）才能生效。 

## <a name="use-a-native-sdk-or-app-auth"></a>使用本机 SDK 或 App-Auth

Azure AD B2C 符合公共客户端资源所有者密码凭据的 OAuth 2.0 标准，并且应与大多数客户端 SDK 兼容。 有关最新信息，请参阅[适用于 OAuth 2.0 的本机应用 SDK 和实现新式最佳做法的 OpenID Connect](https://appauth.io/)。

## <a name="next-steps"></a>后续步骤

从 GitHub 下载已配置为与 Azure AD B2C 一起使用的工作示例：[用于 Android](https://aka.ms/aadb2cappauthropc) 和[用于 iOS](https://aka.ms/aadb2ciosappauthropc)。
