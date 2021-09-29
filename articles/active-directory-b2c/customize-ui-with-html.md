---
title: 利用 HTML 模板自定义用户界面
titleSuffix: Azure AD B2C
description: 了解如何自定义使用 Azure Active Directory B2C 的应用程序的用户界面。
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
ms.openlocfilehash: 0f0ffa8a4a25df07cf212eb3352d3515d8c5267c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575550"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 中的 HTML 模板自定义用户界面

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

对 Azure Active Directory B2C (Azure AD B2C) 向客户显示的用户界面进行品牌设计和自定义有助于在应用程序中提供无缝的用户体验。 这些体验包括注册、登录、个人资料编辑和密码重置。 本文介绍了自定义用户界面 (UI) 的方法。 

> [!TIP]
> 如果只希望修改用户流页面的横幅徽标、背景图像和背景色，则可以尝试[公司品牌](customize-ui.md)功能。

## <a name="custom-html-and-css-overview"></a>自定义 HTML 和 CSS 概述

Azure AD B2C 使用[跨域资源共享 (CORS)](https://www.w3.org/TR/cors/) 在客户的浏览器中运行代码。 在运行时，将从你在用户流或自定义策略中指定的 URL 加载内容。 用户体验中的每个页面从你为该页面指定的 URL 加载其内容。 从 URL 加载内容后，该内容将与 Azure AD B2C 插入的 HTML 片段合并，然后向客户显示页面。

![自定义页面内容边距](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>自定义 HTML 页面内容

使用自己的品牌打造创建 HTML 页面来提供自定义页面内容。 此页面可以是静态 `*.html` 页，也可以是动态页，如 .NET、Node.js 或 PHP。

你的自定义页面内容可以包含任何 HTML 元素，包括 CSS 和 JavaScript，但不能包含不安全的元素（如 iframe）。 唯一必需的元素是将 `id` 设置为 `api` 的 div 元素，例如 HTML 页面中的 `<div id="api"></div>` 元素。

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>自定义默认 Azure AD B2C 页面

你可以自定义 Azure AD B2C 默认页面内容，而无需从头开始创建自定义页面内容。

下表列出了 Azure AD B2C 提供的默认页面内容。 下载文件，将其作为创建自己的自定义页面的起点。

| 默认页 | 说明 | 内容定义 ID<br/>（仅自定义策略） |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **错误页面**。 遇到异常或错误时显示此页面。 | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **自断言页面**。 将此文件作为社交帐户注册页、本地帐户注册页、本地帐户登录页面、密码重置等的自定义页面内容。 该窗体可以包含各种输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。 | *api.localaccountsignin*、*api.localaccountsignup*、*api.localaccountpasswordreset*、*api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多重身份验证页面** 在此页面上，用户可以在注册或登录期间（使用文字或语音）验证其电话号码。 | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **个人资料更新页面**。 此页面包含用户在更新其个人资料时可以访问的窗体。 除了密码输入字段之外，此页面类似于社交帐户注册页面。 | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **统一注册或登录页面**。 此页面处理用户注册和登录过程。 用户可以使用企业标识提供者、社交标识提供者（例如 Facebook 或 Google+）或本地帐户。 | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>承载页面内容

使用自己的 HTML 和 CSS 文件自定义 UI 时，可将 UI 内容托管在支持 CORS 的任何公用 HTTPS 终结点上。 例如，[Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)、[Azure 应用服务](../app-service/index.yml)、Web 服务器、CDN、AWS S3 或文件共享系统。

## <a name="guidelines-for-using-custom-page-content"></a>使用自定义页面内容的准则

- 在 HTML 文件中包含外部资源（如媒体、CSS 和 JavaScript 文件）时，请使用绝对 URL。
- 使用[页面布局版本](page-layout.md) 1.2.0 和更高版本，你可以在 HTML 标记中添加 `data-preload="true"` 属性，以控制 CSS 和 JavaScript 的加载顺序。 对于 `data-preload="true"`，该页面将在向用户显示之前进行构造。 此属性可通过预加载 CSS 文件来防止页面“闪烁”，而不会向用户显示不带样式的 HTML。 以下 HTML 代码片段演示了如何使用 `data-preload` 标记。

  ```html
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 建议你从默认页面内容开始，然后在其上构建。
- 你可以在自定义内容中[包含 JavaScript](javascript-and-page-layout.md)。
- 支持的浏览器版本：
  - Internet Explorer 11、10 和 Microsoft Edge
  - 对 Internet Explorer 9 和 8 的支持有限
  - Google Chrome 42.0 和更高版本
  - Mozilla Firefox 38.0 和更高版本
  - 适用于 iOS 和 macOS 的 Safari 版本 12 及更高版本
- 由于安全限制，Azure AD B2C 不支持 `frame`、`iframe` 或 `form` HTML 元素。

## <a name="localize-content"></a>本地化内容

可通过在 Azure AD B2C 租户中启用[语言自定义](language-customization.md)来本地化 HTML 内容。 启用此功能后，Azure AD B2C 可设置 HTML 页面语言属性，并将 OpenID Connect 参数 `ui_locales` 传递给终结点。

#### <a name="single-template-approach"></a>单一模板方法

在页面加载过程中，Azure AD B2C 使用当前语言设置 HTML 页面语言属性。 例如，`<html lang="en">`。 若要按当前语言呈现不同的样式，请使用 CSS `:lang` 选择器以及 CSS 定义。

下面的示例定义了以下类：

* `imprint-en` - 在当前语言为英语时使用。
* `imprint-de` - 在当前语言为德语时使用。
* `imprint` - 在当前语言既不是英语也不是德语时使用的默认类。

```css
.imprint-en:lang(en),
.imprint-de:lang(de) {
    display: inherit !important;
}
.imprint {
    display: none;
}
```

根据页面语言，将显示或隐藏以下 HTML 元素：

```html
<a class="imprint imprint-en" href="Link EN">Imprint</a>
<a class="imprint imprint-de" href="Link DE">Impressum</a>
```

#### <a name="multi-template-approach"></a>多模板方法

利用语言自定义功能，Azure AD B2C 可将 OpenID Connect 参数 `ui_locales` 传递到终结点。 内容服务器可使用此参数提供特定语言的 HTML 页。

> [!NOTE]
> Azure AD B2C 不会将 OpenID Connect 参数（例如 `ui_locales`）传递给[异常页面](page-layout.md#exception-page-globalexception)。

可以基于所用的区域设置从不同位置拉取内容。 在已启用 CORS 的终结点中，可以设置文件夹结构以托管特定语言的内容。 如果使用通配符值 `{Culture:RFC5646}`，则会调用正确的语言。

例如，自定义的页面 URI 可能类似于以下内容：

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

可以通过从以下位置提取内容来加载法语页面：

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>自定义页面内容演练

下面是该过程的概述：

1. 准备一个位置以托管自定义页面内容（可公开访问、启用 CORS 的 HTTPS 终结点）。
1. 下载并自定义默认页面内容文件，例如 `unified.html`。
1. 将自定义页面内容发布于公开可用的 HTTPS 终结点。
1. 为 Web 应用设置跨源资源共享 (CORS)。
1. 将策略指向自定义策略内容 URI。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. 创建 HTML 内容

在标题中使用产品的品牌名称创建 HTML 内容。

1. 复制以下 HTML 代码段。 这是一段采用适当格式的 HTML5 代码，它在“\<body\>”标签中包含名为“\<div id="api"\>\</div\>”的空元素。 此元素指示要在何处插入 Azure AD B2C 内容。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. 将复制的片段粘贴在文本编辑器中
1. 使用 CSS 设置 Azure AD B2C 插入页面的 UI 元素的样式。 以下示例显示了一个简单的 CSS 文件，其中还包含注册注入 HTML 元素的设置：

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  将文件另存为“customize-ui.html”。

> [!NOTE]
> 出于安全限制，如果使用 login.microsoftonline.com，将删除 HTML 窗体元素。 如果要在自定义 HTML 内容中使用 HTML 窗体元素，请[使用 b2clogin.com](b2clogin.md)。

### <a name="2-create-an-azure-blob-storage-account"></a>2. 创建 Azure Blob 存储帐户

在本文中，我们使用 Azure Blob 存储来托管内容。 可以选择将内容托管在 Web 服务器上，但必须[在 Web 服务器上启用 CORS](https://enable-cors.org/server.html)。

若要在 Blob 存储中托管 HTML 内容，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在“中心”菜单上，选择“新建” > “存储” > “存储帐户”。   
1. 为存储帐户选择一个 **订阅**。
1. 创建一个 **资源组** 或选择现有的资源组。
1. 为存储帐户输入唯一的 **名称**。
1. 为存储帐户选择 **地理位置**。
1. “部署模型”可保留为“Resource Manager”。
1. “性能”可保留为“标准”。
1. 将“帐户类型”更改为“Blob 存储”。
1. “复制”可保留为“RA-GRS”。
1. “访问层”可保留为“热”。
1. 选择“查看 + 创建”以创建存储帐户。
    部署完成后，将自动打开“存储帐户”页。

#### <a name="21-create-a-container"></a>2.1 创建容器

若要在 Blob 存储中创建公共容器，请执行以下步骤：

1. 在左侧菜单的“BLOB 服务”下，选择“Blob”。
1. 选择“容器”。
1. 对于“名称”，请输入“root”。 该名称可以是你选择的名称（例如“contoso”），但为简单起见，此示例中我们将使用“root”。
1. 对于“公共访问级别”，请选择“Blob”，然后选择“确定”。
1. 选择“root”以打开新容器。

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 上传自定义页面内容文件

1. 选择“上传”。
1. 选择“选择文件”旁边的文件夹图标。
1. 导航到“customize-ui.html”并将其选中，这是你之前在“页面 UI 自定义”部分中创建的。
1. 如果要上传到子文件夹，请展开“高级”，然后在“上传到文件夹”中输入文件夹名称。
1. 选择“上传”。
1. 选择你已上传的“customize-ui.html blob”。
1. 在“URL”文本框的右侧，选择“复制到剪贴板”图标以将 URL 复制到剪贴板。
1. 在 web 浏览器中，导航到复制的 URL，以验证上传的 blob 是否可访问。 如果它不可访问，例如遇到 `ResourceNotFound` 错误，请确保将容器访问类型设置为“blob”。

### <a name="3-configure-cors"></a>3. 配置 CORS

通过执行以下步骤，将 Blob 存储配置为跨域资源共享：

1. 在菜单中，选择“CORS”。
1. 对于“允许的源”，请输入 `https://your-tenant-name.b2clogin.com`。 将 `your-tenant-name` 替换为 Azure AD B2C 租户的名称。 例如，`https://fabrikam.b2clogin.com`。 输入租户名称时全部使用小写字母。
1. 对于“允许的方法”，请同时选择 `GET` 和 `OPTIONS`。
1. 对于“允许的标头”，请输入一个星号 (*)。
1. 对于“公开的标头”，请输入一个星号 (*)。
1. 对于“最大期限”，请输入 200。
1. 选择“保存”。

#### <a name="31-test-cors"></a>3.1 测试 CORS

通过执行以下步骤验证是否已准备就绪：

1. 重复“配置 CORS”步骤。 对于“允许的源”，请输入 `https://www.test-cors.org`
1. 导航到 [www.test-cors.org](https://www.test-cors.org/) 
1. 对于“远程 URL”框，请粘贴 HTML 文件的 URL。 例如 `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. 选择“发送请求”。
    结果应为 `XHR status: 200`。 
    如果收到错误，请确保 CORS 设置正确。 可能还需要清除浏览器缓存，或通过按 Ctrl+Shift+P 打开专用浏览会话。

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. 更新用户流

1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“用户流”，然后选择“B2C_1_signupsignin1”用户流。
1. 选择“页面布局”，然后在“统一注册或登录页面”下，对“使用自定义页面内容”单击“是”   。
1. 在“自定义页面 URI”中，输入之前记录的“custom-ui.html”文件的 URI。
1. 在页面顶部，选择“保存”。

### <a name="5-test-the-user-flow"></a>5. 测试用户流

1. 在 Azure AD B2C 租户中选择“用户流”，然后选择“B2C_1_signupsignin1”用户流。
1. 在该页顶部，单击“运行用户流”。
1. 单击“运行用户流”按钮。

应该会看到类似于以下示例的页面，其中元素基于所创建的 CSS 文件集中在一起：

![显示带有自定义 UI 元素的注册或登录页的 Web 浏览器](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. 修改扩展文件

要配置 UI 自定义，请将“ContentDefinition”及其子元素从基本文件复制到扩展文件。

1. 打开策略的基文件。 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkBase.xml`</em>。 此基本文件是自定义策略初学者包中包含的策略文件之一，你应该已在先决条件[自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)中获取。
1. 搜索并复制 ContentDefinitions 元素的全部内容。
1. 打开扩展文件， 例如，TrustFrameworkExtensions.xml。 搜索 **BuildingBlocks** 元素。 如果该元素不存在，请添加该元素。
1. 粘贴作为 BuildingBlocks 元素的子元素复制的 ContentDefinitions 元素的全部内容。
1. 在复制的 XML 中搜索包含 `Id="api.signuporsignin"` 的 ContentDefinition 元素。
1. 将 LoadUri 的值更改为上传到存储的 HTML 文件的 URL。 例如，`https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`。

    自定义策略应如下代码片段所示：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. 保存扩展文件。

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. 上传并测试已更新的自定义策略

#### <a name="51-upload-the-custom-policy"></a>5.1 上传自定义策略

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 搜索并选择“Azure AD B2C”。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择“上传自定义策略”。
1. 上传以前已更改的扩展文件。

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 通过使用“立即运行”测试自定义策略

1. 选择已上传的策略，然后选择“立即运行”。
1. 现在，应该可以使用电子邮件地址进行注册了。

## <a name="configure-dynamic-custom-page-content-uri"></a>配置动态自定义页面内容 URI

使用 Azure AD B2C 自定义策略可在 URL 路径中发送参数，或查询字符串。 通过将该参数传递到 HTML 终结点，可以动态更改页面内容。 例如，可以基于从 Web 或移动应用程序传递的参数，更改 Azure AD B2C 注册或登录页面上的背景图像。 参数可以是任何[声明解析程序](claim-resolver-overview.md)，如应用程序 ID、语言 ID 或自定义查询字符串参数，例如 `campaignId`。

### <a name="sending-query-string-parameters"></a>发送查询字符串参数

若要发送查询字符串参数，请在[信赖方策略](relyingparty.md)中添加 `ContentDefinitionParameters` 元素，如下所示。

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

在内容定义中，将 `LoadUri` 的值更改为 `https://<app_name>.azurewebsites.net/home/unified`。 自定义策略 `ContentDefinition` 应如下代码片段所示：

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

当 Azure AD B2C 加载页面时，它会调用你的 web 服务器终结点：

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>动态页内容 URI

可以根据所使用的参数从不同位置拉取内容。 在启用 CORS 的终结点中，设置文件夹结构以托管内容。 例如，可以按以下结构组织内容。 *每个语言/html 文件的根文件夹/文件夹*。 例如，自定义的页面 URI 可能类似于以下内容：

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C 为语言发送两个字母 ISO 代码，法语为 `fr`：

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>示例模板

可以在以下位置找到用于 UI 自定义的示例模板：

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

本项目包含以下模板：
- [海蓝](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [青灰](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [经典](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [模板资源](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

使用本示例：

1. 将存储库克隆到本地计算机。 选择模板文件夹 `/AzureBlue`、`/MSA` 或 `/classic`。
1. 将模板文件夹和 `/src` 文件夹下的所有文件上传到 Blob 存储，如前一部分中所述。
1. 接下来，打开模板文件夹中的每个 `\*.html` 文件。 然后，将 `https://login.microsoftonline.com` URL 的所有实例替换为在步骤 2 中上传的 URL。 例如：
    
    发件人：
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    到:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. 保存 `\*.html` 文件并将其上传到 Blob 存储。
1. 现在，如前文所述，修改策略，使其指向 HTML 文件。
1. 如果发现缺少字体、图像或 CSS，请检查扩展策略和 `\*.html` 文件中的引用。

## <a name="use-company-branding-assets-in-custom-html"></a>在自定义 HTML 中使用公司品牌打造资产

若要在自定义 HTML 中使用[公司品牌打造](customize-ui.md#configure-company-branding)资产，请在标记 `<div id="api">` 外添加以下标记。 图像源被替换为背景图像和横幅徽标的图像源。

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>后续步骤

了解如何启用[客户端 JavaScript 代码](javascript-and-page-layout.md)。
