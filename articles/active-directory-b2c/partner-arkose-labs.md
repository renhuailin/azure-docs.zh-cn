---
title: 有关配置使用 Arkose Labs 的 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 有关配置使用 Arkose Labs 的 Azure Active Directory B2C 以识别风险用户和欺诈用户的教程
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d3fa9b89cb1fa7b0f7fa33025f908ae8ba5af7a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734551"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中配置 Arkose Labs

本示例教程介绍如何将 Azure Active Directory (AD) B2C 身份验证与 [Arkose Labs](https://www.arkoselabs.com/) 集成。 Arkose Labs 可帮助组织应对机器人攻击、帐户接管攻击和欺诈帐户漏洞。  

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。

- 一个 [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) 帐户。

## <a name="scenario-description"></a>方案描述

Arkose Labs 集成包括以下组件：

- **Arkose Labs** - 一个欺诈和滥用防护服务，用于防范机器人和其他自动式滥用。

- **Azure AD B2C 注册用户流** - 注册体验，将使用 Arkose Labs 服务。 将使用自定义 HTML 和 JavaScript 以及 API 连接器来与 Arkose Labs 服务集成。

- **Azure Functions** - 由你托管的 API 终结点，使用 API 连接器功能。 此 API 负责对 Arkose Labs 会话令牌执行服务器端验证。

下图描绘了 Arkose Labs 与 Azure AD B2C 的集成方式。

![图中显示了 Arkose Labs 体系结构图](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| 步骤  | 说明 |
|---|---|
|1     | 用户注册并创建帐户。 当用户选择“提交”时，会出现 Arkose Labs 强制质询内容。         |
|2     |  用户完成质询后，Azure AD B2C 将状态发送到 Arkose Labs 以生成令牌。 |
|3     |  Arkose Labs 生成令牌并将其发回给 Azure AD B2C。   |
|4     |  Azure AD B2C 调用一个中间 Web API 来传递注册表单。      |
|5     |  中间 Web API 将注册表单发送到 Arkose Labs 进行令牌验证。    |
|6     | Arkose Labs 处理验证并将验证结果发回给中间 Web API。|
|7     | 中间 Web API 将质询后的成功或失败结果发送到 Azure AD B2C。 |
|8     | 如果质询成功完成，则将注册表单提交到 Azure AD B2C，而 Azure AD B2C 将完成身份验证。|

## <a name="onboard-with-arkose-labs"></a>在 Arkose Labs 中登记

1. 联系 [Arkose](https://www.arkoselabs.com/book-a-demo/) 并创建帐户。

2. 创建帐户后，导航到 https://dashboard.arkoselabs.com/login  

3. 在仪表板中，导航到站点设置以找到公钥和私钥。 稍后需使用此信息来配置 Azure AD B2C。 在[示例代码](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)中，公钥和私钥的值称为 `ARKOSE_PUBLIC_KEY` 和 `ARKOSE_PRIVATE_KEY`。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>第 1 部分 – 创建 ArkoseSessionToken 自定义属性

若要创建自定义属性，请执行以下步骤：  

1. 转到“Azure 门户” > “Azure AD B2C” 

2. 选择“用户属性”

3. 选择“添加”

4. 输入 **ArkoseSessionToken** 作为属性名称

5. 选择 **创建**

详细了解[自定义属性](./user-flow-custom-attributes.md?pivots=b2c-user-flow)。

### <a name="part-2---create-a-user-flow"></a>第 2 部分 - 创建用户流

用户流可用于 **注册** 和 **登录**，或者只用于 **注册**。 Arkose Labs 用户流只在注册过程中才会显示。

1. 请参阅有关创建用户流的[说明](./tutorial-create-user-flows.md)。 如果使用现有的用户流，该用户流必须是“建议”版本类型。

2. 在用户流设置中，转到“用户属性”并选择“ArkoseSessionToken”声明。 

![展示如何选择自定义属性的插图](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>第 3 部分 - 配置自定义 HTML、JavaScript 和页面布局

转到提供的 [HTML 脚本](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)。 该文件包含一个 HTML 模板，其中的 JavaScript `<script>` 标记执行以下三项操作：

1. 加载 Arkose Labs 脚本，该脚本呈现 Arkose Labs 小组件并执行客户端 Arkose Labs 验证。

2. 在 UI 中隐藏向用户显示的、对应于 `ArkoseSessionToken` 自定义属性的 `extension_ArkoseSessionToken` 输入元素和标签。

3. 当用户完成 Arkose Labs 质询时，Arkose Labs 将验证用户的响应并生成令牌。 自定义 JavaScript 中的回调 `arkoseCallback` 将 `extension_ArkoseSessionToken` 值设置为生成的令牌值。 此值将按下一部分中所述提交到 API 终结点。

    请参阅[此文](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup)了解 Arkose Labs 客户端验证。

遵循所述步骤对用户流使用自定义 HTML 和 JavaScript。

1. 修改 [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) 文件，使 `<ARKOSE_PUBLIC_KEY>` 与你为客户端验证生成的、用于为帐户加载 Arkose Labs 脚本的值相匹配。

2. 将 HTML 页面托管在已启用跨源资源共享 (CORS) 的 Web 终结点上。 [创建 Azure Blob 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)并[配置 CORS](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。

  >[!NOTE]
  >如果你有自己的自定义 HTML，请将 `<script>` 元素复制并粘贴到 HTML 页面上。

3. 遵循以下步骤配置页面布局

   a. 在 Azure 门户中转到“Azure AD B2C”

   b. 导航到“用户流”并选择你的用户流

   c. 选择“页面布局”

   d. 选择“本地帐户注册页面布局”

   e. 将“使用自定义页面内容”切换为“是” 

   f. 在“自定义页面 URI”中，粘贴自定义 HTML 所在的 URI

   g. 如果使用的是社交标识提供者，请对“社交帐户注册页面”布局重复 **步骤 e** 和 **f**。

   ![显示页面布局的插图](media/partner-arkose-labs/page-layouts.png)

4. 在用户流中，转到“属性”并选择“启用 JavaScript”强制页面布局（预览版）。  请阅读[此文](./javascript-and-page-layout.md?pivots=b2c-user-flow)了解详细信息。

### <a name="part-4---create-and-deploy-your-api"></a>第 4 部分 - 创建并部署 API

安装适用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

>[!Note]
>本部分中所述的步骤假设使用 Visual Studio Code 来部署 Azure Functions。 也可以使用 Azure 门户、终端或命令提示符，或者任何其他代码编辑器进行部署。

#### <a name="run-the-api-locally"></a>在本地运行 API

1. 在左侧导航栏中导航到“Visual Studio Code 中的 Azure 扩展”。 选择表示本地 Azure Functions 的“本地项目”文件夹。

2. 按 **F5** 或使用“调试” > “开始调试”菜单启动调试器并附加到 Azure Functions 主机。  此命令自动使用 Azure Functions 创建的单个调试配置。

3. Azure Functions 扩展将自动生成几个用于本地开发的文件，安装依赖项，并安装 Function Core 工具（如果尚不存在）。 这些工具可帮助完成调试体验。

4. Function Core 工具的输出显示在 Visual Studio Code 的“终端”面板中。 启动主机后，在按住 **Alt** 键的同时单击输出中显示的本地 URL，以打开浏览器并运行函数。 在 Azure Functions 资源管理器中，右键单击该函数以查看本地托管函数的 URL。

若要在测试过程中重新部署本地实例，请重复步骤 1 到 4。

#### <a name="add-environment-variables"></a>添加环境变量

此示例使用 [HTTP 基本身份验证](https://tools.ietf.org/html/rfc7617)来保护 Web API 终结点。

用户名和密码以环境变量的形式存储，而不是存储库的一部分。 有关详细信息，请参阅 [local.settings.json](../azure-functions/functions-develop-local.md#local-settings-file) 文件。

1. 在根文件夹中创建 local.settings.json 文件

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
**BASIC_AUTH_USERNAME** 和 **BASIC_AUTH_PASSWORD** 值是用于对 Azure Functions 的 API 调用进行身份验证的凭据。 选择所需的值。

`<ARKOSE_PRIVATE_KEY>` 是在 Arkose Labs 服务中生成的服务器端机密。 它用于调用 [Arkose Labs 服务器端验证 API](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) 来验证前端生成的 `ArkoseSessionToken` 值。

`<B2C_EXTENSIONS_APP_ID>` 是 Azure AD B2C 在目录中存储自定义属性所用的应用的应用程序 ID。 可通过以下方式找到此应用程序 ID：导航到“应用注册”，搜索 b2c-extensions-app，然后复制“概述”窗格中的“应用程序(客户端) ID”。 删除 `-` 字符。

![展示按应用 ID 进行搜索的插图](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>将应用程序部署到 Web

1. 遵循[此指南](/azure/javascript/tutorial-vscode-serverless-node-04)中所述的步骤将 Azure Functions 部署到云中。 复制 Azure Functions 的终结点 Web URL。

2. 部署后，选择“上传设置”选项。 环境变量将上传到应用服务的[应用程序设置](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure)。 还可以[通过 Azure 门户](../azure-functions/functions-how-to-use-azure-function-app-settings.md)配置或管理这些应用程序设置。

请参阅[此文](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files)详细了解 Azure Functions 的 Visual Studio Code 开发。

#### <a name="configure-and-enable-the-api-connector"></a>配置并启用 API 连接器

[创建 API 连接器](./add-api-connector.md)并为用户流启用该连接器。 API 连接器配置应如下所示：

![展示如何配置 API 连接器的插图](media/partner-arkose-labs/configure-api-connector.png)

- **终结点 URL** - 前面在部署 Azure Functions 时复制的函数 URL。

- **用户名和密码** - 前面已定义为环境变量的用户名和密码。

若要启用 API 连接器，请在用户流的“API 连接器”设置中，选择要在执行“创建用户之前”步骤时调用的 API 连接器。  当用户在注册流中选择“创建”时，将调用 API。 API 对 `ArkoseSessionToken` 值执行服务器端验证，该值由 Arkose 小组件的回调 `arkoseCallback` 设置。

![展示如何启用 API 连接器的插图](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，在“策略”下选择“用户流”。

2. 选择前面创建的用户流。

3. 选择“运行用户流”，然后选择设置：

   a. 应用程序：选择已注册的应用（示例为 JWT）

   b. 回复 URL：选择重定向 URL

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户

5. 注销

6. 浏览登录流  

7. 选择“继续”后，将显示一道 Arkose Labs 试题。

## <a name="additional-resources"></a>其他资源

- Azure AD B2C 注册用户流的[示例代码](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)