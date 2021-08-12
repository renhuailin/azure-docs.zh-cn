---
title: 在自定义策略中定义 OAuth2 自定义错误技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义 OAuth2 自定义错误技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/26/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6fc4f9e7be394a8c63bb95969a2928c63b0c122d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962324"
---
# <a name="define-an-oauth2-custom-error-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 OAuth2 自定义错误技术配置文件

本文介绍如何使用 Azure Active Directory B2C (Azure AD B2C) 处理 OAuth2 自定义错误。 如果策略中的逻辑内容出现问题，请使用此技术配置文件。 该技术配置文件会将错误返回到 OAuth2 或 OpenId Connect 信赖方应用程序。

若要处理自定义 OAuth2 错误消息，请执行以下操作：

1. 定义 OAuth2 错误技术配置文件。
1. 设置错误代码，以及错误消息声明。
1. 从用户旅程调用 OAuth2 错误技术配置文件。

## <a name="oauth2-error"></a>OAuth2 错误

该错误会返回，包含以下数据：

- 错误 - `access_denied`
- error_description - 使用约定 `AAD_Custom_<errorCode>: <errorMessage>` 的错误消息。
- 相关 ID - Azure AD B2C 相关 ID。
- 时间戳 - 该错误的时间戳。

下面的示例演示了返回到 https://jwt.ms 应用的自定义错误消息：

```http
https://jwt.ms/#error=access_denied&error_description=AAD_Custom_1234%3a+My+custom+error+message%0d%0aCorrelation+ID%3a+233bf9bd-747a-4800-9062-6236f3f69a47%0d%0aTimestamp%3a+2021-03-25+14%3a01%3a23Z%0d%0a
```
  
## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `None`。 将 **OutputTokenFormat** 元素设置为 `OAuth2Error`。

以下示例演示了 `ReturnOAuth2Error` 的技术配置文件：

```xml
<!--
 <ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ReturnOAuth2Error">
        <DisplayName>Return OAuth2 error</DisplayName>
        <Protocol Name="None" />
        <OutputTokenFormat>OAuth2Error</OutputTokenFormat>
        <CryptographicKeys>
          <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
        </CryptographicKeys>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="errorCode" />
          <InputClaim ClaimTypeReferenceId="errorMessage" />
        </InputClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="input-claims"></a>输入声明

InputClaims 元素包含返回 OAuth2 错误所需声明的列表。 

| ClaimReferenceId | 必须 | 说明 |
| --------- | -------- | ----------- |
| errorCode | 是 | 错误代码。 | 
| errorMessage | 是 | 错误消息。 |

## <a name="cryptographic-keys"></a>加密密钥

CryptographicKeys 元素包含以下密钥：

| Attribute | 必须 | 说明 |
| --------- | -------- | ----------- |
| issuer_secret | 是  | X509 证书（RSA 密钥集）。 使用在[自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)中配置的 `B2C_1A_TokenSigningKeyContainer` 密钥。|
|

## <a name="invoke-the-technical-profile"></a>调用技术配置文件

你可以从用户旅程或子旅程中调用 OAuth2 错误技术配置文件。 将[业务流程步骤](userjourneys.md#orchestrationsteps)类型设置为 `SendClaims`，并对 OAuth2 错误技术配置文件进行引用。

如果用户旅程或子旅程已经有另一个 `SendClaims` 业务流程步骤，请将 `DefaultCpimIssuerTechnicalProfileReferenceId` 属性设置为令牌颁发者技术配置文件。

如下示例中：

-  用户旅程 `SignUpOrSignIn-Custom` 将 `DefaultCpimIssuerTechnicalProfileReferenceId` 设置为令牌颁发者技术配置文件 `JwtIssuer`。 
- 第八个业务流程步骤检查 `errorCode` 是否存在。 如果存在，则调用 `ReturnOAuth2Error` 技术配置文件，以返回错误。
- 如果 `errorCode` 不存在，则第九个业务流程步骤会颁发令牌。

```xml
<UserJourney Id="SignUpOrSignIn-Custom" DefaultCpimIssuerTechnicalProfileReferenceId="JwtIssuer">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="ReturnOAuth2Error">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>errorCode</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
    </OrchestrationStep>

    <OrchestrationStep Order="9" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />

  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="next-steps"></a>后续步骤

了解 [UserJourneys](userjourneys.md)