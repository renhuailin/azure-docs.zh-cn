---
title: JavaScript 和页面布局版本
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中启用 JavaScript 和使用页面布局版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2021
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9db30170103f76eb4bc611107faeeedd45da9acc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724895"
---
# <a name="enable-javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中启用 JavaScript 和页面布局版本

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

借助 Azure Active Directory B2C (Azure AD B2C) [HTML 模板](customize-ui-with-html.md)，可以创建用户的身份体验。 HTML 模板只能包含某些 HTML 标记和特性。 允许的基本 HTML 标记，例如 &lt;b&gt;、&lt;i&gt;、&lt;u&gt;、&lt;h1&gt; 和 &lt;hr&gt;。 出于安全原因，删除了更高级的标记，如 &lt;script&gt; 和 &lt;iframe&gt;。

启用 JavaScript 并推进 HTML 标记和属性：

::: zone pivot="b2c-user-flow"

* 选择[页面布局](page-layout.md)
* 使用 Azure 门户在用户流上启用它
* 在请求中使用 [b2clogin.com](b2clogin.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

* 选择[页面布局](page-layout.md)
* 向[自定义策略](custom-policy-overview.md)添加一个元素
* 在请求中使用 [b2clogin.com](b2clogin.md)

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="begin-setting-up-a-page-layout-version"></a>开始设置页面布局版本

如果打算启用 JavaScript 客户端代码，则 JavaScript 所基于的元素必须是不可变的。 如果它们不是不可变的，则任何更改都可能会导致用户页上出现意外行为。 为了防止这些问题，请强制使用页面布局，并指定页面布局版本，确保 JavaScript 所基于的内容定义是不可变的。 即使不打算启用 JavaScript，也可为页面指定页面布局版本。

::: zone pivot="b2c-user-flow"

为用户流页面指定页面布局版本： 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“页面布局”。 选择“布局名称”，然后选择“页面布局版本”。

若要了解不同的页面布局版本，请参阅[页面布局版本更改日志](page-layout.md)。

![门户中的页面布局设置，显示了页面布局版本下拉列表](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

为自定义策略页面指定页面布局版本：

1. 为应用程序的用户界面元素选择[页面布局](contentdefinitions.md#select-a-page-layout)。
1. 为自定义策略中的所有内容定义定义[页面布局版本](contentdefinitions.md#migrating-to-page-layout)和页面`contract`版本。 值的格式必须包含单词 `contract`：_urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_。 

以下示例显示了内容定义标识符以及对应的包含页面协定的 DataUri： 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>启用 JavaScript

::: zone pivot="b2c-user-flow"

在用户流 **属性** 中，可以启用 JavaScript。 启用 JavaScript 也会强制使用页面布局。 然后就可以设置用户流的页面布局版本，如下一部分所述。

![用户流属性页面，突出显示了“启用 JavaScript”设置](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

通过向 [RelyingParty](relyingparty.md)元素添加 ScriptExecution 元素来启用脚本执行。

1. 打开自定义策略文件。 例如，SignUpOrSignin.xml。
1. 将 **ScriptExecution** 元素添加到 **RelyingParty** 元素中：

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. 保存并上传文件。

::: zone-end

## <a name="guidelines-for-using-javascript"></a>JavaScript 的使用准则

按照以下准则，使用 JavaScript 自定义应用程序的界面：

- 禁止事项 
    - 对 `<a>` HTML 元素绑定单击事件。
    - 依赖 Azure AD B2C 代码或注释。
    - 更改 Azure AD B2C HTML 元素的顺序或层次结构。 使用 Azure AD B2C 策略控制 UI 元素的顺序。
- 在调用 RESTful 服务时考虑到以下事项：
    - 可能需要设置 RESTful 服务 CORS 才能实现客户端 HTTP 调用。
    - 确保 RESTful 服务安全，且它仅使用 HTTPS 协议。
    - 不直接使用 JavaScript 来调用Azure AD B2C 终结点。
- 可以嵌入 JavaScript，也可以链接到外部 JavaScript 文件。 如果是使用外部 JavaScript 文件，请确保使用 绝对 URL，而不是相对 URL。
- JavaScript 框架：
    - Azure AD B2C 使用 [jQuery 的特定版本](page-layout.md#jquery-and-handlebars-versions)。 不包括 jQuery 的其他版本。 在同一页面上使用多个版本会造成问题。
    - 不支持使用 RequireJS。
    - Azure AD B2C 不支持大多数 JavaScript 框架。
- 可通过调用 `window.SETTINGS`、`window.CONTENT` 对象读取 Azure AD B2C 设置，例如当前的 UI 语言。 勿更改这些对象的值。
- 若要自定义 Azure AD B2C 错误消息，请在策略中使用本地化。
- 如果可以通过策略实现一切操作，建议这样做。

## <a name="javascript-samples"></a>JavaScript 示例

### <a name="show-or-hide-a-password"></a>显示或隐藏密码

帮助客户成功注册的一种常见的方式是：允许他们查看他们作为密码输入的内容。 该选项使用户能够根据需要轻松查看和更正密码，从而帮助用户登录。 任何密码类型的字段都有一个带有“显示密码”标签的复选框。  这使用户能够看到纯文本形式的密码。 使自断言页面的注册或登录模板中包括以下代码片段：

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>添加使用条款

使需要包含“使用条款”复选框的页面中含有以下代码。 本地帐户注册和社交帐户注册页面中通常需要该复选框。

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

在代码中，将 `termsOfUseUrl` 替换使用条款协议的链接。 对于你的目录，创建一个名为 **termsOfUse** 的新用户属性，然后包括 **termsOfUse** 作为用户属性。

## <a name="next-steps"></a>后续步骤

查找有关如何[在 Azure Active Directory B2C 中自定义应用程序的用户界面](customize-ui-with-html.md)的详细信息。
