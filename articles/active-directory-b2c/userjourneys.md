---
title: UserJourneys | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 UserJourneys 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/31/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2a935e88f1127f27e3f779fb88447466c470fd32
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123271910"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

用户旅程指定策略允许信赖方应用程序为用户获取所需声明的显式路径。 用户通过这些路径检索要提供给信赖方的声明。 换言之，用户旅程定义最终用户在 Azure AD B2C 标识体验框架处理请求时所经历的业务逻辑。

这些用户旅程可视为一些模板，可用于满足感兴趣社区的各种信赖方的核心需求。 用户旅程有助于定义策略的信赖方部分。 策略可以定义多个用户旅程。 每个用户旅程都是一系列的业务流程步骤。

若要定义策略支持的用户旅程，请在策略文件的顶级元素下添加 UserJourneys 元素。

UserJourneys 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | 定义完整用户流所需的所有构造的用户旅程。 |

UserJourney 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用户旅程的标识符，可用于从策略中的其他元素中引用它。 [信赖方策略](relyingparty.md)的 DefaultUserJourney 元素指向此属性。 |
| DefaultCpimIssuerTechnicalProfileReferenceId| 否 | 默认令牌颁发者技术配置文件引用 ID。 例如，[JWT 令牌颁发者](userjourneys.md)、[SAML 令牌颁发者](saml-issuer-technical-profile.md)或 [OAuth2 自定义错误](oauth2-error-technical-profile.md)。 如果用户旅程或子旅程已有另一个 `SendClaims` 业务流程步骤，请将 `DefaultCpimIssuerTechnicalProfileReferenceId` 特性设置为用户旅程的令牌颁发者技术配置文件。 |

UserJourney 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | 授权技术配置文件的列表。 | 
| OrchestrationSteps | 1:n | 成功事务必须遵循的业务流程序列。 每个用户旅程都包含按顺序执行的业务流程步骤的有序列表。 如果任何步骤失败，则事务将失败。 |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

假设用户已完成 UserJourney，并获得了访问权限或 ID 令牌。 若要管理其他资源（例如 [UserInfo 终结点](userinfo-endpoint.md)），必须标识该用户。 若要开始此过程，用户必须提供先前发布的访问令牌，以证明他们最初已通过有效的 Azure AD B2C 策略进行了身份验证。 在此过程中，用户必须始终提供有效令牌，以确保可以发出此请求。 授权技术配置文件验证传入的令牌并从令牌中提取声明。

AuthorizationTechnicalProfiles 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | 授权技术配置文件的列表。 | 

AuthorizationTechnicalProfile 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | 是 | 要执行的技术配置文件的标识符。 |

以下示例显示了具有授权技术配置文件的用户旅程元素：

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

用户旅程表示为成功事务必须遵循的业务流程序列。 如果任何步骤失败，则事务将失败。 这些业务流程步骤引用策略文件中允许的构建基块和声明提供程序。 负责显示或呈现用户体验的任何业务流程步骤也具有对相应内容定义标识符的引用。

业务流程步骤可以基于业务流程步骤元素中定义的前提条件有条件地执行。 例如，仅当存在特定声明或声明等于或未达到指定值时，才能检查执行业务流程步骤。

若要指定业务流程步骤的有序列表，请将 OrchestrationSteps 元素作为策略的一部分添加。 此元素是必需的。

OrchestrationSteps 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | 一个有序的业务流程步骤。 |

OrchestrationStep 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| `Order` | 是 | 业务流程步骤的顺序。 |
| `Type` | 是 | 业务流程步骤的类型。 可能的值： <ul><li>ClaimsProviderSelection - 指示业务流程步骤向用户提供各种声明提供程序以选择一个。</li><li>CombinedSignInAndSignUp - 指示业务流程步骤提供组合的社交提供程序登录和本地帐户注册页面。</li><li>ClaimsExchange - 指示业务流程步骤与声明提供程序交换声明。</li><li>**GetClaims** - 指定业务流程步骤应处理通过其 `InputClaims` 配置从信赖方发送到 Azure AD B2C 的声明数据。</li><li>**InvokeSubJourney** - 指示业务流程步骤与 [子历程](subjourneys.md)（为公共预览版）交换声明。</li><li>SendClaims - 指示业务流程步骤将声明发送给具有声明颁发者颁发的令牌的信赖方。</li></ul> |
| ContentDefinitionReferenceId | 否 | 与此业务流程步骤相关联的[内容定义](contentdefinitions.md)的标识符。 通常内容定义引用标识符在自断言的技术配置文件中定义。 但是，在某些情况下，Azure AD B2C 需要显示无技术配置文件的某些内容。 有两个示例 - 如果业务流程步骤的类型是以下类型之一：`ClaimsProviderSelection` 或 `CombinedSignInAndSignUp`，Azure AD B2C 需要在没有技术配置文件的情况下显示标识提供者选择。 |
| CpimIssuerTechnicalProfileReferenceId | 否 | 业务流程步骤的类型是 `SendClaims`。 此属性定义为信赖方颁发令牌的声明提供程序的技术配置文件标识符。  如果不存在，则不会创建任何信赖方令牌。 |

OrchestrationStep 元素可以包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | 为执行业务流程步骤而必须满足的前置条件列表。 |
| ClaimsProviderSelections | 0:n | 业务流程步骤的声明提供程序选择列表。 |
| ClaimsExchanges | 0:n | 业务流程步骤的声明交换列表。 |
| JourneyList | 0:1 | 业务流程步骤的子历程候选项的列表。 |

### <a name="preconditions"></a>Preconditions

业务流程步骤可基于业务流程步骤中定义的前提条件有条件地执行。 `Preconditions` 元素包含要评估的前提条件列表。 满足前提条件评估要求后，关联的业务流程步骤将跳转到下一个业务流程步骤。 

Azure AD B2C 按列表顺序评估前提条件。 基于顺序的前提条件可让你设置前提条件的应用顺序。 满足的第一个前提条件将替代所有后续前提条件。 仅当不满足所有前提条件时，才执行该业务流程步骤。 

Preconditions 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| Precondition | 1:n | 要评估的前提条件。 |

#### <a name="precondition"></a>Precondition

Precondition 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| `Type` | 是 | 要对此前置条件执行的检查或查询的类型。 值可以是 ClaimsExist（指定在用户当前声明集中存在指定声明时应执行操作）或 ClaimEquals（指定当指定声明存在且其值等于指定值时应执行操作）。 |
| `ExecuteActionsIf` | 是 | 确定在哪种情况下才认为满足前提条件。 可能的值为 `true`（默认）或 `false`。 如果值设置为 `true`，则当声明与前提条件匹配时，认为满足前提条件。  如果值设置为 `false`，则当声明与前提条件不匹配时，认为满足前提条件。  |

Precondition 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 值 | 1:2 | 声明类型的标识符。 已在策略文件或父策略文件的声明架构节中定义声明。 当前置条件为 `ClaimEquals` 类型时，第二个 `Value` 元素包含要检查的值。 |
| 操作 | 1:1 | 如果满足前提条件评估要求，则应执行该操作。 可能的值：`SkipThisOrchestrationStep`。 关联的业务流程步骤将跳转到下一个步骤。 |
  
每个前提条件的评估结果是单个声明。 有两种类型的前置条件：
 
- ClaimsExist - 指定如果用户的当前声明包中存在指定的声明，则应执行操作。
- ClaimEquals - 指定如果指定的声明存在且其值等于指定的值，则应执行操作。 该检查执行区分大小写的序号比较。 检查布尔声明类型时，请使用 `True` 或 `False`。 

    如果声明为 NULL 或未初始化，则忽略前提条件（无论 `ExecuteActionsIf` 是 `true` 还是 `false`）。 最佳做法是，同时检查声明是否存在，以及是否等于某个值。

一个示例场景是，如果用户将 `MfaPreference` 设置为 `Phone`，则要求用户执行 MFA。 若要执行此条件逻辑，请检查 `MfaPreference` 声明是否存在，并检查声明值是否等于 `Phone`。 以下 XML 演示如何使用前提条件实现此逻辑。 
  
```xml
<Preconditions>
  <!-- Skip this orchestration step if MfaPreference doesn't exist. -->
  <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
    <Value>MfaPreference</Value>
    <Action>SkipThisOrchestrationStep</Action>
  </Precondition>
  <!-- Skip this orchestration step if MfaPreference doesn't equal to Phone. -->
  <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
    <Value>MfaPreference</Value>
    <Value>Phone</Value>
    <Action>SkipThisOrchestrationStep</Action>
  </Precondition>
</Preconditions>
```

#### <a name="preconditions-examples"></a>Preconditions 示例

以下前置条件检查是否存在用户的 objectId。 在用户旅程中，用户已选择使用本地帐户进行登录。 如果存在 objectId，请跳过此业务流程步骤。

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

以下前置条件检查用户是否使用社交帐户登录。 已尝试在目录中查找用户帐户。 如果用户使用本地帐户登录或注册，请跳过此业务流程步骤。

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Preconditions 可以检查多个前置条件。 以下示例检查是否存在“objectId”或“电子邮件”。 如果第一个条件为 true，旅程将跳到下一个业务流程步骤。

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claims-provider-selection"></a>声明提供程序选择

通过声明提供程序选择，用户可以从选项列表中选择操作。 标识提供者选择包含一对两个业务流程步骤： 

1. 按钮 - 它从 `ClaimsProviderSelection` 或 `CombinedSignInAndSignUp` 类型开始，其中包含用户可从中选择的选项列表。 `ClaimsProviderSelections` 元素内的选项顺序控制向用户呈现的按钮顺序。
2. 操作 - 后跟 `ClaimsExchange` 类型。 ClaimsExchange 包含操作列表。 操作是对技术配置文件的引用，如 [OAuth2](oauth2-technical-profile.md)、[OpenID Connect](openid-connect-technical-profile.md)、[声明转换](claims-transformation-technical-profile.md)或[自断言](self-asserted-technical-profile.md)。 当用户单击其中一个按钮时，会执行相应的操作。

**ClaimsProviderSelections** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | 提供可以选择的声明提供程序的列表。|

**ClaimsProviderSelections** 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| DisplayOption| 否 | 控制单个声明提供程序选择可用时的行为。 可能的值：`DoNotShowSingleProvider`（默认值），用户将立即被重定向到联合标识提供者。 或 `ShowSingleProvider`，Azure AD B2C 会显示选择了单个标识提供者的登录页。 若要使用此属性，[内容定义版本](page-layout.md)必须为 `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` 及更高版本。|

ClaimsProviderSelection 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | 否 | 声明交换的标识符，在声明提供程序选择的下一个业务流程步骤中执行。 必须指定此属性或 ValidationClaimsExchangeId 属性，但不能同时指定这两个属性。 |
| ValidationClaimsExchangeId | 否 | 声明交换的标识符，在当前业务流程步骤中执行以验证声明提供程序选择。 必须指定此属性或 TargetClaimsExchangeId 属性，但不能同时指定这两个属性。 |

### <a name="claims-provider-selection-example"></a>声明提供程序选择示例

在以下业务流程步骤中，用户可以选择使用 Facebook、LinkedIn、Twitter、Google 或本地帐户登录。 如果用户选择其中一个社交标识提供者，则第二个业务流程步骤将使用 `TargetClaimsExchangeId` 属性中指定的所选声明交换执行。 第二个业务流程步骤将用户重定向到社交标识提供者以完成登录过程。 如果用户选择使用本地帐户登录，Azure AD B2C 将保持相同的业务流程步骤（相同的注册页面或登录页面），并跳过第二个业务流程步骤。

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

ClaimsExchanges 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | 具体取决于正在使用的技术配置文件，根据所选的 ClaimsProviderSelection 重定向客户端，或对交换声明进行服务器调用。 |

ClaimsExchange 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 声明交换步骤的标识符。 该标识符用于从策略中的声明提供程序选择步骤引用声明交换。 |
| TechnicalProfileReferenceId | 是 | 要执行的技术配置文件的标识符。 |

## <a name="journeylist"></a>JourneyList

JourneyList 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 候选对象 | 1:1 | 对要调用的子历程的引用。 |

### <a name="candidate"></a>候选对象

Candidate 元素包含以下属性：

| Attribute | 必需 | 描述 |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | 是 | 要执行的[子历程](subjourneys.md)的标识符。 |
