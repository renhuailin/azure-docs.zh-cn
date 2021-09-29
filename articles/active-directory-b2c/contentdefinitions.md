---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中指定自定义策略的 ContentDefinitions 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/12/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbe79082f63065ed73b573ab87f3299f7df79148
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740238"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

可以自定义任何[自我断言技术配置文件](self-asserted-technical-profile.md)的外观。 Azure Active Directory B2C (Azure AD B2C) 在客户的浏览器中运行代码，并使用称为“跨域资源共享”(CORS) 的现代方法。

若要自定义用户界面，请在包含自定义 HTML 内容的 **ContentDefinition** 元素中指定一个 URL。 在自我断言技术配置文件或 **OrchestrationStep** 中，指向该内容定义标识符。 内容定义可以包含 **LocalizedResourcesReferences** 元素，该元素指定要加载的本地化资源列表。 Azure AD B2C 将用户界面元素与从 URL 加载的 HTML 内容合并，然后向用户显示页面。

**ContentDefinitions** 元素包含可在用户旅程中使用的 HTML5 模板的 URL。 HTML5 页面 URI 用于指定的用户界面步骤。 例如，登录或注册、密码重置或错误页面。 可以通过重写 HTML5 文件的 LoadUri 来修改外观。 可根据需要创建新的内容定义。 此元素可以包含对 [Localization](localization.md) 元素中指定的本地化标识符的本地化资源引用。

以下示例演示了本地化资源的内容定义标识符和定义：

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

**LocalAccountSignUpWithLogonEmail** 自我断言技术配置文件的元数据包含设置为 `api.localaccountsignup` 的内容定义标识符 **ContentDefinitionReferenceId**

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

**ContentDefinition** 元素包含以下属性：

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 内容定义标识符。 其值为本页稍后的“内容定义 ID”部分指定的值。  |

**ContentDefinition** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | 一个字符串，包含内容定义 HTML5 页面的 URL。 |
| RecoveryUri | 1:1 | 一个字符串，包含用于显示内容定义相关错误的 HTML 页面的 URL。 当前未使用，此值必须为 `~/common/default_page_error.html`。 |
| DataUri | 1:1 | 一个字符串，包含一个 HTML 文件的相对 URL，该文件提供要为步骤调用的用户体验。 |
| 元数据 | 0:1 | 一个键/值对集合，包含内容定义使用的元数据。 |
| LocalizedResourcesReferences | 0:1 | 本地化的资源引用集合。 使用此元素可以自定义用户界面和声明属性的本地化。 |

### <a name="loaduri"></a>LoadUri

LoadUri 元素用于为内容定义指定 HTML5 页面的 URL。 Azure AD B2C [自定义策略启动包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)附带使用 Azure AD B2C HTML 页面的内容定义。 LoadUri 以 `~` 开头，是 Azure AD B2C 租户的相对路径。

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  ...
</ContentDefinition>
```

可以[利用 HTML 模板自定义用户界面](customize-ui-with-html.md)。 使用 HTML 模板时，请提供绝对 URL。 以下示例使用 HTML 模板说明内容定义：

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
  ...
</ContentDefinition>
``` 

### <a name="datauri"></a>DataUri

**DataUri** 元素用于指定页面标识符。 Azure AD B2C 使用页面标识符来加载和启动 UI 元素与客户端 JavaScript。 值的格式为 `urn:com:microsoft:aad:b2c:elements:page-name:version`。 下表列出了可以使用的页面标识符。

| 页面标识符 | 说明 |
| ----- | ----------- |
| `globalexception` | 遇到异常或错误时显示错误页面。 |
| `providerselection`, `idpselection` | 列出可供用户在登录期间选择的标识提供者。  |
| `unifiedssp` | 显示一个窗体，用于通过基于电子邮件地址或用户名的本地帐户进行登录。 此值还提供“使我保持登录功能”和“忘记了密码?” 链接。 |
| `unifiedssd` | 显示一个窗体，用于通过基于电子邮件地址或用户名的本地帐户进行登录。 此页标识符已被弃用。 请改为使用 `unifiedssp` 页面标识符。  |
| `multifactor` | 在注册或登录期间使用短信或语音来验证电话号码。 |
| `selfasserted` | 显示用于从用户收集数据的表单。 例如，让用户创建或更新其个人资料。 |

### <a name="select-a-page-layout"></a>选择页面布局

在 `elements` 和页面类型之间插入 `contract` 即可启用 [JavaScript 客户端代码](javascript-and-page-layout.md)。 例如，`urn:com:microsoft:aad:b2c:elements:contract:page-name:version` 。

`DataUri` 的 [version](page-layout.md) 部分指定内容包，其中包含的 HTML、CSS 和 JavaScript 适用于策略中的用户界面元素。 如果打算启用 JavaScript 客户端代码，则 JavaScript 所基于的元素必须是不可变的。 如果它们不是不可变的，则任何更改都可能会导致用户页上出现意外行为。 为了防止这些问题，请强制使用页面布局，并指定页面布局版本。 这样做可以确保 JavaScript 所基于的所有内容定义不可变。 即使不打算启用 JavaScript，也仍然需要为页面指定页面布局版本。

以下示例显示版本 `1.2.0` 的 **DataUri** 为 `selfasserted`：

```xml
<!-- 
<BuildingBlocks> 
  <ContentDefinitions>-->
    <ContentDefinition Id="api.localaccountpasswordreset">
      <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
      <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
      <Metadata>
        <Item Key="DisplayName">Local account change password page</Item>
      </Metadata>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions> 
</BuildingBlocks> -->
```

#### <a name="migrating-to-page-layout"></a>迁移到页面布局

若要从旧的 DataUri 值（无页面合同）迁移到页面布局版本，请在页面版本后添加 `contract` 一词。 使用下表从旧的 DataUri 值迁移到页面布局版本。

| 旧 DataUri 值 | 新 DataUri 值 |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5` |

以下示例显示了内容定义标识符以及对应的包含[最新页面版本](page-layout.md)的 DataUri： 

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>元数据

**Metadata** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 项目 | 0:n | 与内容定义相关的元数据。 |

**Metadata** 元素的 **Item** 元素包含以下属性：

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| 密钥 | 是 | 元数据密钥。  |

#### <a name="metadata-keys"></a>元数据键

内容定义支持以下元数据项：

| Key | 必需 | 说明 |
| --------- | -------- | ----------- |
| DisplayName | 否 | 一个包含内容定义名称的字符串。 |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**LocalizedResourcesReferences** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | 内容定义的本地化资源引用列表。 |

**LocalizedResourcesReference** 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| 语言 | 是 | 一个字符串，包含符合 RFC 5646“用于标识语言的标记”的策略支持的语言。 |
| LocalizedResourcesReferenceId | 是 | **LocalizedResources** 元素的标识符。 |

以下示例演示包含对英语、法语和西班牙语本地化的引用的注册或登录内容定义：

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

若要了解如何将本地化支持添加到内容定义，请参阅[本地化](localization.md)。

## <a name="content-definition-ids"></a>内容定义 ID

**ContentDefinition** 元素的 ID 属性指定与内容定义相关的页面类型。 该元素定义要在其中应用自定义 HTML5/CSS 模板的上下文。 下表描述了标识体验框架识别的内容定义 ID 集及其相关的页面类型。 可以使用任意 ID 创建自己的内容定义。

| ID | 默认模板 | 说明 |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **错误页面** - 遇到异常或错误时显示错误页面。 |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **标识提供者选项页面** - 列出可供用户在登录期间选择的标识提供者。 选项通常是企业标识提供者、社交标识提供者（例如 Facebook 和 Google+）或本地帐户。 |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **注册时的标识提供者选项** - 列出可供用户在注册期间选择的标识提供者。 选项通常是企业标识提供者、社交标识提供者（例如 Facebook 和 Google+）或本地帐户。 |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **忘记了密码页面** - 显示一个窗体，用户必须填写该窗体才能发起密码重置。 |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本地帐户登录页面** - 显示一个窗体，用于通过基于电子邮件地址或用户名的本地帐户进行登录。 该窗体可以包含文本输入框和密码输入框。 |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本地帐户注册页面** - 显示一个窗体，用于注册基于电子邮件地址或用户名的本地帐户。 该窗体可以包含各种输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。 |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多重身份验证页面** - 在注册或登录期间使用短信或语音来验证电话号码。 |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **社交帐户注册页面** - 显示一个窗体，用户在使用社交标识提供者的现有帐户注册时必须填写此窗体。 除了密码输入字段之外，此页面类似于前面的社交帐户注册页面。 |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **个人资料更新页面** - 显示用户在更新其个人资料时可以访问的窗体。 除了密码输入字段之外，此页面类似于社交帐户注册页面。 |
| **api.signuporsignin** | [unified.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **统一注册或登录页面** - 处理用户注册和登录过程。 用户可以使用企业标识提供者、社交标识提供者（例如 Facebook 或 Google+）或本地帐户。 |

## <a name="next-steps"></a>后续步骤

有关使用内容定义自定义用户界面的示例，请参阅：

[使用自定义策略自定义应用程序的用户界面](customize-ui-with-html.md)
