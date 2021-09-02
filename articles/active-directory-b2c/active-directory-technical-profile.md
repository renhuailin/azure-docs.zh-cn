---
title: 在自定义策略中定义 Azure AD 技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义 Azure Active Directory 技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a1894fe63bcf24e19671a146cd16bb6fe59b0b
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "122652659"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 Azure Active Directory 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 为 Azure Active Directory 用户管理提供支持。 本文介绍了与支持此标准化协议的声明提供程序进行交互的技术配置文件的详细信息。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 **handler** 属性必须包含协议处理程序程序集 `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` 的完全限定名称。

以下[自定义策略初学者包](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) Azure AD 技术配置文件包含 AAD-Common 技术配置文件。 Azure AD 技术配置文件不会指定协议，因为协议在 **AAD-Common** 技术配置文件中进行配置：
 
- **AAD-UserReadUsingAlternativeSecurityId** 和 **AAD-UserReadUsingAlternativeSecurityId-NoError** - 在目录中查找社交帐户。
- **AAD-UserWriteUsingAlternativeSecurityId** - 创建新的社交帐户。
- **AAD-UserReadUsingEmailAddress** - 在目录中查找本地帐户。
- **AAD-UserWriteUsingLogonEmail** - 创建新的本地帐户。
- **AAD-UserWritePasswordUsingObjectId** - 更新本地帐户的密码。
- **AAD-UserWriteProfileUsingObjectId** - 更新本地或社交帐户的用户配置文件。
- **AAD-UserReadUsingObjectId** - 读取本地或社交帐户的用户配置文件。
- **AAD-UserWritePhoneNumberUsingObjectId** - 写入本地或社交帐户的 MFA 电话号码

以下示例演示了 **AAD-Common** 技术配置文件：

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

InputClaims 元素包含一个声明，该声明用于在目录中查找帐户，或创建一个新帐户。 所有 Azure AD 技术配置文件的输入声明集合中必须有且只有一个 InputClaim 元素。 可能需要将策略中定义的声明名称映射到 Azure Active Directory 中定义的名称。

若要读取、更新或删除现有用户帐户，输入声明是在 Azure AD 目录中唯一标识该帐户的键。 例如，**objectId**、**userPrincipalName**、**signInNames.emailAddress**、**signInNames.userName** 或 **alternativeSecurityId**。 

若要新建用户帐户，输入声明是唯一标识本地帐户或联合帐户的键。 例如，对于本地帐户，为：**signInNames.emailAddress** 或 **signInNames.userName**。 对于联合帐户，为：**alternativeSecurityId**。

[InputClaimsTransformations](technicalprofiles.md#input-claims-transformations) 元素可以包含一组输入声明转换元素，这些元素用于修改输入声明或生成新的声明。

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 元素包含 Azure AD 技术配置文件返回的声明列表。 可能需要将策略中定义的声明名称映射到 Azure Active Directory 中定义的名称。 如果设置了 `DefaultValue` 属性，则还可以包含 Azure Active Directory 不会返回的声明。

[OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations) 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

例如，**AAD-UserWriteUsingLogonEmail** 技术配置文件可创建本地帐户并返回以下声明：

- **objectId**：新帐户的标识符
- **newUser**：指示用户是否为新用户
- **authenticationSource**：将身份验证设置为 `localAccountAuthentication`
- **userPrincipalName**：新帐户的用户主体名称
- **signInNames.emailAddress**：帐户登录名，类似于 **email** 输入声明

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** 元素包含 Azure AD 应持久保存的所有值，以及策略的 [ClaimsSchema](claimsschema.md) 节中已定义的声明类型与 Azure AD 属性名称之间可能的映射信息。

**AAD-UserWriteUsingLogonEmail** 技术配置文件，它可以创建新本地帐户并保存以下声明：

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

除非指定了包含 Azure AD 属性名称的 **PartnerClaimType** 属性，否则声明名称是 Azure AD 属性的名称。

## <a name="requirements-of-an-operation"></a>操作要求

- 所有 Azure AD 技术配置文件的声明包中必须刚好有一个 **InputClaim** 元素。
- [“用户配置文件属性”一文](user-profile-attributes.md)介绍了可在输入声明、输出声明和持久化声明中使用的受支持 Azure AD B2C 用户配置文件属性。 
- 如果操作为 `Write` 或 `DeleteClaims`，则 **PersistedClaims** 元素中也必须包含此操作。
- **userPrincipalName** 声明的值必须采用 `user@tenant.onmicrosoft.com` 格式。
- **displayName** 声明是必需的，不能为空字符串。

## <a name="azure-ad-technical-provider-operations"></a>Azure AD 技术提供程序操作

### <a name="read"></a>读取

**Read** 操作读取有关单个用户帐户的数据。 以下技术配置文件使用用户的 objectId 读取有关用户帐户的数据：

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>写入

**Write** 操作创建或更新单个用户帐户。 以下技术配置文件创建新社交帐户：

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

**DeleteClaims** 操作从提供的声明列表中清除信息。 以下技术配置文件删除声明：

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

**DeleteClaimsPrincipal** 操作从目录中删除单个用户帐户。 以下技术配置文件使用用户主体名称从目录中删除用户帐户：

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

以下技术配置文件使用 **alternativeSecurityId** 删除社交用户帐户：

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>元数据

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| 操作 | 是 | 要执行的操作。 可能的值：`Read`、`Write`、`DeleteClaims` 或 `DeleteClaimsPrincipal`。 |
| RaiseErrorIfClaimsPrincipalDoesNotExist | 否 | 如果目录中不存在该用户对象，则引发错误。 可能的值：`true` 或 `false`。 |
| RaiseErrorIfClaimsPrincipalAlreadyExists | 否 | 如果该用户对象已存在，则引发错误。 可能的值：`true` 或 `false`。|
| ApplicationObjectId | 否 | 扩展属性的应用程序对象标识符。 值：应用程序的 ObjectId。 有关详细信息，请参阅[使用自定义属性](user-flow-custom-attributes.md?pivots=b2c-custom-policy)。 |
| ClientId | 否 | 作为第三方访问租户的客户端标识符。 有关详细信息，请参阅[在自定义配置文件编辑策略中使用自定义属性](user-flow-custom-attributes.md?pivots=b2c-custom-policy) |
| IncludeClaimResolvingInClaimsHandling  | 否 | 对于输入和输出声明，指定[声明解析](claim-resolver-overview.md)是否包含在技术配置文件中。 可能的值：`true` 或 `false`（默认值）。 若要使用技术配置文件中的声明解析程序，请将此项设为 `true`。 |

### <a name="ui-elements"></a>UI 元素
 
以下设置可用于配置失败时显示的错误消息。 元数据应该在[自断言](self-asserted-technical-profile.md)技术配置文件中进行配置。 可以将错误消息[本地化](localization.md)。

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | 否 | 如果要引发错误（参阅 RaiseErrorIfClaimsPrincipalAlreadyExists 属性说明），则指定当用户对象已存在时要向用户显示的消息。 |
| UserMessageIfClaimsPrincipalDoesNotExist | 否 | 如果要引发错误（参阅 RaiseErrorIfClaimsPrincipalDoesNotExist 属性说明），则指定当用户对象不存在时要向用户显示的消息。 |


## <a name="next-steps"></a>后续步骤

有关使用 Azure AD 技术配置文件的示例，请参阅以下文章：

- [在 Azure Active Directory B2C 中使用自定义策略添加声明和自定义用户输入](configure-user-input.md)














