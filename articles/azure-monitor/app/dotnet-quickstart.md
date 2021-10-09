---
title: 快速入门：使用 Azure Monitor Application Insights 监视 ASP.NET Core 应用
description: 使用 Azure Monitor Application Insights 检测 ASP.NET Core Web 应用以进行监视。
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 06/11/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 03506bd066ee81eb61c2d44f0d31f1336bd44f05
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081104"
---
# <a name="quickstart-monitor-an-aspnet-core-app-with-azure-monitor-application-insights"></a>快速入门：使用 Azure Monitor Application Insights 监视 ASP.NET Core 应用

在本快速入门中，你将使用 Application Insights SDK 检测 ASP.NET Core 应用，以收集客户端和服务器端遥测数据。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet)。
- .NET 5.0 SDK 或更高版本。 为平台[安装最新的 .NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet/5.0)。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

若要开始引入遥测数据，请在 Azure 订阅中创建一个 Application Insights 资源。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 选择“创建资源” > “开发人员工具” > “Application Insights”。

1. 填写出现的表单：
    1. 验证所选订阅。
    1. 选择现有或新的资源组。
    1. 为此 Application Insights 资源指定名称。
    1. 选择你所在位置附近的区域。
    1. 将“资源模式”设置为“经典”。 

1. 选择“查看 + 创建”按钮。 
1. 选择“创建”按钮。
1. 部署完成后，选择“转到资源”按钮。
1. 从显示的“概述”中，复制检测密钥（可在“Essentials”下找到）。

## <a name="create-and-configure-an-aspnet-core-web-app"></a>创建和配置 ASP.NET Core Web 应用

完成以下步骤以创建和配置新的 ASP.NET Core Web 应用：

1. 创建新的 ASP.NET Core Razor Pages 应用：
    
    ```dotnetcli
    dotnet new razor -o ai.quickstart
    ```
    
    上述命令在名为 ai.quickstart 的目录中创建新的 ASP.NET Core Razor Pages 应用。 
    
    > [!TIP]
    > 你可能想要[使用 Visual Studio 来创建应用](/visualstudio/ide/quickstart-aspnet-core)。

1. 从项目目录中，将 `Microsoft.ApplicationInsights.AspNetCore` 包添加到项目。 如果使用 Visual Studio，则可以使用 [NuGet 包管理器](/nuget/consume-packages/install-use-packages-visual-studio)。

    ```dotnetcli
    dotnet add package Microsoft.ApplicationInsights.AspNetCore --version 2.17.0 
    ```

1. 使用文本编辑器或 IDE，将 appsettings.json 修改为包含值 `ApplicationInsights.InstrumentationKey`，如下所示。 使用前面复制的检测密钥。

    :::code language="json" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/appsettings.json" range="1-12" highlight="2-4":::
    
    > [!IMPORTANT]
    > Application Insights SDK 需要 `ApplicationInsights.InstrumentationKey` 配置值。 请务必正确命名！

## <a name="configure-server-side-telemetry"></a>配置服务器端遥测数据

在 Startup.cs 的 `ConfigureServices` 方法中，将 Application Insights 服务添加到管道。 添加突出显示的行：

:::code language="csharp" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/startup.cs" range="24-28" highlight="4":::

## <a name="configure-client-side-telemetry"></a>配置客户端遥测数据

完成以下步骤来检测应用以发送客户端遥测数据：

1. 在 Pages/_ViewImports.cshtml 中，添加以下行：

    ```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

    上一个更改将注册包含 Application Insights 客户端脚本元素的 `Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet` 依赖项。

1. 在 Pages/Shared/_Layout.cshtml 中，在 `<head>` 元素中添加突出显示的行：

    :::code language="cshtml" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/pages/shared/_layout.cshtml" range="3-10" highlight="7":::

   此更改使用注入的 `JavaScriptSnippet` 对象，以确保 `<script>` 元素呈现在应用中每个页面的 `<head>` 元素中。

## <a name="validate-telemetry-ingestion"></a>验证遥测数据引入

需要几分钟时间将遥测数据引入到 Application Insights 中进行分析。 若要验证应用是否是实时发送遥测数据，请使用实时指标：

1. 使用 `dotnet run` 或 IDE 运行 Web 应用。
1. 在 Azure 门户中查看 Application Insights 资源时，请选择“调查”下的“实时指标”。
1. 在应用 (`http://localhost:5000`) 中，重复选择“主页”和“隐私”链接 。
1. 当在应用中发出请求时，查看“实时指标”显示上的活动。

## <a name="next-steps"></a>后续步骤

祝贺你！ 现在可以使用你的应用发送的遥测数据来执行以下操作：

- [查找运行时异常](tutorial-runtime-exceptions.md)。
- [查找性能问题](tutorial-performance.md)。
- [有关应用运行状况的警报](tutorial-alert.md)。

> [!div class="nextstepaction"]
> [详细了解 ASP.NET Core 中的 Application Insights](asp-net-core.md)
