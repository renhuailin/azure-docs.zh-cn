---
title: Azure Active Directory B2C 中的语言自定义
description: 了解如何在 Azure Active Directory B2C 中自定义用户流中的语言体验。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0954f438f21229b16800847f5d248909a61df051
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573947"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的语言自定义

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

用户流可以使用 Azure Active Directory B2C (Azure AD B2C) 中的语言自定义来适应不同的语言，从而满足客户需求。 Microsoft 提供 [36 种语言](#supported-languages)的翻译，但你也可以为任何语言提供自己的翻译。 即使体验是针对一种语言提供的，也可以自定义页面上的任何文本。

## <a name="how-language-customization-works"></a>语言自定义的工作原理

通过语言自定义可选择用户流可用于的语言。 启用该功能后，可以从应用程序提供查询字符串参数 `ui_locales`。 调用 Azure AD B2C 时，我们会根据指定的区域设置翻译页面。 使用此配置类型可以完全控制用户流中的语言，并忽略客户浏览器的语言设置。

可能不需要对客户看到的语言拥有这种控制度。 如果未提供 `ui_locales` 参数，客户的体验由其浏览器设置确定。 仍可以控制要将用户流翻译成哪种语言，只需将该语言添加为支持的语言即可。 如果客户浏览器设置为显示你不希望支持的语言，则将改为显示你在受支持区域性中选为默认的语言。

* **ui-locales 指定的语言**：启用语言自定义后，用户流将翻译成此处指定的语言。
* **浏览器请求的语言**：如果未指定 `ui_locales` 参数，用户流将翻译成浏览器请求的语言（*如果该语言受支持*）。
* **策略默认语言**：如果浏览器未指定语言，或者指定了不支持的语言，用户流将翻译成用户流默认语言。

> [!NOTE]
> 如果使用自定义用户属性，需要提供自己的翻译。 有关详细信息，请参阅[自定义字符串](#customize-your-strings)。

观看此视频，了解如何使用 Azure AD B2C 来本地化或自定义语言。

>[!Video https://www.youtube.com/embed/yqrX5_tA7Ms]

::: zone pivot="b2c-custom-policy"

本地化需要三个步骤： 

1. 设置受支持语言的显式列表
1. 提供特定于语言的字符串和集合
1. 编辑页面的[内容定义](contentdefinitions.md)。 

::: zone-end 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>支持 ui_locales 请求的语言

在语言自定义正式版发布之前创建的策略将需要先启用此功能。 之后创建的策略和用户流默认情况下已启用语言自定义。

在用户流中启用语言自定义后，可以通过添加 `ui_locales` 参数来控制用户流的语言。

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击想要启用翻译的用户流。
1. 选择“语言”  。
1. 选择“启用语言自定义”。 

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>选择要在用户流中启用的语言

为用户流启用一组语言，以便在无 `ui_locales` 参数的浏览器提出请求时翻译成这些语言。

1. 确保已根据前面的说明为用户流启用语言自定义。
1. 在用户流的“语言”页面，选择想要支持的语言  。
1. 在属性窗格中，将“已启用”更改为“是”。  
1. 选择属性窗格顶部的“保存”  。

>[!NOTE]
>如果未提供 `ui_locales` 参数，则仅当客户的浏览器语言已启用时，才将页面翻译成此语言
>

## <a name="customize-your-strings"></a>自定义字符串

使用语言自定义可以自定义用户流中的任何字符串。

1. 确保已根据前面的说明为用户流启用语言自定义。
1. 在用户流的“语言”页面，选择想要自定义的语言  。
1. 在“页面级别资源文件”下，选择想要编辑的页面  。
1. 选择“下载默认值”（如果以前已编辑这种语言，则选择“下载重写”）。  

执行这些步骤可以创建用于开始编辑字符串的 JSON 文件。

### <a name="change-any-string-on-the-page"></a>更改页面上的任何字符串

1. 在 JSON 编辑器中打开根据前面的说明下载的 JSON 文件。
1. 找到想要更改的元素。 可以查找所需字符串的 `StringId`，或者查找想要更改的 `Value` 属性。
1. 使用想要显示的内容更新 `Value` 属性。
1. 对于每个要更改的字符串，将 `Override` 更改为 `true`。
1. 保存文件并上传更改。 （可在下载 JSON 文件的同一位置找到上传控件。）

> [!IMPORTANT]
> 如果需要重写字符串，请确保将 `Override` 值设置为 `true`。 如果未更改该值，将忽略该条目。

### <a name="change-extension-attributes"></a>更改扩展属性

若要更改自定义用户属性的字符串或者想要在 JSON 中添加一个字符串，该字符串需采用以下格式：

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

将 `<ExtensionAttribute>` 替换为自定义用户属性的名称。

将 `<ExtensionAttributeValue>` 替换为要显示的新字符串。

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>使用 LocalizedCollections 提供值列表

若要为响应提供一组值列表，需要创建 `LocalizedCollections` 属性。 `LocalizedCollections` 是 `Name` 和 `Value` 对的数组。 项目的顺序将是它们显示的顺序。 若要添加 `LocalizedCollections`，请使用以下格式：

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` 是此 `LocalizedCollections` 属性响应的用户属性。
* `Name` 是向用户显示的值。
* `Value` 是选择此选项时要在声明中返回的内容。

### <a name="upload-your-changes"></a>上传更改

1. 完成对 JSON 文件的更改后，返回到 B2C 租户。
1. 选择“用户流”，单击想要启用翻译的用户流  。
1. 选择“语言”  。
1. 选择要翻译成的语言。
1. 选择想要提供翻译的页面。
1. 选择文件夹图标，选择要上传的 JSON 文件。

更改将自动保存到用户流。

## <a name="customize-the-page-ui-by-using-language-customization"></a>使用语言自定义来自定义页面 UI

可通过两种方法本地化 [HTML 内容](customize-ui-with-html.md)。 一种方法是启用[语言自定义](language-customization.md)。 启用此功能可让 Azure AD B2C 将 OpenID Connect 参数 `ui-locales` 转发到终结点。 内容服务器可使用此参数提供特定语言的自定义 HTML 页面。

或者，可以基于所用的区域设置从不同位置拉取内容。 在已启用 CORS 的终结点中，可以设置文件夹结构以托管特定语言的内容。 如果使用通配符值 `{Culture:RFC5646}`，则会调用正确的语言。 例如，假设自定义页 URI 如下：

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

可以在 `fr` 中加载页面。 当页面拉取 HTML 和 CSS 内容时，将从以下项拉取：

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>添加自定义语言

还可以添加 Microsoft 目前未为其提供翻译的语言。 需要为用户流中的所有字符串提供翻译。 语言和区域设置代码仅限于 ISO 639-1 标准中的代码。 区域设置代码格式应为 "ISO_639-1_code"-"CountryCode"，例如 `en-GB`。 有关详细信息，请参阅 [区域设置 ID 格式](/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a)。

1. 在 Azure AD B2C 租户中，选择“用户流”  。
2. 单击想要添加自定义语言的用户流，然后单击“语言”  。
3. 从页面顶部选择“添加自定义语言”  。
4. 在打开的上下文窗格中，通过输入有效的区域设置代码确定要为其提供翻译的语言。
5. 对于每个页，可以下载一组英语重写，并处理翻译。
6. 完成 JSON 文件后，可为每个页面上传这些文件。
7. 选择“启用”，用户流即可为用户显示此语言  。
8. 保存语言。

>[!IMPORTANT]
>在保存之前，你需要启用自定义语言或上传替代语言。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>设置支持的语言的列表

打开策略的扩展文件。 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 添加包含支持的语言的 `Localization` 元素：英语（默认）和西班牙语。  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>提供特定于语言的标签

`Localization` 元素的 [LocalizedResources](localization.md#localizedresources) 包含本地化字符串的列表。 本地化资源元素具有用于唯一标识本地化资源的标识符。 此标识符稍后会在[内容定义](contentdefinitions.md)元素中使用。

你将为内容定义和要支持的任何语言配置本地化资源元素。 若要自定义适用于英语和西班牙语的统一注册或登录页面，请在 `</SupportedLanguages>` 元素结束后添加以下 `LocalizedResources` 元素。

> [!NOTE]
> 在下面的示例中，我们在每行的开头都添加了井字符号 `#`，以便你可以在屏幕上轻松找到本地化的标签。

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>通过本地化编辑内容定义

粘贴作为 BuildingBlocks 元素的子元素复制的 ContentDefinitions 元素的全部内容。

在以下示例中，英语 (en) 和西班牙语 (es) 自定义字符串已添加到注册或登录页面，并已添加到本地帐户注册页面。 每个 **LocalizedResourcesReference** 的 **LocalizedResourcesReferenceId** 与其区域设置相同，但可将任何字符串用作标识符。 对于每个语言和页面组合，请指向前面创建的相应 **LocalizedResources**。

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="upload-and-test-your-updated-custom-policy"></a>上传并测试已更新的自定义策略

### <a name="upload-the-custom-policy"></a>上传自定义策略

1. 保存扩展文件。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 搜索并选择“Azure AD B2C”。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择“上传自定义策略”。
1. 上传以前已更改的扩展文件。

### <a name="test-the-custom-policy-by-using-run-now"></a>使用“立即运行”测试自定义策略

1. 选择已上传的策略，然后选择“立即运行”。
1. 你应该能够看到已本地化的注册或登录页面。
1. 单击注册链接，你应该能够看到已本地化的注册页面。
1. 将浏览器默认语言切换为西班牙语。 还可以将查询字符串参数 `ui_locales` 添加到授权请求。 例如： 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>其他信息

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>页面 UI 自定义标签保留为重写

启用语言自定义时，会在英语 (en) 的 JSON 文件中保留以前使用页面 UI 自定义的标签编辑。 可以通过在语言自定义中上传语言资源来继续更改标签和其他字符串。

::: zone-end

### <a name="up-to-date-translations"></a>最新的翻译

Microsoft 致力于提供最新的翻译以供使用。 Microsoft 会持续改进翻译，使其符合需要。 Microsoft 将识别全局术语中的 bug 和更改，并在用户流中进行无缝更新。

### <a name="support-for-right-to-left-languages"></a>对从右向左书写的语言的支持

Microsoft 目前不支持从右向左书写的语言。 你可以通过使用自定义区域设置并使用 CSS 更改字符串的显示方式来实现此目的。 如果需要此功能，请在 [Azure 反馈](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)中为此功能投票。

### <a name="social-identity-provider-translations"></a>社交标识提供者翻译

Microsoft 为社交登录名提供 `ui_locales` OIDC 参数。 但某些社交标识提供者（包括 Facebook 和 Google）并不遵循此参数。

### <a name="browser-behavior"></a>浏览器行为

Chrome 和 Firefox 都会请求其设置的语言。 如果支持该语言，将先显示该语言，再显示默认语言。 Microsoft Edge 目前不会请求语言，而是直接使用默认语言。

## <a name="supported-languages"></a>支持的语言

Azure AD B2C 包括对以下语言的支持。 用户流语言由 Azure AD B2C 提供。 多重身份验证 (MFA) 通知语言由 [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md) 提供。

| 语言              | 语言代码 | 用户流         | MFA 通知  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| 阿拉伯语                | ar            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 保加利亚语             | bg            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| Bangla                | bn            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 加泰罗尼亚语               | ca            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 捷克语                 | cs            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 丹麦语                | da            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 德语                | de            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 希腊语                 | el            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 英语               | en            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 西班牙语               | es            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 爱沙尼亚语              | et            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 巴斯克语                | eu            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 芬兰语               | fi            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 法语                | fr            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 加利西亚语              | gl            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 古吉拉特语              | gu            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 希伯来语                | he            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 克罗地亚语              | hr            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 匈牙利语             | hu            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 印度尼西亚语            | id            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 意大利语               | it            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 日语              | ja            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 哈萨克语                | kk            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 卡纳达语               | kn            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 朝鲜语                | ko            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 立陶宛语            | lt            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 拉脱维亚语               | lv            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 马拉雅拉姆语             | ml            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 马拉地语               | mr            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 马来语                 | ms            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 挪威博克马尔语      | nb            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 荷兰语                 | nl            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 挪威语             | 否            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 旁遮普语               | pa            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 波兰语                | pl            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 葡萄牙语 - 巴西   | pt-br         | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 葡萄牙语 - 葡萄牙 | pt-pt         | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 罗马尼亚语              | ro            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 俄语               | ru            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 斯洛伐克语                | sk            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 斯洛文尼亚语             | sl            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 塞尔维亚语 - 西里尔文    | sr-cryl-cs    | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 塞尔维亚语 - 拉丁语       | sr-latn-cs    | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 瑞典语               | sv            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 泰米尔语                 | ta            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 泰卢固语                | te            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![X 指示否。](./media/user-flow-language-customization/no.png) |
| 泰语                  | th            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 土耳其语               | tr            | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 乌克兰语             | uk            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 越南语            | vi            | ![X 指示否。](./media/user-flow-language-customization/no.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 简体中文  | zh-hans       | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |
| 繁体中文 | zh-hant       | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) | ![绿色的选中标记。](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>后续步骤

::: zone pivot="b2c-user-flow"

有关如何自定义应用程序用户界面的详细信息，请参阅[在 Azure Active Directory B2C 中自定义应用程序的用户界面](customize-ui-with-html.md)。

::: zone-end 

::: zone pivot="b2c-custom-policy"

- 在 IEF 参考中详细了解 [localization](localization.md) 元素。
- 查看 Azure AD B2C 中可用的[本地化字符串 ID](localization-string-ids.md) 的列表。

::: zone-end 
