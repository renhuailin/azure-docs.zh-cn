---
title: 自定义策略中的条件访问技术配置文件
titleSuffix: Azure AD B2C
description: Azure AD B2C 中的条件访问技术配置文件的自定义策略参考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/18/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 902d9251c7393d3f6e693ba37c587398136493e6
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112368941"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义条件访问技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C 使用 Azure Active Directory (Azure AD) 条件访问作为工具来汇集信号、做出决策，以及实施组织策略。 通过策略条件自动执行风险评估意味着，一旦识别风险登录便可立即修正或阻止。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 handler 属性必须包含 Azure AD B2C 使用的协议处理程序程序集的完全限定名称：

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

以下示例显示了一个条件访问技术配置文件：

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>条件访问评估

每次登录时，Azure AD B2C 都会评估所有策略，确保只有满足所有要求时才向该用户授予访问权限。 “阻止访问”优先于所有其他配置设置。 在使用本地帐户登录期间，条件访问技术配置文件的“评估”模式会评估 Azure AD B2C 收集的信号。 条件访问技术配置文件的结果是一组由条件访问评估生成的声明。 Azure AD B2C 策略在下一个业务流程步骤中使用这些声明来执行操作，例如阻止用户或使用多重身份验证对用户进行质询。 可以为该模式配置以下选项。

### <a name="metadata"></a>元数据

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| OperationType | 是 | 必须是 **Evaluation**。  |

### <a name="input-claims"></a>输入声明

**InputClaims** 元素包含要发送到条件访问的声明的列表。 还可将声明名称映射到条件访问技术配置文件中定义的名称。

| ClaimReferenceId | 必须 | 数据类型 | 说明 |
| --------- | -------- | ----------- |----------- |
| UserId | 是 | 字符串 | 登录的用户的标识符。 |
| AuthenticationMethodsUsed | 是 |stringCollection | 用户用于登录的方法列表。 可能的值：`Password` 和 `OneTimePasscode`。 |
| IsFederated | 是 |boolean | 指示用户是否使用了联合帐户登录。 值必须是 `false`。 |
| IsMfaRegistered | 是 |boolean | 指示用户是否已注册了用于多重身份验证的电话号码。 |


InputClaimsTransformations 元素可能包含一系列 InputClaimsTransformation 元素，这些元素用于修改输入声明，或者用于生成新的声明并将其发送到条件访问服务。 

### <a name="output-claims"></a>输出声明

**OutputClaims** 元素包含由 ConditionalAccessProtocolProvider 生成的声明列表。 还可将声明名称映射到下面定义的名称。

| ClaimReferenceId | 必须 | 数据类型 | 说明 |
| --------- | -------- | ----------- |----------- |
| 挑战 | 是 |stringCollection | 用于修正查明的威胁的操作列表。 可能的值：`block`、`mfa` 和 `chg_pwd`。 |
| MultiConditionalAccessStatus | 是 | stringCollection | 条件访问评估的状态。  |

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="example-evaluation"></a>示例：计算

以下示例展示了用于评估登录威胁的条件访问技术配置文件。

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>补救

条件访问技术配置文件的“修正”模式会告知 Azure AD B2C 已修正登录过程中查明的威胁。 可以为修正模式配置以下选项。

### <a name="metadata"></a>元数据

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| OperationType | 是 | 必须是 **Remediation**。  |

### <a name="input-claims"></a>输入声明

**InputClaims** 元素包含要发送到条件访问的声明的列表。 还可将声明名称映射到条件访问技术配置文件中定义的名称。

| ClaimReferenceId | 必须 | 数据类型 | 说明 |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | 是 | stringCollection| 满足的质询列表（质询声明），用于修正从评估模式返回的已查明威胁。|


InputClaimsTransformations 元素可能包含一系列 InputClaimsTransformation 元素，这些元素用于修改输入声明，或者用于在调用条件访问服务之前生成新的声明。 

### <a name="output-claims"></a>输出声明

条件访问协议提供程序不返回任何 **OutputClaims**，因此无需指定输出声明。 但是，只要设置了 `DefaultValue` 属性，就可以包含条件访问协议提供程序不会返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="example-remediation"></a>示例：补救

以下示例展示了用于修正登录威胁的条件访问技术配置文件。

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>后续步骤

- 了解如何[向 Azure Active Directory B2C 中的用户流添加条件访问](conditional-access-user-flow.md)。
