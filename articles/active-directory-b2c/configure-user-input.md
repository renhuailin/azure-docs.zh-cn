---
title: 添加用户属性并自定义用户输入
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中自定义用户输入，并将用户属性添加到注册或登录旅程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f2b7eade30ad675b9c64fbe8d21ce5c7f6d63e43
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558402"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中添加用户属性和自定义用户输入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

在本文中，你将在 Azure Active Directory B2C (Azure AD B2C) 的注册过程中收集一个新属性。 你将获得用户的“city”，将其配置为下拉列表，并定义是否需要提供此项。

> [!IMPORTANT]
> 此示例使用内置声明“city”。 相反，你可以选择一个受支持的 [Azure AD B2C 内置属性](user-profile-attributes.md)或自定义属性。 若要使用自定义属性，请[启用自定义属性](user-flow-custom-attributes.md)。 若要使用不同的内置或自定义属性，请将“city”替换为所选的属性，例如内置属性“jobTitle”或“extension_loyaltyId”之类的自定义属性。  

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>为用户流添加用户属性

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“用户属性”，然后选择用户属性（例如，“City”）。 
1. 选择“保存”。

## <a name="provide-optional-claims-to-your-app"></a>向应用提供可选声明

应用程序声明是返回给应用程序的值。 更新用户流以包含所需的声明。

1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“应用程序声明”  。
1. 选择要发送回应用程序的属性（例如“City”）。
1. 选择“保存”。
 
## <a name="configure-user-attributes-input-type"></a>配置用户属性输入类型

1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“页面布局”。
1. 选择“本地帐户注册页”。
1. 在“用户属性”下，选择“City”。
    1. 在“用户输入类型”下拉列表中，选择“DropdownSingleSelect”。 可选：使用“上移”/“下移”按钮可排列注册页中的文本顺序。
    1. 在“可选”下拉列表中，选择“否”。
1. 选择“保存”。 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>使用本地化集合提供值列表

若要为 city 属性提供一组值列表，请执行以下操作： 

1. [在用户流上启用语言自定义](language-customization.md#support-requested-languages-for-ui_locales)
1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 在用户流的“语言”页面，选择想要自定义的语言  。
1. 在“页面-级别-资源文件”下，选择“本地帐户注册页”。
1. 选择“下载默认值”（如果以前已编辑这种语言，则选择“下载重写”）。  
1. 创建 `LocalizedCollections` 属性。

`LocalizedCollections` 是 `Name` 和 `Value` 对的数组。 项目的顺序将是它们显示的顺序。 

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
      "Override": true,
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
1. 选择“用户流”，并选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“语言”  。
1. 选择要翻译成的语言。
1. 选择“本地帐户注册页”。
1. 选择文件夹图标，选择要上传的 JSON 文件。 更改将自动保存到用户流。

## <a name="test-your-user-flow"></a>测试用户流

1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>概述

可以通过注册或登录用户旅程从用户那里收集初始数据。 稍后可以通过配置文件编辑用户旅程收集其他声明。 每当 Azure AD B2C 以交互方式直接从用户收集信息时，都会使用[自断言技术配置文件](self-asserted-technical-profile.md)。 在此示例中，你将执行以下操作：

1. 定义“city”声明。 
1. 请求用户输入其所在城市。
1. 将城市保存到 Azure AD B2C 目录中的用户配置文件。
1. 每次登录时，请从 Azure AD B2C 目录中读取 city 声明。
1. 登录或注册后，将 city 返回给信赖方应用。  

## <a name="define-a-claim"></a>定义声明

声明可在 Azure AD B2C 策略执行过程中提供数据的临时存储。 [声明架构](claimsschema.md)是发出声明的位置。 以下元素用于定义声明：

- **DisplayName** - 一个字符串，用于定义面向用户的标签。
- [DataType](claimsschema.md#datatype) - 声明类型。
- **UserHelpText** - 帮助用户了解需要提供哪些信息。
- [UserInputType](claimsschema.md#userinputtype) - 输入控件类型（如文本框、单选按钮选择、下拉列表或多重选择）。

打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将 city 声明添加到 ClaimsSchema 元素。  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Berlin" Value="berlin" />
        <Enumeration Text="London" Value="bondon" />
        <Enumeration Text="Seattle" Value="seattle" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

在 `Enumeration` 元素上包含 [SelectByDefault](claimsschema.md#enumeration) 属性，使其在页面首次加载时默认选中。 例如，若要预先选择 London 项，请更改 `Enumeration` 元素，如以下示例所示：

```xml
<Restriction>
  <Enumeration Text="Berlin" Value="berlin" />
  <Enumeration Text="London" Value="bondon" SelectByDefault="true" />
  <Enumeration Text="Seattle" Value="seattle" />
</Restriction>
```

## <a name="add-a-claim-to-the-user-interface"></a>向用户界面添加声明

以下技术配置文件为[自断言](self-asserted-technical-profile.md)，在用户需要提供输入时调用：

- LocalAccountSignUpWithLogonEmail - 本地帐户注册流。
- SelfAsserted-Social - 联合帐户首次用户登录。
- SelfAsserted-ProfileUpdate - 编辑配置文件流。

若要在注册期间收集 city 声明，则必须将其作为输出声明添加到 `LocalAccountSignUpWithLogonEmail` 技术配置文件。 在扩展文件中覆盖此技术配置文件。 指定输出声明的整个列表，以控制声明在屏幕上的显示顺序。 找到 **ClaimsProviders** 元素。 添加一个新的 ClaimsProviders，如下所示：

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

若要在使用联合帐户初次登录后收集 city 声明，必须将其作为输出声明添加到 `SelfAsserted-Social` 技术配置文件。 为了使本地和联合帐户用户以后能够编辑其配置文件数据，请将输入和输出声明添加到 `SelfAsserted-ProfileUpdate` 技术配置文件。 在扩展文件中覆盖这些技术配置文件。 指定输出声明的整个列表，以控制声明在屏幕上的显示顺序。 找到 **ClaimsProviders** 元素。 添加一个新的 ClaimsProviders，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
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

以下技术配置文件是 [Active Directory 技术配置文件](active-directory-technical-profile.md)，它们会在 Azure Active Directory 中读写数据。  
使用 `PersistedClaims` 将数据写入用户配置文件，使用 `OutputClaims` 从相应的 Active Directory 技术配置文件的用户配置文件中读取数据。

在扩展文件中覆盖这些技术配置文件。 找到 **ClaimsProviders** 元素。  添加一个新的 ClaimsProviders，如下所示：

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
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>在令牌中包括声明 

若要将 city 声明返回到信赖方应用，请将输出声明添加到 <em>`SocialAndLocalAccounts/``SignUpOrSignIn.xml`</em> 文件。 输出声明将在用户旅程成功后添加到令牌，并发送到应用程序。 修改信赖方部分中的技术配置文件元素，以将 city 添加为输出声明。
 
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

## <a name="upload-and-test-your-updated-custom-policy"></a>上传并测试已更新的自定义策略

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 搜索并选择“Azure AD B2C”。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择“上传自定义策略”。
1. 上传之前更改的策略文件。

### <a name="test-the-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 从注册或登录页面，选择“立即注册”进行注册。 完成输入用户信息（包括城市名称），然后单击“创建”。 应看到返回的令牌的内容。

::: zone-end

注册屏幕应类似于以下屏幕快照：

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
  "city": "Berlin"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="optional-localize-the-ui"></a>[可选] UI 本地化

Azure AD B2C 允许你根据不同的语言调整策略。 有关详细信息，请参阅[了解如何自定义语言体验](language-customization.md)。 若要本地化注册页面，请参阅[设置支持语言列表](language-customization.md#set-up-the-list-of-supported-languages)和[提供特定于语言的标签](language-customization.md#provide-language-specific-labels)。

> [!NOTE]
> 将 `LocalizedCollection` 与特定于语言的标签一起使用时，可以从[声明定义](#define-a-claim)中删除 `Restriction` 集合。

以下示例演示如何提供适用于英语和西班牙语的城市列表。 这两个元素都设置了声明 city 的 `Restriction` 集合，以及适用于英语和西班牙语的项列表。 [SelectByDefault](claimsschema.md#enumeration) 在页面首次加载时默认选中一个项目。
   
```xml
<!-- 
<BuildingBlocks>-->
  <Localization Enabled="true">
    <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
    </SupportedLanguages>
    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="city" TargetCollection="Restriction">
          <Item Text="Berlin" Value="Berlin"></Item>
          <Item Text="London" Value="London" SelectByDefault="true"></Item>
          <Item Text="Seattle" Value="Seattle"></Item>
        </LocalizedCollection>
      </LocalizedCollections>
    </LocalizedResources>
    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="city" TargetCollection="Restriction">
          <Item Text="Berlina" Value="Berlin"></Item>
          <Item Text="Londres" Value="London" SelectByDefault="true"></Item>
          <Item Text="Seattle" Value="Seattle"></Item>
        </LocalizedCollection>
      </LocalizedCollections>
    </LocalizedResources>
  </Localization>
<!-- 
</BuildingBlocks>-->
```

添加本地化元素后，[使用本地化编辑内容定义](language-customization.md#edit-the-content-definition-with-the-localization)。 在以下示例中，英语 (en) 和西班牙语 (es) 自定义本地化资源已添加到注册页面：
   
```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
   <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
   </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks>-->
```

## <a name="next-steps"></a>后续步骤

- 在 IEF 参考中详细了解 [ClaimsSchema](claimsschema.md) 元素。
- 了解如何[在 Azure AD B2C 中使用自定义属性](user-flow-custom-attributes.md)。

::: zone-end
