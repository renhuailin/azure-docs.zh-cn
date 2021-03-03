---
title: 用于配置 Arkose 实验室 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何配置 Arkose 实验室 Azure Active Directory B2C 来识别有风险和虚假的用户
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 04492abc0f235c2dc6139adbe543bcce82f7f7b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646843"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>教程：配置 Arkose 实验室与 Azure Active Directory B2C

在本教程中，了解如何将 Azure Active Directory (AD) B2C 身份验证与 [Arkose 实验室](https://www.arkoselabs.com/)集成。 Arkose 实验室可帮助组织防范机器人攻击、帐户接管攻击和欺诈性帐户空缺。  

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的[Azure AD B2C 租户](tutorial-create-tenant.md)。

- [Arkose 实验室](https://www.arkoselabs.com/book-a-demo/)帐户。

## <a name="scenario-description"></a>方案描述

Arkose 实验室集成包括以下组件：

- **Arkose Labs** -一种欺诈和滥用服务，用于防止 bot 和其他自动滥用。

- **Azure AD B2C 注册用户流** -将使用 Arkose 实验室服务的注册体验。 将使用自定义 HTML 和 JavaScript 以及 API 连接器与 Arkose 实验室服务集成。

- **Azure 函数** -由你托管的 api 终结点，适用于 api 连接器功能。 此 API 负责对 Arkose 实验室会话令牌进行服务器端验证。

下图说明了 Arkose 实验室与 Azure AD B2C 的集成方式。

![图像显示 Arkose 实验室体系结构图](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| 步骤  | 说明 |
|---|---|
|1     | 用户注册并创建帐户。 当用户选择 "提交" 时，会出现 "Arkose 实验强制" 的质询。         |
|2     |  用户完成质询后，Azure AD B2C 将状态发送到 Arkose 实验室以生成令牌。 |
|3     |  Arkose 实验室生成令牌并将其发送回 Azure AD B2C。   |
|4     |  Azure AD B2C 调用中间 web API 来传递注册窗体。      |
|5     |  中间 web API 将注册窗体发送到 Arkose Lab 进行令牌验证。    |
|6     | Arkose Lab 处理并将验证结果发回到中间 web API。|
|7     | 中间 web API 会将成功或失败结果从质询发送到 Azure AD B2C。 |
|8     | 如果质询成功完成，则会将注册表单提交到 Azure AD B2C，并 Azure AD B2C 完成身份验证。|

## <a name="onboard-with-arkose-labs"></a>与 Arkose 实验室集成

1. 请联系 [Arkose](https://www.arkoselabs.com/book-a-demo/) 并创建帐户。

2. 创建帐户后，导航到 https://dashboard.arkoselabs.com/login  

3. 在仪表板中，导航到 "站点设置" 查找公钥和私钥。 稍后将需要此信息来配置 Azure AD B2C。 公钥和私钥的值 `ARKOSE_PUBLIC_KEY` `ARKOSE_PRIVATE_KEY` 在 [示例代码](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)中称为和。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>第1部分–创建 ArkoseSessionToken 自定义属性

若要创建自定义属性，请执行以下步骤：  

1. 中转到 **Azure 门户**  >  **Azure AD B2C**

2. 选择 **用户属性**

3. 选择“添加”

4. 输入 **ArkoseSessionToken** 作为属性名称

5. 选择“创建”

了解有关 [自定义属性](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-custom-attributes?pivots=b2c-user-flow)的详细信息。

### <a name="part-2---create-a-user-flow"></a>第2部分-创建用户流

用户流可以用于 **注册** 和 **登录** ，也可以只 **注册**。 只有在注册过程中才会显示 Arkose Labs 用户流。

1. 有关创建用户流的 [说明](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) ，请参阅。 如果使用现有用户流，则它必须为 **建议 (下一代预览)** 版本类型。

2. 在 "用户流设置" 中，单击 " **用户属性** "，然后选择 " **ArkoseSessionToken** " 声明。

![图像显示如何选择自定义属性](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>第3部分-配置自定义 HTML、JavaScript 和页面布局

中转到提供的 [HTML 脚本](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)。 此文件包含一个 HTML 模板，其中包含 JavaScript `<script>` 标记，它将执行以下三个操作：

1. 加载 Arkose 实验室脚本，该脚本将呈现 Arkose 实验室小组件，并执行客户端 Arkose 实验室验证。

2. `extension_ArkoseSessionToken` `ArkoseSessionToken` 从向用户显示的 UI 中隐藏对应于自定义特性的输入元素和标签。

3. 当用户完成 Arkose 实验室质询时，Arkose 实验室会验证用户的响应并生成一个令牌。 `arkoseCallback`自定义 JavaScript 中的回调会将的值设置 `extension_ArkoseSessionToken` 为生成的令牌值。 此值将提交到 API 终结点，如下一节中所述。

    请参阅 [此文](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) ，了解 Arkose 实验室客户端验证。

按照所述的步骤，为用户流使用自定义 HTML 和 JavaScript。

1. 修改 [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) 文件，使其 `<ARKOSE_PUBLIC_KEY>` 与你为客户端验证生成的值匹配，并用于为你的帐户加载 Arkose 实验室脚本。

2. 将 HTML 页面置于跨域资源共享上 (CORS) 启用的 web 终结点。 [创建 Azure blob 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) 并 [配置 CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。

  >[!NOTE]
  >如果有自己的自定义 HTML，请将元素复制并粘贴 `<script>` 到 HTML 页上。

3. 按照以下步骤配置页面布局

   a. 从 Azure 门户中转到 **Azure AD B2C**

   b. 导航到 " **用户** 流" 并选择用户流

   c. 选择 **页面布局**

   d. 选择 **本地帐户注册页面布局**

   e. 切换 **使用自定义页内容** 

   f. 粘贴自定义 HTML 所在位置的 URI **使用自定义页内容**

   g. 如果使用社交标识提供者，请为 **社交帐户注册页面** 布局重复 **步骤 e** 和 **f** 。

   ![显示页面布局的图像](media/partner-arkose-labs/page-layouts.png)

4. 从用户流中转到 " **属性** "，然后选择 " **启用 JavaScript** 强制页面布局 (预览") 。 请参阅此 [文章](https://docs.microsoft.com/azure/active-directory-b2c/javascript-and-page-layout?pivots=b2c-user-flow) 了解详细信息。

### <a name="part-4---create-and-deploy-your-api"></a>第4部分-创建和部署 API

安装 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 。

>[!Note]
>本部分中所述的步骤假设你使用 Visual Studio Code 部署 Azure 函数。 你还可以使用 Azure 门户、终端或命令提示符或任何其他代码编辑器来进行部署。

#### <a name="run-the-api-locally"></a>在本地运行 API

1. 导航到左侧导航栏上的 Visual Studio code 中的 Azure 扩展。 选择表示本地 Azure 函数的 **本地项目** 文件夹。

2. 按 **F5** 或使用 "**调试**" "  >  **开始调试**" 菜单启动调试器并附加到 Azure Functions 主机。 此命令自动使用 Azure 函数创建的单个调试配置。

3. Azure Function extension 将自动生成一些用于本地开发的文件，安装依赖项，并安装函数核心工具（如果尚未存在）。 这些工具可帮助进行调试体验。

4. 函数核心工具的输出将显示在 Visual Studio Code **终端** 面板中。 启动主机后， **Alt + 单击** 显示在输出中的本地 URL，打开浏览器并运行该函数。 在 Azure Functions 资源管理器中，右键单击该函数可查看本地托管函数的 url。

若要在测试过程中重新部署本地实例，请重复步骤1到4。

#### <a name="add-environment-variables"></a>添加环境变量

此示例使用 [HTTP 基本身份验证](https://tools.ietf.org/html/rfc7617)来保护 web API 终结点。

用户名和密码存储为环境变量，而不是存储库的一部分。 有关详细信息，请参阅文件 [ 上的local.settings.js](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=macos%2Ccsharp%2Cbash#local-settings-file) 。

1. 在根文件夹中的文件上创建 local.settings.js

2. 将以下代码复制并粘贴到该文件中：

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
**BASIC_AUTH_USERNAME** 和 **BASIC_AUTH_PASSWORD** 值将是用于对 Azure 函数的 API 调用进行身份验证的凭据。 选择所需的值。

`<ARKOSE_PRIVATE_KEY>`是在 Arkose 实验室服务中生成的服务器端机密。 它用于调用 [Arkose 实验室服务器端验证 API](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) 来验证前端生成的的值 `ArkoseSessionToken` 。

`<B2C_EXTENSIONS_APP_ID>`是 Azure AD B2C 用来在目录中存储自定义属性的应用的应用程序 ID。 可以通过导航到应用注册、搜索 b2c 扩展应用程序，并从 " **概述** " 窗格中复制应用程序 (客户端) id 来查找此应用程序 id。 删除 `-` 字符。

![Image 按应用 id 显示搜索](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>将应用程序部署到 web

1. 遵循 [本](https://docs.microsoft.com/azure/javascript/tutorial-vscode-serverless-node-04) 指南中所述的步骤，将 Azure 函数部署到云。 复制 Azure 函数的终结点 web URL。

2. 部署完成后，选择 " **上传设置** " 选项。 它会将环境变量上传到应用服务的 [应用程序设置](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#application-settings-in-azure) 。 还可以[通过 Azure 门户](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)配置或管理这些应用程序设置。

若要详细了解 Visual Studio Code 开发 Azure Functions，请参阅 [此文](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#republish-project-files) 。

#### <a name="configure-and-enable-the-api-connector"></a>配置并启用 API 连接器

为用户流[创建 API 连接器](https://docs.microsoft.com/azure/active-directory-b2c/add-api-connector)并启用它。 API 连接器配置应如下所示：

![Image 按应用 id 显示搜索](media/partner-arkose-labs/configure-api-connector.png)

- **终结点 url** -是在部署 Azure function 之前复制的函数 URL。

- **用户名和密码** -是之前定义为环境变量的用户名和密码。

若要启用 API 连接器，请在用户流的 " **API 连接器** 设置" 中，选择要在 **创建用户步骤之前** 在上调用的 api 连接器。 当用户在注册流中选择 " **创建** " 时，将调用 API。 API 将对值进行服务器端验证 `ArkoseSessionToken` ，此值是由 Arkose 小组件的回调设置的 `arkoseCallback` 。

![图像显示启用 api 连接器](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，并在 "策略" 下选择 " **用户流**"。

2. 选择以前创建的用户流。

3. 选择 " **运行用户流** "，然后选择设置：

   a. 应用程序：选择注册应用 (示例为 JWT) 

   b. 回复 URL：选择 "重定向 URL"

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户

5. 注销

6. 浏览登录流  

7. 选择 " **继续**" 后，将显示 Arkose 实验室测验题。

## <a name="additional-resources"></a>其他资源

- Azure AD B2C 注册用户流的[示例代码](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
