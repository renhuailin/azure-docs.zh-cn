---
title: 快速入门：部署 ASP.NET Web 应用
description: 了解如何通过部署首个 ASP.NET 应用，在 Azure 应用服务中运行 Web 应用。
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 06/08/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 3b3abdf40d5aa9d56421361237432ddf08c0c016
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746530"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>快速入门：部署 ASP.NET Web 应用

本快速入门介绍如何创建第一个 ASP.NET Web 应用并将其部署到 [Azure 应用服务](overview.md)。 应用服务支持各种版本的 .NET 应用，并提供高度可缩放、自修补的 Web 托管服务。 ASP.NET Web 应用具有跨平台特性，可以在 Linux 或 Windows 上托管。 完成后，你将获得一个 Azure 资源组，其中包含一个应用服务托管计划，以及一个部署了 Web 应用程序的应用服务。

## <a name="prerequisites"></a>先决条件

:::zone target="docs" pivot="development-environment-vs"

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet)。
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> 与“ASP.NET 和 Web 开发”工作负载。

    如果已安装 Visual Studio 2019：

    - 通过选择“帮助” > “检查更新”，在 Visual Studio 中安装最新更新。
    - 通过选择“工具” > “获取工具和功能”，添加工作负荷。

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet)。
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>。
- <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure 工具</a>扩展。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安装最新的 .NET Core 3.1 SDK。</a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">安装最新的 .NET 5.0 SDK。</a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank">安装 .NET Framework 4.8 开发人员包。</a>

> [!NOTE]
> Visual Studio Code 可跨平台，但 .NET Framework 不可以。 若要使用 Visual Studio Code 开发 .NET Framework 应用，请考虑使用 Windows 计算机以满足生成依赖项。

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet)。
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>。
- .NET SDK（包括运行时和 CLI）。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安装最新的 .NET Core 3.1 SDK。</a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">安装最新的 .NET 5.0 SDK。</a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">安装最新的 .NET 5.0 SDK</a> 和 <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank">.NET Framework 4.8 开发人员包。</a>

> [!NOTE]
> [.NET CLI](/dotnet/core/tools) 可跨平台，但 NET Framework 不可以。 若要使用 .NET CLI 开发 .NET Framework 应用，请考虑使用 Windows 计算机以满足生成依赖项。

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>创建 ASP.NET Web 应用

> [!TIP]
> .NET Core 3.1 是 .NET 的当前长期支持 (LTS) 版本。 有关详细信息，请参阅 [.NET 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. 打开 Visual Studio，然后选择“创建新项目”。
1. 在“创建新项目”中，找到并选择“ASP.NET Core Web 应用”，然后选择“下一步”。
1. 在“配置新项目”中，将应用程序命名为“MyFirstAzureWebApp”，然后选择“下一步”。

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="配置 ASP.NET Core 3.1 Web 应用" border="true":::

1. 选择“.NET Core 3.1（长期支持）”。
1. 确保“身份验证类型”设置为“无身份验证”。 选择“创建”。

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio - 选择“.NET Core 3.1”和“无身份验证”作为“身份验证类型”。" border="true":::

1. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以在本地运行 Web 应用。 

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio -.NET Core 3.1 本地浏览" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. 打开 Visual Studio，然后选择“创建新项目”。
1. 在“创建新项目”中，找到并选择“ASP.NET Core Web 应用”，然后选择“下一步”。
1. 在“配置新项目”中，将应用程序命名为“MyFirstAzureWebApp”，然后选择“下一步”。

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio - 配置 ASP.NET 5.0 Web 应用。" border="true":::

1. 选择“.NET Core 5.0（当前版本）”。
1. 确保“身份验证类型”设置为“无身份验证”。 选择“创建”。

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio - 选择 .NET Core 5.0 时的其他信息。" border="true":::

1. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以在本地运行 Web 应用。 

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio - ASP.NET Core 5.0 本地运行。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. 打开 Visual Studio，然后选择“创建新项目”。
1. 在“创建新项目”中，找到并选择“ASP.NET Web 应用程序 (.NET Framework)”，然后选择“下一步”。
1. 在“配置新项目”中，将应用程序命名为“MyFirstAzureWebApp”，然后选择“创建”。

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio - 配置 ASP.NET Framework 4.8 Web 应用。" border="true":::

1. 选择 MVC 模板。
1. 请确保 **身份验证** 设置为  **不进行身份验证**。 选择“创建”。

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio - 选择 MVC 模板。" border="true":::

1. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以在本地运行 Web 应用。 

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio - ASP.NET Framework 4.8 本地运行。" lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

创建名为“MyFirstAzureWebApp”的新文件夹，并在 Visual Studio Code 中打开。 打开<a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">“终端”</a>窗口，并使用 [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) 命令创建新的 .NET Web 应用。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> `--target-framework-override` 标志是项目的目标框架名字对象 (TFM) 的自由格式文本替换，并不保证支持模板能够存在或可进行编译。 只能在 Windows 上生成和运行 .NET Framework 应用。

---

在 Visual Studio Code 的“终端”中，使用 [`dotnet run`](/dotnet/core/tools/dotnet-run) 命令本地运行该应用程序。

```dotnetcli
dotnet run
```

打开 Web 浏览器并导航到 `https://localhost:5001` 处的应用。


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

你将看到页面中显示的模板 ASP.NET Core 3.1 Web 应用。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - 在浏览器中本地运行 .NET Core 3.1。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

你将看到页面中显示的模板 ASP.NET Core 5.0 Web 应用。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - 在浏览器中本地运行 .NET 5.0。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

你将看到页面中显示的模板 ASP.NET Framework 4.8 Web 应用。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - 在浏览器中本地运行 .NET 4.8。" lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

在计算机上打开终端窗口，进入工作目录。 使用 [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) 命令创建新的 .NET Web 应用，然后将目录更改为新创建的应用。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> `--target-framework-override` 标志是项目的目标框架名字对象 (TFM) 的自由格式文本替换，并不保证支持模板能够存在或可进行编译。 只能在 Windows 上生成 .NET Framework 应用。

---

在同一终端会话中，使用 [`dotnet run`](/dotnet/core/tools/dotnet-run) 命令本地运行该应用程序。

```dotnetcli
dotnet run
```

打开 Web 浏览器并导航到 `https://localhost:5001` 处的应用。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

你将看到页面中显示的模板 ASP.NET Core 3.1 Web 应用。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - 本地浏览器中的 ASP.NET Core 3.1。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

你将看到页面中显示的模板 ASP.NET Core 5.0 Web 应用。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - 本地浏览器中的 ASP.NET Core 5.0。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

你将看到页面中显示的模板 ASP.NET Framework 4.8 Web 应用。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - 本地浏览器中的 ASP.NET Framework 4.8。" lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>发布 Web 应用

若要发布 Web 应用，必须先创建并配置一个可将应用发布到的新应用服务。

在设置应用服务的过程中，将会创建：

- 一个新的[资源组](../azure-resource-manager/management/overview.md#terminology)，用于包含该服务的所有 Azure 资源。
- 一个新的[托管计划](overview-hosting-plans.md)，用于指定托管应用的 Web 服务器场的位置、大小和功能。

遵循以下步骤创建应用服务并发布 Web 应用：

:::zone target="docs" pivot="development-environment-vs"

1. 在“解决方案资源管理器”中右键单击“MyFirstAzureWebApp”项目，然后选择“发布”。
1. 在“发布”中，选择“Azure”，然后单击“下一步”。

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio - 发布 Web 应用和目标 Azure。" border="true":::

1. 你的选项取决于你是否已登录 Azure 以及是否有一个链接到 Azure 帐户的 Visual Studio 帐户。 选择“添加帐户”或“登录”以登录 Azure 订阅。 如果你已经登录，请选择所需的帐户。

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio - 选择“登录到 Azure”对话框。":::

1. 选择特定目标，即 Azure 应用服务 (Linux) 或 Azure 应用服务 (Windows)。

    > [!IMPORTANT]
    > 面向 ASP.NET Framework 4.8 时，将使用 Azure 应用服务 (Windows)。

1. 在“应用服务实例”的右侧，选择 **+** 。

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio - “新建应用服务”应用对话框。":::

1. 对于“订阅”，请接受列出的订阅，或从下拉列表中选择一个新订阅。
1. 对于“资源组”，选择“新建”。 在“新资源组名称”中，输入“myResourceGroup”并选择“确定”。
1. 对于“托管计划”，请选择“新建”。
1. 在“托管计划:新建”对话框中，输入下表中指定的值：

    | 设置          | 建议的值          | 说明                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **托管计划** | *MyFirstAzureWebAppPlan* | 应用服务计划的名称。                                         |
    | **位置**     | “西欧”            | 托管 Web 应用的数据中心。                           |
    | **大小**         | *免费*                   | [定价层][app-service-pricing-tier]确定托管功能。 |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="创建新的托管计划":::

1. 在 **名称** 中，输入仅包含有效字符 `a-z`、`A-Z`、`0-9` 和 `-` 的唯一应用名称。 可以接受自动生成的唯一名称。 Web 应用的 URL 为 `http://<app-name>.azurewebsites.net`，其中 `<app-name>` 是应用名称。
1. 选择“创建”以创建 Azure 资源。

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio - “创建应用资源”对话框。":::

   向导完成后，便为你创建了 Azure 资源，你就可以进行发布了。

1. 选择“完成”关闭向导。
1. 在“发布”页中，选择“发布”。 Visual Studio 将生成、打包应用并将其发布到 Azure，然后在默认浏览器中启动该应用。

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    你将看到页面中显示的 ASP.NET Core 3.1 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure 中的 ASP.NET Core 3.1 Web 应用。":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    你将看到页面中显示的 ASP.NET Core 5.0 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure 中的 ASP.NET Core 5.0 Web 应用。":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    你将看到页面中显示的 ASP.NET Framework 4.8 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Azure 中的 ASP.NET Framework 4.8 Web 应用。":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

使用 Visual Studio Azure 工具扩展部署 Web 应用：

1. 在 Visual Studio Code 中，打开[“命令面板”](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)，快捷键为 <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>。
1. 搜索并选择“Azure 应用服务：部署到 Web 应用”。
1. 响应提示，如下所述：

    - 选择“MyFirstAzureWebApp”作为要部署的文件夹。
    - 在出现提示时选择“添加配置”。
    - 如果出现提示，请登录到你现有的 Azure 帐户。

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code - 登录到 Azure。" border="true":::

    - 选择 **订阅**。
    - 选择“创建新 Web 应用…高级”。
    - 对于“输入全局唯一名称”，请使用在所有 Azure 中都是唯一的名称（有效字符为 `a-z`、`0-9` 和 `-`）。 良好的模式是结合使用公司名称和应用标识符。
    - 选择“创建新的资源组”并提供一个名称，例如 `myResourceGroup`。
    - 当系统提示“选择运行时堆栈”时：
      - 对于 .NET Core 3.1，选择 .NET Core 3.1 (LTS)
      - 对于 .NET 5.0，选择 .NET 5
      - 对于 .NET Framework 4.8，选择 ASP.NET V4.8
    - 选择操作系统（Windows 或 Linux）。
        - 对于 .NET Framework 4.8，将隐式选择 Windows。
    - 选择“创建新的应用服务计划”，提供一个名称，然后选择“F1 免费”[定价层][app-service-pricing-tier]。
    - 对于 Application Insights 资源，选择“暂时跳过”。
    - 选择附近的位置。

1. 发布完成后，选择通知中的“浏览网站”，并在出现提示时选择“打开”。

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    你将看到页面中显示的 ASP.NET Core 3.1 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure 中的 ASP.NET Core 3.1 Web 应用。":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    你将看到页面中显示的 ASP.NET Core 5.0 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure 中的 ASP.NET Core 5.0 Web 应用。":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    你将看到页面中显示的 ASP.NET Framework 4.8 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Azure 中的 ASP.NET Framework 4.8 Web 应用。":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

使用 [`az webapp up`](/cli/azure/webapp#az_webapp_up) 命令在本地“MyFirstAzureWebApp”目录中部署代码：

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- 如果无法识别 `az` 命令，请确保已按照 [必备条件](#prerequisites) 中所述安装了 Azure CLI。
- 将 `<app-name>` 替换为在整个 Azure 中均唯一的名称（有效字符为 `a-z`、`0-9` 和 `-`）。 良好的模式是结合使用公司名称和应用标识符。
- `--sku F1` 参数在“免费”[定价层][app-service-pricing-tier]上创建 Web 应用。 省略此参数可使用更快的高级层，这会按小时计费。
- 用 `linux` 或 `windows` 替换 `<os>`。 面向 ASP.NET Framework 4.8 时，必须使用 `windows`。
- 可以选择包含参数 `--location <location-name>`，其中 `<location-name>` 是可用的 Azure 区域。 可以运行 [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) 命令来检索 Azure 帐户的允许区域列表。

此命令可能需要花费几分钟时间完成。 运行时，它提供以下相关信息：创建资源组、应用服务计划和托管应用，配置日志记录，然后执行 ZIP 部署。 然后，此命令使用应用的 URL 输出一条消息：

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

打开 Web 浏览器并导航到 URL：

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

你将看到页面中显示的 ASP.NET Core 3.1 Web 应用。

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - Azure 中的 ASP.NET Core 3.1 Web 应用。":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

你将看到页面中显示的 ASP.NET Core 5.0 Web 应用。

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - Azure 中的 ASP.NET Core 5.0 Web 应用。":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

你将看到页面中显示的 ASP.NET Framework 4.8 Web 应用。

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI - Azure 中的 ASP.NET Framework 4.8 Web 应用。":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>更新应用并重新部署

遵循以下步骤更新并重新部署 Web 应用：

:::zone target="docs" pivot="development-environment-vs"

1. 在“解决方案资源管理器”中你的项目下，打开“Index.cshtml”。
1. 将第一个 `<div>` 元素替换为以下代码：

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   保存更改。

1. 若要重新部署到 Azure，请在“解决方案资源管理器”中右键单击“MyFirstAzureWebApp”项目，然后选择“发布”。
1. 在“发布”摘要页中选择“发布” 。

    发布完成后，Visual Studio 将启动浏览器并转到 Web 应用的 URL。

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    你将看到页面中显示的更新之后的 ASP.NET Core 3.1 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure 中更新之后的 ASP.NET Core 3.1 Web 应用。":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    你将看到页面中显示的更新之后的 ASP.NET Core 5.0 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure 中更新之后的 ASP.NET Core 5.0 Web 应用。":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    你将看到页面中显示的更新之后的 ASP.NET Framework 4.8 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Azure 中更新之后的 ASP.NET Framework 4.8 Web 应用。":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. 打开 *index.cshtml*。
1. 将第一个 `<div>` 元素替换为以下代码：

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   保存更改。

1. 打开 Visual Studio Code 侧栏，选择“Azure”图标以展开其选项。
1. 在“应用服务”节点下，展开你的订阅，然后右键单击“MyFirstAzureWebApp”。
1. 选择“部署到 Web 应用…”。
1. 出现提示时，选择“部署”。
1. 发布完成后，选择通知中的“浏览网站”，并在出现提示时选择“打开”。

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    你将看到页面中显示的更新之后的 ASP.NET Core 3.1 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure 中更新之后的 ASP.NET Core 3.1 Web 应用。":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    你将看到页面中显示的更新之后的 ASP.NET Core 5.0 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure 中更新之后的 ASP.NET Core 5.0 Web 应用。":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    你将看到页面中显示的更新之后的 ASP.NET Framework 4.8 Web 应用。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Azure 中更新之后的 ASP.NET Framework 4.8 Web 应用。":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

在本地目录中，打开 Index.cshtml 文件。 替换第一个 `<div>` 元素：

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

保存更改，然后再次使用 `az webapp up` 命令重新部署应用：

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 具有跨平台特性，基于你以前将 `<os>` 替换为 `linux` 或 `windows` 的部署。

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 具有跨平台特性，基于你以前将 `<os>` 替换为 `linux` 或 `windows` 的部署。

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 具有框架依赖性，必须在 Windows 上托管。

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> 如果你有兴趣在 Linux 上托管 .NET 应用，请考虑从 [ASP.NET Framework 迁移到 ASP.NET Core](/aspnet/core/migration/proper-to-2x)。

---

此命令使用本地缓存在 .azure/config 文件中的值，包括应用名称、资源组和应用服务计划。

完成部署后，切换回在“浏览到应用”  步骤中打开的浏览器窗口，并点击“刷新”。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

你将看到页面中显示的更新之后的 ASP.NET Core 3.1 Web 应用。

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Azure 中更新之后的 ASP.NET Core 3.1 Web 应用。":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

你将看到页面中显示的更新之后的 ASP.NET Core 5.0 Web 应用。

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Azure 中更新之后的 ASP.NET Core 5.0 Web 应用。":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

你将看到页面中显示的更新之后的 ASP.NET Framework 4.8 Web 应用。

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI - Azure 中更新之后的 ASP.NET Framework 4.8 Web 应用。":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>管理 Azure 应用

若要管理 Web 应用，请转到 [Azure 门户](https://portal.azure.com)，然后搜索并选择“应用服务”。

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure 门户 - 选择“应用服务”选项。":::

在“应用服务”页上，选择 Web 应用的名称。

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure 门户 - “应用服务”页面，显示已选择一个示例 Web 应用。":::

Web 应用的“概述”页包含用于基本管理（例如浏览、停止、启动、重启和删除）的选项。 左侧菜单提供用于配置应用的更多页面。

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure 门户 - 应用服务概述页面。":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>后续步骤

本快速入门介绍如何创建 ASP.NET Web 应用并将其部署到 Azure 应用服务。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

请继续学习下一篇文章，了解如何创建 .NET Core 应用并将其连接到 SQL 数据库：

> [!div class="nextstepaction"]
> [教程：将 ASP.NET Core 应用与 SQL 数据库配合使用](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [配置 ASP.NET Core 3.1 应用](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

请继续学习下一篇文章，了解如何创建 .NET Core 应用并将其连接到 SQL 数据库：

> [!div class="nextstepaction"]
> [教程：将 ASP.NET Core 应用与 SQL 数据库配合使用](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [配置 ASP.NET Core 5.0 应用](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

请继续学习下一篇文章，了解如何创建 .NET Framework 应用并将其连接到 SQL 数据库：

> [!div class="nextstepaction"]
> [教程：将 ASP.NET 应用与 SQL 数据库配合使用](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [配置 ASP.NET Framework 应用](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
