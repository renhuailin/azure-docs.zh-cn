---
title: 配置令牌 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中配置令牌生存期和兼容性设置。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f5586b43143763ebf36adb15d96fdb2a91b5f5c
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443468"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置令牌

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

在本文汇总，你将了解如何在 Azure Active Directory B2C (Azure AD B2C) 中配置[令牌的生存期和兼容性](tokens-overview.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>令牌生存期行为

可以配置令牌生存期，包括：

- 访问令牌和 ID 令牌生存期（分钟）- OAuth 2.0 持有者令牌和 ID 令牌的生存期。 默认值为 60 分钟（1 小时）。 最小值为 5 分钟（含）。 最大值为 1,440 分钟（24 小时）（含）。
- 刷新令牌生存期（天）- 在应用程序已获取 `offline_access` 范围的情况下，可以使用某个刷新令牌获取新访问令牌之前所要经过的最长时间段。 默认值为 14 天。 最小值为 1 天（含）。 最大值为 90 天（含）。
- 刷新令牌滑动窗口生存期 - 刷新令牌滑动窗口类型。 `Bounded` 指示按照“生存期长度（天）”中指定的时间扩展刷新令牌。 `No expiry` 指示刷新令牌滑动窗口生存期永不过期。
- 生存期长度（天）- 此时间段过后，会强制用户重新进行身份验证，不考虑该应用程序获取的最近刷新令牌的有效期。 此值必须大于或等于“刷新令牌生存期”的值。

下图显示了刷新令牌滑动窗口生存期行为。

![刷新令牌生存期](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >使用带有 PKCE 的授权代码流的单页面应用程序的刷新令牌生存期始终为 24 小时，而移动应用、桌面应用和 Web 应用不会遇到此限制。 [详细了解浏览器中的刷新令牌的安全影响](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)。


## <a name="configure-token-lifetime"></a>配置令牌生存期

::: zone pivot="b2c-user-flow"

配置用户流令牌生存期：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“用户流(策略)”。
1. 打开之前创建的用户流。
1. 选择“属性”。
1. 在“令牌生存期”下，调整属性以满足应用程序的需要。
1. 单击“保存”  。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要更改令牌兼容性的设置，需要在扩展或想要影响的策略的信赖方文件中设置[令牌颁发者](jwt-issuer-technical-profile.md)技术配置文件元数据。 令牌颁发者技术配置文件类似于以下示例：

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

上一示例中设置了以下值：

- token_lifetime_secs - 访问令牌生存期（秒）。 默认值为 3,600（1 小时）。 最小值为 300（5 分钟）。 最大值为 86,400（24 小时）。 
- id_token_lifetime_secs - ID 令牌生存期（秒）。 默认值为 3,600（1 小时）。 最小值为 300（5 分钟）。 最大值为 86,400（24 小时）。 
- refresh_token_lifetime_secs - 刷新令牌生存期（秒）。 默认值为 120,9600（14 天）。 最小值为 86,400（24 小时）。 最大值为 7,776,000（90 天）。 
- rolling_refresh_token_lifetime_secs - 刷新令牌滑动窗口生存期（秒）。 默认值为 7,776,000（90 天）。 最小值为 86,400（24 小时）。 最大值为 31,536,000（365 天）。 如果不想强制实施滑动窗口生存期，请将 `allow_infinite_rolling_refresh_token` 的值设置为 `true`。 
- allow_infinite_rolling_refresh_token - 刷新令牌滑动窗口生存期永不过期。 

::: zone-end


## <a name="token-compatibility-settings"></a>令牌兼容性设置

你可以配置令牌兼容性，包括：

- 颁发者 (iss) 声明 - 访问令牌和 ID 令牌颁发者格式。
- 使用者 (sub) 声明 - 标识令牌断言信息的主体，例如应用程序的用户。 此值固定不变，无法重新分配或重复使用。 可以使用它来安全地执行授权检查，例如，当使用令牌访问资源时。 默认情况下，将使用目录中用户的对象 ID 填充使用者声明。
- 表示用户流的声明 - 此声明标识已执行的用户流。 可能的值为 `tfp`（默认）或 `acr`。

::: zone pivot="b2c-user-flow"

配置用户流兼容性设置：

1. 选择“用户流(策略)”。
1. 打开之前创建的用户流。
1. 选择“属性”。
1. 在“令牌兼容性设置”下，调整属性以满足应用程序的需要。
1. 单击“保存”  。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要更改令牌兼容性的设置，需要在扩展或想要影响的策略的信赖方文件中设置[令牌颁发者](jwt-issuer-technical-profile.md)技术配置文件元数据。 令牌颁发者技术配置文件类似于以下示例：

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **颁发者 (iss) 声明** - 颁发者 (iss) 声明是通过 **IssuanceClaimPattern** 元数据项设置的。 适用的值为 `AuthorityAndTenantGuid` 和 `AuthorityWithTfp`。
- **设置声明表示策略 ID** - 用于设置此值的选项为 `TFP`（信任框架策略）和 `ACR`（身份验证上下文引用）。 `TFP` 是建议使用的值。 将 **AuthenticationContextReferenceClaimPattern** 设置为值 `None`。

    在 ClaimsSchema 元素中，添加此元素：

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    在 OutputClaims 元素中，添加此元素：

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    对于 ACR，请删除 **AuthenticationContextReferenceClaimPattern** 项。

- **使用者 (sub) 声明** - 此选项默认为 ObjectID，如果要将此设置切换为 `Not Supported`，请替换以下行：

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    替换为以下代码行：

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>向应用提供可选声明

应用程序声明是返回给应用程序的值。 更新用户流以包含所需的声明。

::: zone pivot="b2c-user-flow"

1. 选择“用户流(策略)”。
1. 打开之前创建的用户流。
1. 选择“应用程序声明”  。
1. 选择要发送回应用程序的声明和属性。
1. 单击“ **保存**”。

::: zone-end

::: zone pivot="b2c-custom-policy"

[信赖方策略技术配置文件](relyingparty.md#technicalprofile)输出声明是返回到应用程序的值。 添加输出声明时，会在用户旅程成功后向令牌发出声明，然后声明就会被发送到应用程序。 修改信赖方部分中的技术配置文件元素，以将所需声明添加为输出声明。

1. 打开自定义策略文件。 例如，SignUpOrSignin.xml。
1. 查找 OutputClaims 元素。 添加要包含在令牌中的 OutputClaim。 
1. 设置输出声明属性。 

下面的示例将添加 `accountBalance` 声明。 accountBalance 声明将作为余额发送到应用程序。 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

OutputClaim  元素包含以下属性：

- **ClaimTypeReferenceId** - 已在策略文件或父策略文件的 [ClaimsSchema](claimsschema.md) 节中定义的声明类型的标识符。
- **PartnerClaimType** - 允许你更改令牌中的声明的名称。 
- **DefaultValue** - 默认值。 还可以将默认值设置为[声明解析程序](claim-resolver-overview.md)，如租户 ID。
- **AlwaysUseDefaultValue** - 强制使用默认值。

::: zone-end

## <a name="authorization-code-lifetime"></a>授权代码生存期

使用 [OAuth 2.0 授权代码流](authorization-code-flow.md)时，应用可以使用授权代码请求目标资源的访问令牌。 授权代码的生存期很短，约 10 分钟后即过期。 授权代码的生存期是不能进行配置的。 请确保应用程序在 10 分钟内兑换授权代码。 

## <a name="next-steps"></a>后续步骤

详细了解如何[请求访问令牌](access-tokens.md)。
