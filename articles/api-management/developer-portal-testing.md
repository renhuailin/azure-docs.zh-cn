---
title: 测试自承载开发人员门户
titleSuffix: Azure API Management
description: 了解如何为自承载 API 管理门户设置单元测试和端到端测试。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 62c5ab98e067bb735bcfa1510cae405b17461cac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646432"
---
# <a name="test-the-self-hosted-developer-portal"></a>测试自承载开发人员门户

本文介绍如何为[自承载门户](developer-portal-self-host.md)设置单元测试和端到端测试。

## <a name="unit-tests"></a>单元测试

单元测试是验证小部分功能的一种方法。 它在与应用程序其他部分隔离的情况下完成。

### <a name="example-scenario"></a>示例方案

在此方案中，要测试一个密码输入控件。 它仅接受至少包含以下内容的密码：

- 一个字母

- 一个数字

- 一个特殊字符
 
因此，验证这些要求的测试如下所示：

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>项目结构

通常会使单元测试位于它应该验证的组件旁。

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>模拟 HTTP 请求

在某些情况下，需要组件发出 HTTP 请求。 组件应正确应对不同种类的响应。 若要模拟特定 HTTP 响应，请使用 `MockHttpClient`。 它实现项目的许多其他组件所使用的 `HttpClient` 接口。

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>端到端测试

端到端测试执行特定用户方案，执行你期望用户执行的确切步骤。在 Web 应用程序（例如 Azure API 管理开发人员门户）中，用户滚动浏览内容并选择选项以实现特定结果。 

若要复制用户导航，可以使用浏览器操作帮助程序库，如 [Puppeteer](https://github.com/puppeteer/puppeteer)。 它使你能够模拟用户操作并自动执行假定的方案。 Puppeteer 还会在测试的任何阶段自动获取页面或组件的屏幕截图。 稍后将它们与以前的结果进行比较，以捕获偏差和潜在的回归。

### <a name="example-scenario"></a>示例方案

在此方案中，需要验证用户登录流。 此方案需要以下步骤：

1. 打开浏览器并导航到登录页面。

1. 输入电子邮件地址。

1. 输入密码。

1. 选择“登录”。

1. 验证用户是否已重定向到主页。

1. 验证该页面是否包含“配置文件”菜单项。 这是成功登录的一种可能指示。

若要自动运行测试，请创建具有完全相同步骤的脚本：

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> 诸如“#email”、“#password”和“#signin”之类的字符串是类似于 CSS 的选择器，用于标识页面上的 HTML 元素。 请参阅[选择器级别 3](https://www.w3.org/TR/selectors-3/) W3C 规范以了解详细信息。

### <a name="ui-component-maps"></a>UI 组件映射

用户流通常会经过相同的页面或组件。 一个良好示例是每个页面上都会出现的主网站菜单。 

创建 UI 组件映射，以避免为每个测试配置和更新相同的选择器。 例如，可以将前面示例中的步骤 2 到 6 替换为仅仅两行：

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>测试配置

某些方案可能需要预先创建的数据或配置。 例如，可能需要通过社交媒体帐户自动进行用户登录。 难以快速或轻松地创建该数据。

为此，可以向测试方案添加特殊的配置文件。 测试脚本可以从文件中提取所需数据。 根据版本和测试管道，测试可以从指定安全存储拉取机密。

下面是一个 `validate.config.json` 示例，它会存储在项目的 `src` 文件夹中。

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>无外设测试与正常测试

新式浏览器（如 Chrome 或 Microsoft Edge）允许在无外设模式和正常模式下运行自动化。 浏览器在无外设模式下运行，没有图形用户界面。 它仍会执行相同的页面和文档对象模型 (DOM) 操作。 传递管道中通常不需要浏览器 UI。 在这种情况下，在无外设模式下运行测试是不错的选择。

开发测试脚本时，查看浏览器中发生的确切情况会非常有用。 这是使用正常模式的好时机。

若要在模式之间切换，请在 `constants.ts` 文件中更改 `headless` 选项。 它位于项目的 `tests` 文件夹中：

```typescript
export const LaunchOptions = {
    headless: false
};
```

另一个有用的选项是 `slowMo`。 它会在每个操作之间暂停测试执行：

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>运行测试

可通过两种内置方式在此项目中执行测试：

**npm 命令**

```console
npm run test
```

**测试资源管理器**

适用于 VS Code 的测试资源管理器扩展（例如，[Mocha 测试资源管理器](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)）具有一个方便的 UI 和一个选项，可用于在每次更改源代码时自动运行测试：

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Visual Studio Code 测试资源管理器的屏幕截图":::

## <a name="next-steps"></a>后续步骤

详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)

- [自承载开发人员门户](developer-portal-self-host.md)
