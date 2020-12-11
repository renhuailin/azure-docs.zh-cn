---
title: 添加用户属性和自定义用户输入
titleSuffix: Azure AD B2C
description: 了解如何自定义用户输入，并将用户属性添加到 Azure Active Directory B2C 中的注册或登录旅程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 698864a4dc1081cb8cad9036ff1cfc737a17473c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111140"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>添加用户属性和自定义 Azure Active Directory B2C 中的用户输入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

本文介绍如何在注册旅程 Azure Active Directory B2C (Azure AD B2C) 中收集新属性。 你将获得用户的 "城市"，将其配置为下拉箭头，并定义是否需要提供此项。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>为用户流添加用户属性

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择 " **用户属性** "，然后选择 "用户" 属性 (例如 "City" ) 。 
1. 选择“保存”。

## <a name="provide-optional-claims-to-your-app"></a>向应用提供可选声明

应用程序声明是返回给应用程序的值。 更新用户流以包含所需的声明。

1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“应用程序声明”  。
1. 选择要发送回应用程序 (的属性，例如 "City" ) 。
1. 选择“保存”。
 
## <a name="configure-user-attributes-input-type"></a>配置用户特性输入类型

1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“页面布局”。
1. 选择“本地帐户注册页”。
1. 在 " **用户属性**" 下，选择 " **City**"。
    1. 在 "**用户输入类型**" 下拉 DropdownSingleSelect 中，选择 " "。
    1. 在 **可选** 下拉箭头中，选择 " **否**"。
1. 选择“保存”。 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>使用本地化集合提供值的列表

若要为 city 属性提供一组值的列表，请执行以下操作： 

1. [对用户流启用语言自定义](language-customization.md#support-requested-languages-for-ui_locales)
1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 在用户流的“语言”页面，选择想要自定义的语言  。
1. 在 " **页面级别-资源文件**" 下，选择 " **本地帐户注册页**"。
1. 选择“下载默认值”（如果以前已编辑这种语言，则选择“下载重写”）。  
1. 创建 `LocalizedCollections` 属性。

`LocalizedCollections`是和对的数组 `Name` `Value` 。 项目的顺序将是它们显示的顺序。 

* `ElementId` 是此 `LocalizedCollections` 属性响应的用户属性。
* `Name` 是向用户显示的值。
* `Value` 是选择此选项时要在声明中返回的内容。

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>上传更改

1. 完成对 JSON 文件的更改后，返回到 B2C 租户。
1. 选择 " **用户流** "，然后选择策略 (例如，"B2C_1_SignupSignin" ) 将其打开。
1. 选择“语言”  。
1. 选择要翻译成的语言。
1. 选择 **本地帐户注册页**。
1. 选择文件夹图标，选择要上传的 JSON 文件。 更改将自动保存到用户流。

## <a name="test-your-user-flow"></a>测试用户流

1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> 此示例使用内置声明 "city"。 相反，您可以选择一个受支持的 [Azure AD B2C 内置属性](user-profile-attributes.md) 或自定义属性。 若要使用自定义属性，请 [在策略中启用自定义属性](custom-policy-custom-attributes.md)。 若要使用不同的内置或自定义属性，请将 "city" 替换为所选的属性，例如内置属性 *jobTitle* 或 *extension_loyaltyId* 之类的自定义属性。  

你可以使用注册或登录用户旅程收集用户的初始数据。 稍后可以通过配置文件编辑用户旅程收集其他声明。 Azure AD B2C 以交互方式直接从用户那里收集信息，标识体验框架将使用其 [自断言技术配置文件](self-asserted-technical-profile.md)。 在此示例中，你：

1. 定义 "city" 声明。 
1. 要求用户提供其城市。
1. 将城市保存到 Azure AD B2C 目录中的用户配置文件。
1. 在每次登录时，请从 Azure AD B2C 目录中读取 city 声明。
1. 登录或注册后，将城市返还给信赖方应用程序。  

## <a name="define-a-claim"></a>定义声明

声明在 Azure AD B2C 策略执行过程中提供数据的临时存储。 [声明架构](claimsschema.md)是发出声明的位置。 以下元素用于定义声明：

- **DisplayName** - 一个字符串，用于定义面向用户的标签。
- [DataType](claimsschema.md#datatype) -声明的类型。
- **UserHelpText** - 帮助用户了解需要提供哪些信息。
- [UserInputType](claimsschema.md#userinputtype) -输入控件（如文本框、广播选择、下拉列表或多个选项）的类型。

打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将 city 声明添加到 **ClaimsSchema** 元素。  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>向用户界面添加声明

以下技术配置文件 [自断言](self-asserted-technical-profile.md)，在用户需要提供输入时被调用：

- **LocalAccountSignUpWithLogonEmail** -本地帐户注册流。
- **SelfAsserted-** 联合帐户首次用户登录。
- **SelfAsserted-self-asserted-profileupdate** -编辑配置文件流。

若要在注册期间收集城市声明，则必须将其作为输出声明添加到 `LocalAccountSignUpWithLogonEmail` 技术配置文件中。 覆盖扩展文件中的此技术配置文件。 指定输出声明的完整列表，以控制声明在屏幕上的显示顺序。 找到 **ClaimsProviders** 元素。 添加新的 ClaimsProviders，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

若要在使用联合帐户初次登录后收集 city 声明，必须将其作为输出声明添加到 `SelfAsserted-Social` 技术配置文件。 为了使本地和联合帐户用户以后能够编辑其配置文件数据，请将输出声明添加到 `SelfAsserted-ProfileUpdate` 技术配置文件中。 覆盖扩展文件中的这些技术配置文件。 指定输出声明的完整列表，以控制声明在屏幕上的显示顺序。 找到 **ClaimsProviders** 元素。 添加新的 ClaimsProviders，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>读取和写入声明

以下技术配置文件是 [Active Directory 技术配置文件，这些配置文件](active-directory-technical-profile.md)将数据读写到 Azure Active Directory 中。  
使用 `PersistedClaims` 将数据写入用户配置文件，并 `OutputClaims` 从各自 Active Directory 技术配置文件中的用户配置文件中读取数据。

覆盖扩展文件中的这些技术配置文件。 找到 **ClaimsProviders** 元素。  添加新的 ClaimsProviders，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>在令牌中包括声明 

若要将城市声明返回给信赖方应用程序，请将输出声明添加到该 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 文件。 用户成功旅程后，会将输出声明添加到令牌中，并将其发送到应用程序。 修改 "信赖方" 部分中的 "技术配置文件" 元素，以将城市添加为输出声明。
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
4. 选择“标识体验框架”。
5. 选择“上传自定义策略”，然后上传已更改的两个策略文件。
2. 选择已上传的注册或登录策略，并单击“立即运行”按钮。
3. 现在，应该可以使用电子邮件地址注册。

::: zone-end

注册屏幕应类似于以下屏幕截图：

![修改后的注册选项屏幕截图](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

发送回应用程序的令牌包含 `city` 声明。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>后续步骤

- 详细了解 IEF 参考中的 [ClaimsSchema](claimsschema.md) 元素。
- 了解如何 [使用自定义配置文件编辑策略中的自定义属性](custom-policy-custom-attributes.md)。

::: zone-end