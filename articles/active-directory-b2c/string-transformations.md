---
title: 自定义策略的字符串声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的 Identity Experience Framework (IEF) 架构的字符串声明转换示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 176c36ee5c3addf655503e3a371767764e0d9968
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108738046"
---
# <a name="string-claims-transformations"></a>字符串声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了在 Azure Active Directory B2C (Azure AD B2C) 中使用标识体验框架架构的字符串声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

比较两个声明，如果根据指定的比较 inputClaim1、inputClaim2 和 stringComparison 它们不相等，将引发异常。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字符串 | 要比较的第一个声明的类型。 |
| InputClaim | inputClaim2 | 字符串 | 要比较的第二个声明的类型。 |
| InputParameter | stringComparison | 字符串 | 字符串比较，值为下列其中一项：Ordinal、OrdinalIgnoreCase。 |

AssertStringClaimsAreEqual 声明转换始终从一个[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)或 [DisplayControl](display-controls.md) 调用。 自断言技术配置文件的 `UserMessageIfClaimsTransformationStringsAreNotEqual` 元数据控制向用户显示的错误消息。 可以将错误消息[本地化](localization-string-ids.md#claims-transformations-error-messages)。


![AssertStringClaimsAreEqual 执行](./media/string-transformations/assert-execution.png)

可以使用此声明转换来确保两个 ClaimTypes 具有相同的值。 如果没有，则会引发错误消息。 以下示例检查 strongAuthenticationEmailAddress ClaimType 是否等同于 email ClaimType， 否则会引发错误消息。

```xml
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


login-NonInteractive 验证技术配置文件调用 AssertEmailAndStrongAuthenticationEmailAddressAreEqual 声明转换。
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

自断言技术配置文件调用验证 login-NonInteractive 技术配置文件。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>示例

- 输入声明：
  - inputClaim1: someone@contoso.com
  - inputClaim2: someone@outlook.com
- 输入参数：
  - stringComparison: ordinalIgnoreCase
- 结果：引发错误

## <a name="changecase"></a>ChangeCase

将所提供的声明更改为小写或大写，具体要取决于运算符。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字符串 | 要更改的 ClaimType。 |
| InputParameter | toCase | 字符串 | 以下值之一：`LOWER` 或 `UPPER`。 |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后生成的 ClaimType。 |

使用此声明转换将任何字符串 ClaimType 更改为小写或大写。

```xml
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - email: SomeOne@contoso.com
- 输入参数：
    - **toCase**：LOWER
- 输出声明：
  - email: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

基于转换中提供的输入参数创建字符串声明。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
|----- | ----------------------- | --------- | ----- |
| InputParameter | 值 | 字符串 | 要设置的字符串。 此输入参数支持[字符串声明转换表达式](string-transformations.md#string-claim-transformations-expressions)。 |
| OutputClaim | createdClaim | 字符串 | 调用此声明转换后生成的 ClaimType，其值在输入参数中指定。 |

使用此声明转换设置一个字符串 ClaimType 值。

```xml
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入参数：
    - **value**：Contoso 服务条款...
- 输出声明：
    - **createdClaim**：TOS ClaimType 包含“Contoso 服务条款...”值。

## <a name="copyclaimifpredicatematch"></a>CopyClaimIfPredicateMatch

如果输入声明的值与输出声明谓词匹配，则将一个声明的值复制到另一个声明的值。 

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 要复制的声明类型。 |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后生成的声明类型。 根据此声明谓词检查输入声明的值。 |

仅当 signInName 为电话号码时，以下示例才会将 signInName 声明值复制到 phoneNumber 声明。 有关完整的示例，请参阅[电话号码或电子邮件登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/Phone_Email_Base.xml)初学者包策略。

```xml
<ClaimsTransformation Id="SetPhoneNumberIfPredicateMatch" TransformationMethod="CopyClaimIfPredicateMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>示例 1

- 输入声明：
    - **inputClaim**: bob@contoso.com
- 输出声明：
    - outputClaim：不会更改输出声明的原始值。

### <a name="example-2"></a>示例 2

- 输入声明：
    - inputClaim：+11234567890
- 输出声明：
    - **outputClaim**：+11234567890

## <a name="compareclaims"></a>CompareClaims

确定一个字符串声明是否等于另一个字符串声明。 结果是新布尔型 ClaimType，值为 `true` 或 `false`。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字符串 | 要比较的第一个声明类型。 |
| InputClaim | inputClaim2 | 字符串 | 要比较的第二个声明类型。 |
| InputParameter | 运算符后的表达式 | 字符串 | 可能的值：`EQUAL` 或 `NOT EQUAL`。 |
| InputParameter | ignoreCase | boolean | 指定此比较是否应忽略所比较字符串的大小写。 |
| OutputClaim | outputClaim | boolean | 调用此声明转换后生成的 ClaimType。 |

使用此声明转换检查一个声明是否等于另一个声明。 例如，以下声明转换检查 email 声明的值是否等于 Verified.Email 声明的值。

```xml
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - inputClaim1: someone@contoso.com
  - inputClaim2: someone@outlook.com
- 输入参数：
    - **operator**：不等于
    - ignoreCase: true
- 输出声明：
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

确定声明值是否等于输入参数值。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字符串 | 要比较的声明类型。 |
| InputParameter | 运算符后的表达式 | 字符串 | 可能的值：`EQUAL` 或 `NOT EQUAL`。 |
| InputParameter | compareTo | 字符串 | 字符串比较，值为下列其中一项：Ordinal、OrdinalIgnoreCase。 |
| InputParameter | ignoreCase | boolean | 指定此比较是否应忽略所比较字符串的大小写。 |
| OutputClaim | outputClaim | boolean | 调用此声明转换后生成的 ClaimType。 |

可以使用此声明转换检查一个声明是否等于指定的值。 例如，以下声明转换将检查 termsOfUseConsentVersion 声明的值是否等于 `v1`。

```xml
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例
- 输入声明：
    - inputClaim1: v1
- 输入参数：
    - **compareTo**:V1
    - **operator**：EQUAL
    - ignoreCase:  true
- 输出声明：
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

使用随机数生成器创建随机字符串。 如果随机数生成器是 `integer` 类型，则可以选择提供种子参数和最大数。 可选字符串格式参数允许使用它来格式化输出，可选的 base64 参数指定输出是否为 base64 编码的 encoded randomGeneratorType [guid, integer] outputClaim（字符串）。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | 字符串 | 指定要生成的随机值，`GUID`（全局唯一 ID）或 `INTEGER`（数字）。 |
| InputParameter | stringFormat | 字符串 | [可选]格式化随机值。 |
| InputParameter | base64 | boolean | [可选]将随机值转换为 base64。 如果应用字符串格式，则字符串格式之后的值将被编码为 base64。 |
| InputParameter | maximumNumber | int | [可选]仅限 `INTEGER` randomGeneratorType。 指定最大数。 |
| InputParameter | seed  | int | [可选]仅限 `INTEGER` randomGeneratorType。 指定随机值的种子。 注意：同一个种子生成相同的随机数字序列。 |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后将生成的 ClaimTypes。 随机值。 |

下面的示例将生成全局唯一 ID。 此声明转换用于创建随机 UPN（用户主体名称）。

```xml
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>示例

- 输入参数：
    - **randomGeneratorType**：GUID
- 输出声明：
    - outputClaim: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

下面的示例生成 0 到 1000 之间的随机整数值。 值被格式化为 OTP_{random value}。

```xml
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入参数：
    - **randomGeneratorType**：INTEGER
    - **maximumNumber**：1000
    - **stringFormat**：OTP_{0}
    - base64: false
- 输出声明：
    - **outputClaim**：OTP_853


## <a name="formatlocalizedstring"></a>FormatLocalizedString

根据提供的本地化格式字符串来格式化多个声明。 此转换将使用 C# `String.Format` 方法。


| 项目 | TransformationClaimType | 数据类型 | 注释 |
| ---- | ----------------------- | --------- | ----- |
| InputClaims |  |字符串 | 充当字符串格式 {0}、{1}、{2} 参数的输入声明的集合。 |
| InputParameter | stringFormatId | 字符串 |  [本地化字符串](localization.md)的 `StringId`。   |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后生成的 ClaimType。 |

> [!NOTE]
> 允许的字符串格式最大大小为 4000。

若要使用 FormatLocalizedString 声明转换：

1. 定义[本地化字符串](localization.md)，并将其与 [self-asserted-technical-profile](self-asserted-technical-profile.md) 相关联。
1. `LocalizedString` 元素的 `ElementType` 必须设为 `FormatLocalizedStringTransformationClaimType`。
1. `StringId` 是定义的唯一标识符，稍后会用于声明转换 `stringFormatId`。
1. 在声明转换中，指定要使用本地化字符串设置的声明列表。 然后，将 `stringFormatId` 设置为本地字符串元素的 `StringId`。 
1. 在[自我断言技术配置文件](self-asserted-technical-profile.md)或[显示控制](display-controls.md)输入或输出声明转换中引用你的声明转换。


当帐户已经在目录中时，下面的示例将生成一条错误消息。 该示例针对英语（默认）和西班牙语定义了本地化字符串。

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">Este correo electrónico "{0}" ya es una cuenta de esta organización. Haga clic en Siguiente para iniciar sesión con esa cuenta.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

声明转换基于本地化的字符串创建响应消息。 此消息包含嵌入到本地化字符串 ResponseMessge_EmailExists 中的用户电子邮件地址。

```xml
<ClaimsTransformation Id="SetResponseMessageForEmailAlreadyExists" TransformationMethod="FormatLocalizedString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormatId" DataType="string" Value="ResponseMessge_EmailExists" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**: sarah@contoso.com
- 输入参数：
    - **stringFormat**：ResponseMessge_EmailExists
- 输出声明：
  - **outputClaim**：电子邮件地址“sarah@contoso.com”已是此组织中的帐户。 单击“下一步”，使用该帐户进行登录。


## <a name="formatstringclaim"></a>FormatStringClaim

根据提供的格式字符串格式化声明。 此转换将使用 C# `String.Format` 方法。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |字符串 |作为字符串格式 {0} 参数的 ClaimType。 |
| InputParameter | stringFormat | 字符串 | 字符串格式，包括 {0} 参数。 此输入参数支持[字符串声明转换表达式](string-transformations.md#string-claim-transformations-expressions)。  |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后生成的 ClaimType。 |

> [!NOTE]
> 允许的字符串格式最大大小为 4000。

使用此声明转换格式化任何带一个参数 {0} 的字符串。 以下示例创建一个 userPrincipalName。 所有社交标识提供者技术配置文件（如 `Facebook-OAUTH`）都调用 CreateUserPrincipalName 来生成 userPrincipalName。

```xml
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**：5164db16-3eee-4629-bfda-dcc3326790e9
- 输入参数：
    - stringFormat:  cpim_{0}@{RelyingPartyTenantId}
- 输出声明：
  - outputClaim: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

根据提供的格式字符串格式化两个声明。 此转换将使用 C# `String.Format` 方法。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |字符串 | 作为字符串格式 {0} 参数的 ClaimType。 |
| InputClaim | inputClaim | 字符串 | 作为字符串格式 {1} 参数的 ClaimType。 |
| InputParameter | stringFormat | 字符串 | 字符串格式，包括 {0} 和 {1} 参数。 此输入参数支持[字符串声明转换表达式](string-transformations.md#string-claim-transformations-expressions)。   |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后生成的 ClaimType。 |

> [!NOTE]
> 允许的字符串格式最大大小为 4000。

使用此声明转换格式化任何带两个参数 {0} 和 {1} 的字符串。 下面的示例创建带指定格式的 displayName：

```xml
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim1**：Joe
    - **inputClaim2**：Fernando
- 输入参数：
    - **stringFormat**：{0} {1}
- 输出声明：
    - **outputClaim**：Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

将本地化的字符串复制到声明中。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | 本地化的字符串的名称 | 字符串 | 调用此声明转换后生成的声明类型列表。 |

使用 GetLocalizedStringsTransformation 声明转换：

1. 定义[本地化字符串](localization.md)并将其与 [self-asserted-technical-profile](self-asserted-technical-profile.md) 相关联。
1. `LocalizedString` 元素的 `ElementType` 必须设为 `GetLocalizedStringsTransformationClaimType`。
1. `StringId` 是定义的唯一标识符，稍后会用于声明转换。
1. 在声明转换中，指定要使用本地化字符串设置的声明列表。 `ClaimTypeReferenceId` 是对在策略的 ClaimsSchema 节中已定义的 ClaimType 的引用。 `TransformationClaimType` 是在 `LocalizedString` 元素的 `StringId` 中定义的已本地化字符串的名称。
1. 在[自我断言技术配置文件](self-asserted-technical-profile.md)或[显示控制](display-controls.md)输入或输出声明转换中引用你的声明转换。

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

以下示例在本地化的字符串中查找电子邮件主题、正文、代码消息和电子邮件签名。 稍后自定义电子邮件验证模板将使用这些声明。

定义英语（默认）和西班牙语的已本地化字符串。

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

声明转换使用 `StringId` 值 *email_subject* 设置声明类型 *subject* 的值。

```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输出声明：
  - **subject**：Contoso 帐户电子邮件验证码
  - **message**：感谢验证你的帐户！
  - **codeIntro**：你的代码是
  - **signature**：此致


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

从声明 Restriction 集合中查找项。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | 字符串 | 该声明包含要在带 Restriction 集合的 restrictionValueClaim 声明中查找的文本。  |
| OutputClaim | restrictionValueClaim | 字符串 | 包含 Restriction 集合的声明。 在调用声明转换后，此声明的值将包含选定项的值。 |

下面的示例基于错误密钥查找错误消息描述。 ResponseMsg 声明包含一系列要显示给最终用户或发送给信赖方的错误消息。

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
声明转换将查找项文本，并返回其值。 如果使用 `<LocalizedCollection>` 本地化限制，则声明转换返回本地化的值。

```xml
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **mapFromClaim**：B2C_V1_90001
- 输出声明：
    - **restrictionValueClaim**：无法登录，因为你未成年。

## <a name="lookupvalue"></a>LookupValue

基于另一个声明的值从值列表中查找声明值。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | 字符串 | 包含查找值的声明 |
| InputParameter | |字符串 | inputParameters 集合。 |
| InputParameter | errorOnFailedLookup | boolean | 控制在没有任何匹配查找时是否返回错误。 |
| OutputClaim | inputParameterId | 字符串 | 调用此声明转换后将生成的 ClaimTypes。 匹配 `Id` 的值。 |

下面的示例在某一个 inputParameters 集合中查找域名。 声明转换查找标识符中的域名，并返回其值（应用程序 ID）。

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - inputParameterId: test.com
- 输入参数：
    - **contoso.com**：13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**：0213308f-17cb-4398-b97e-01da7bd4804e
    - test.com: c7026f88-4299-4cdb-965d-3f166464b8a9
    - errorOnFailedLookup: false
- 输出声明：
    - outputClaim：c7026f88-4299-4cdb-965d-3f166464b8a9

将 `errorOnFailedLookup` 输入参数设置为 `true` 时，LookupValue 声明转换始终从[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)或 [DisplayConrtol](display-controls.md) 调用。 自断言技术配置文件的 `LookupNotFound` 元数据控制向用户显示的错误消息。

![AssertStringClaimsAreEqual 执行](./media/string-transformations/assert-execution.png)

下面的示例在某一个 inputParameters 集合中查找域名。 声明转换查找标识符中的域名，并返回其值（应用程序 ID）或引发错误消息。

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputParameterId**: live.com
- 输入参数：
    - **contoso.com**：13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**：0213308f-17cb-4398-b97e-01da7bd4804e
    - test.com: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- 错误：
    - 在输入参数 ID 列表中找不到输入声明值的匹配项，errorOnFailedLookup 为 true。


## <a name="nullclaim"></a>NullClaim

清除给定声明的值。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | 字符串 | 声明的值设置为 NULL。 |

使用此声明转换可从声明属性包中删除不必要的数据，因此会话 Cookie 会更小。 以下示例将删除 `TermsOfService` 声明类型的值。

```xml
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- 输入声明：
    - **outputClaim**：欢迎使用 Contoso 应用。 如果继续浏览和使用本网站，表示你同意遵守并受下列条款和条件的约束...
- 输出声明：
    - **outputClaim**：Null

## <a name="parsedomain"></a>ParseDomain

获取电子邮件地址的域部分。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | 字符串 | 包含电子邮件地址的 ClaimType。 |
| OutputClaim | 域 | 字符串 | 调用此声明转换后生成的 ClaimType - 域。 |

使用此声明转换分析用户 @ 符号之后的域名。 以下声明转换演示如何分析 email 声明中的域名。

```xml
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - emailAddress: joe@outlook.com
- 输出声明：
    - domain: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

检查字符串声明 `claimToMatch` 和 `matchTo` 输入参数是否相等，并使用 `outputClaimIfMatched` 输入参数中提供的值设置输出声明，以及比较结果输出声明，将基于比较结果将此声明设置为 `true` 或 `false`。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | 字符串 | 要比较的声明类型。 |
| InputParameter | matchTo | 字符串 | 要匹配的正则表达式。 |
| InputParameter | outputClaimIfMatched | 字符串 | 字符串相等情况下要设置的值。 |
| InputParameter | extractGroups | boolean | [可选] 指定正则表达式匹配是否应提取组值。 可能的值：`true` 或 `false`（默认值）。 | 
| OutputClaim | outputClaim | 字符串 | 如果正则表达式是匹配项，此输出声明包含 `outputClaimIfMatched` 输入参数的值。 如果没有匹配项，则为 null。 |
| OutputClaim | regexCompareResultClaim | boolean | 正则表达式匹配结果输出声明类型，将基于匹配结果设置为 `true` 或 `false`。 |
| OutputClaim| 声明名称| 字符串 | 如果 extractGroups 输入参数设置为 true，调用此声明转换后生成的声明类型列表。 ClaimType 的名称必须与正则表达式组名称匹配。 | 

### <a name="example-1"></a>示例 1

根据电话号码正则表达式模式检查提供的电话号码是否有效。

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- 输入声明：
    - **claimToMatch**："64854114520"
- 输入参数：
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**:  "isPhone"
- 输出声明：
    - **outputClaim**: "isPhone"
    - regexCompareResultClaim：true

### <a name="example-2"></a>示例 2

检查提供的电子邮件地址是否有效，并返回电子邮件别名。

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- 输入声明：
    - **claimToMatch**: "emily@contoso.com"
- 输入参数：
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**:  "isEmail"
    - **extractGroups**: true
- 输出声明：
    - **outputClaim**: "isEmail"
    - regexCompareResultClaim：true
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

检查字符串声明和 `matchTo` 输入参数是否相等，并使用 `stringMatchMsg` 和 `stringMatchMsgCode` 输入参数中提供的值设置输出声明，以及比较结果输出声明，将基于比较结果将此声明设置为 `true` 或 `false`。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 要比较的声明类型。 |
| InputParameter | matchTo | 字符串 | 要与 `inputClaim` 进行比较的字符串。 |
| InputParameter | stringComparison | 字符串 | 可能的值：`Ordinal` 或 `OrdinalIgnoreCase`。 |
| InputParameter | stringMatchMsg | 字符串 | 字符串相等情况下第一个要设置的值。 |
| InputParameter | stringMatchMsgCode | 字符串 | 字符串相等情况下第二个要设置的值。 |
| OutputClaim | outputClaim1 | 字符串 | 如果字符串相等，则此输出声明包含 `stringMatchMsg` 输入参数的值。 |
| OutputClaim | outputClaim2 | 字符串 | 如果字符串相等，则此输出声明包含 `stringMatchMsgCode` 输入参数的值。 |
| OutputClaim | stringCompareResultClaim | boolean | 比较结果输出声明类型，将基于比较结果设置为 `true` 或 `false`。 |

可以使用此声明转换检查一个声明是否等于指定的值。 例如，以下声明转换将检查 termsOfUseConsentVersion 声明的值是否等于 `v1`。 如果是，则将值更改为 `v2`。

```xml
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>示例

- 输入声明：
    - inputClaim: v1
- 输入参数：
    - **matchTo**：V1
    - stringComparison: ordinalIgnoreCase
    - **stringMatchMsg**：B2C_V1_90005
    - **stringMatchMsgCode**：TOS 升级到 v2
- 输出声明：
    - **outputClaim1**：B2C_V1_90005
    - **outputClaim2**：TOS 升级到 v2
    - stringCompareResultClaim: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

检查字符串声明和 `matchTo` 输入参数是否相等，并使用 `outputClaimIfMatched` 输入参数中提供的值设置输出声明，以及比较结果输出声明，将基于比较结果将此声明设置为 `true` 或 `false`。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | 字符串 | 要比较的声明类型。 |
| InputParameter | matchTo | 字符串 | 要与 inputClaim 进行比较的字符串。 |
| InputParameter | stringComparison | 字符串 | 可能的值：`Ordinal` 或 `OrdinalIgnoreCase`。 |
| InputParameter | outputClaimIfMatched | 字符串 | 字符串相等情况下要设置的值。 |
| OutputClaim | outputClaim | 字符串 | 如果字符串相等，则此输出声明包含 `outputClaimIfMatched` 输入参数的值。 或者如果字符串不匹配，则为 NULL。 |
| OutputClaim | stringCompareResultClaim | boolean | 比较结果输出声明类型，将基于比较结果设置为 `true` 或 `false`。 |

例如，以下声明转换检查 ageGroup 声明的值是否等于 `Minor`。 如果是，则返回 `B2C_V1_90001` 值。

```xml
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **claimToMatch**：Minor
- 输入参数：
    - **matchTo**：Minor
    - stringComparison: ordinalIgnoreCase
    - **outputClaimIfMatched**：B2C_V1_90001
- 输出声明：
    - **isMinorResponseCode**：B2C_V1_90001
    - isMinor: true


## <a name="stringcontains"></a>StringContains

确定输入声明中是否出现了指定的子字符串。 结果是新布尔型 ClaimType，值为 `true` 或 `false`。 如果此字符串中出现了值参数，则为 `true`；否则为 `false`。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 要搜索的声明类型。 |
|InputParameter|contains|字符串|要搜索的值。|
|InputParameter|ignoreCase|字符串|指定此项比较是否应忽略所比较字符串的大小写。|
| OutputClaim | outputClaim | 字符串 | 调用此 ClaimsTransformation 后生成的 ClaimType。 一个布尔值，指示输入声明中是否出现了该子字符串。 |

使用此声明转换可以检查字符串声明类型是否包含某个子字符串。 以下示例检查 `roles` 字符串声明类型是否包含 **admin** 值。

```xml
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**："Admin, Approver, Editor"
- 输入参数：
    - **contains**: "admin,"
    - ignoreCase: true
- 输出声明：
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

提取字符串声明类型的组成部分（从位于指定位置处的字符开始），并返回指定数目的字符。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 包含该字符串的声明类型。 |
| InputParameter | startIndex | int | 此实例中子字符串的起始字符位置（从零开始）。 |
| InputParameter | length | int | 子字符串中的字符数。 |
| OutputClaim | outputClaim | boolean | 与此实例中在 startIndex 处开头、具有指定长度的子字符串等效的一个字符串；如果 startIndex 等于此实例的长度且长度为零，则为空。 |

例如，获取国家/地区电话号码前缀。


```xml
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**: "+1644114520"
- 输入参数：
    - **startIndex**:0
    - **length**:2
- 输出声明：
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>StringReplace

在声明类型字符串中搜索指定的值，并返回一个新的声明类型字符串，在该字符串中，当前字符串中出现的所有指定字符串已替换为另一个指定的字符串。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 包含该字符串的声明类型。 |
| InputParameter | oldValue | 字符串 | 要搜索的字符串。 |
| InputParameter | newValue | 字符串 | 用于替换出现的所有 `oldValue` 的字符串 |
| OutputClaim | outputClaim | boolean | 等效于当前字符串的字符串，只不过 oldValue 的所有实例已替换为 newValue。 如果在当前实例中找不到 oldValue，该方法将按原样返回当前实例。 |

例如，通过删除 `-` 字符来规范化某个电话号码


```xml
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**: "+164-411-452-054"
- 输入参数：
    - **oldValue**: "-"
    - **newValue**：""
- 输出声明：
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin

在每个元素或成员之间使用指定的分隔符，串联指定字符串集合声明类型的元素。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | 包含要串联的字符串的集合。 |
| InputParameter | delimiter | 字符串 | 用作分隔符的字符串，例如逗号 `,`。 |
| OutputClaim | outputClaim | 字符串 | 由 `inputClaim` 字符串集合的成员组成的字符串，以 `delimiter` 输入参数分隔。 |

以下示例提取用户角色的字符串集合，并将其转换为逗号分隔符字符串。 可以使用此方法在 Azure AD 用户帐户中存储字符串集合。 以后在从目录读取帐户时，可以使用 `StringSplit` 将逗号分隔符字符串转换回字符串集合。

```xml
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **inputClaim**: [ "Admin", "Author", "Reader" ]
- 输入参数：
  - **delimiter**: ","
- 输出声明：
  - **outputClaim**："Admin,Author,Reader"


## <a name="stringsplit"></a>StringSplit

返回一个字符串数组，其中包含此实例中由指定字符串的元素分隔的子字符串。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 包含要拆分的子字符串的字符串声明类型。 |
| InputParameter | delimiter | 字符串 | 用作分隔符的字符串，例如逗号 `,`。 |
| OutputClaim | outputClaim | stringCollection | 一个字符串集合，其元素包含此字符串中由 `delimiter` 输入参数分隔的子字符串。 |

以下示例提取用户角色的逗号分隔符字符串，并将其转换为字符串集合。

```xml
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **inputClaim**："Admin,Author,Reader"
- 输入参数：
  - **delimiter**: ","
- 输出声明：
  - outputClaim：[ "Admin", "Author", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>字符串声明转换表达式
Azure AD B2C 自定义策略中的声明转换表达式提供了有关租户 ID 和技术配置文件 ID 的上下文信息。

  | 表达式 | 说明 | 示例 |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | 技术配置文件 ID 名称。 | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | 信赖方策略的租户 ID。 | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | 信任框架的租户 ID。 | your-tenant.onmicrosoft.com |
