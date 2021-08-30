---
title: 自定义策略的整数声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的 Identity Experience Framework (IEF) 架构的整数声明转换示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7583698bd78ecb3a56d00b1af845f6d5f94c78c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724899"
---
# <a name="integer-claims-transformations"></a>整数声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C (Azure AD B2C) 中使用标识体验框架架构的整数声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="adjustnumber"></a>AdjustNumber

增加或减少数值声明并返回新声明。

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | int | 声明类型，其中包含要增加或减少的数量。 如果 `inputClaim` 声明值为 NULL，则使用默认值 0。 |
| InputParameter | 运算符 | string | 可能的值为 `INCREMENT`（默认）或 `DECREMENT`。|
| OutputClaim | outputClaim | int | 调用此声明转换后生成的声明类型。 |

使用此声明转换来增加或减少数值声明值。 

```xml
<ClaimsTransformation Id="UpdateSteps" TransformationMethod="AdjustNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="steps" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="INCREMENT" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="steps" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>示例 1

- 输入声明：
    - inputClaim：1
- 输入参数：
    - Operator：INCREMENT
- 输出声明：
    - outputClaim：2

### <a name="example-2"></a>示例 2

- 输入声明：
    - inputClaim：NULL
- 输入参数：
    - Operator：INCREMENT
- 输出声明：
    - outputClaim：1


## <a name="assertnumber"></a>AssertNumber

确定数值声明大于、小于、等于还是不等于某一数字。 

| 项目 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | int | 第一个数值声明，用于比较它大于、小于、等于还是不等于第二个数字。 Null 值会引发异常。 |
| InputParameter | CompareToValue | int | 第二个数字，用于比较它大于、小于、等于还是不等于第一个数字。 |
| InputParameter | 运算符 | string | 可能的值：`LESSTHAN`、`GREATERTHAN`、`GREATERTHANOREQUAL`、`LESSTHANOREQUAL`、`EQUAL`、`NOTEQUAL`。 |
| InputParameter | throwError | boolean | 指定比较结果为 `true` 时，此断言是否应引发错误。 可能的值为 `true`（默认）或 `false`。 <br />&nbsp;<br />如果设置为 `true`（断言模式）并且比较结果为 `true`，则会引发异常。 如果设置为 `false`（计算模式），结果为一个新的布尔声明类型，值为 `true` 或 `false`。| 
| OutputClaim | outputClaim | boolean | 如果 `ThrowError` 设置为 `false`，根据比较结果，此输出声明将包含 `true` 或 `false`。 |

### <a name="assertion-mode"></a>断言模式

当 `throwError` 输入参数为 `true`（默认）时，AssertNumber 声明转换始终从[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)调用。 

AssertNumberError 自断言技术配置文件元数据控制技术配置文件向用户呈现的错误消息。 可以将错误消息[本地化](localization-string-ids.md#claims-transformations-error-messages)。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="AssertNumberError">You've reached the maximum logon attempts</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

有关如何在断言模式下调用声明转换的详细信息，请参阅 [AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal)、[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) 和 [AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) 声明转换。

### <a name="assertion-mode-example"></a>断言模式示例

以下示例断言尝试次数超过五次。 声明转换根据比较结果引发错误。 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```

- 输入声明：
    - inputClaim：10
- 输入参数：
    - Operator：GREATERTHAN
    - CompareToValue：5
    - throwError：true
- 结果：引发错误

### <a name="evaluation-mode-example"></a>计算模式示例

以下示例计算尝试次数是否超过五次。 根据比较结果，输出声明包含一个布尔值。 声明转换不会引发错误。 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="attemptsCountExceeded" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- 输入声明：
    - inputClaim：10
- 输入参数：
    - Operator：GREATERTHAN
    - CompareToValue：5
    - throwError：false
- 输出声明：
    - **outputClaim**: true


## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

将 long 数据类型转换为字符串数据类型。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 要转换为字符串的声明类型。 |
| OutputClaim | outputClaim | string | 调用此声明转换后生成的声明类型。 |

在此示例中，值类型为 long 的 `numericUserId` 声明将转换为值类型为字符串的 `UserId` 声明。

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**: 12334 (long)
- 输出声明：
    - **outputClaim**: "12334" (string)

