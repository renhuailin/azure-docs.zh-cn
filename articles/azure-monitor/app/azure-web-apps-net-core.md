---
title: 使用 .NET Core 监视 Azure 应用服务性能 | Microsoft Docs
description: 使用 ASP.NET Core 为 Azure 应用服务进行应用程序性能监视。 对加载和响应时间、依赖项信息绘制图表，并针对性能设置警报。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: d31f8e59b297c16995482826414d07ab8cf5774f
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233212"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet-core"></a>为 Azure 应用服务和 ASP.NET Core 进行应用程序监视 

现在，可以比过去更轻松地针对 [Azure 应用服务](../../app-service/index.yml)中运行的基于 ASP.NET Core 的 Web 应用程序启用监视。 以前需要手动检测某个应用，而现在应用服务映像中默认会内置最新的扩展/代理。 本文将逐步讲解如何启用 Azure Monitor Application Insights 监视，并提供有关如何自动完成大规模部署过程的初步指导。

## <a name="enable-agent-based-monitoring"></a>启用基于代理的监视

# <a name="windows"></a>[Windows](#tab/Windows)

> [!IMPORTANT]
> Windows 上的自动检测支持以下 ASP.NET Core 版本：ASP.NET Core 3.1 和 5.0。 版本 2.0、2.1、2.2 和 3.0 已停用，不再受支持。 请升级到 .NET Core 的[受支持版本](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)，使自动检测能够正常工作。

Windows 不支持将来自 ASP.NET Core 的完整框架作为目标。 请改为通过代码[手动检测](./asp-net-core.md)。

在 Windows 中，仅支持[依赖于框架的部署](/dotnet/core/deploying/#publish-framework-dependent)，不支持[独立部署](/dotnet/core/deploying/#publish-self-contained)。

请参阅下面的[启用监视部分](#enable-monitoring )，开始为应用服务资源设置 Application Insights。 

# <a name="linux"></a>[Linux](#tab/Linux)

> [!IMPORTANT]
> 仅支持 ASP.NET Core 6.0（预览版）在 Linux 上自动检测。

> [!NOTE]
> Linux 自动检测应用程序服务门户支持目前为公共预览版。 这些预览版现已推出，但未提供服务级别协议。 某些功能可能不受支持或者受限。

 在 Linux 中，支持[依赖于框架的部署](/dotnet/core/deploying/#publish-framework-dependent)和[独立部署](/dotnet/core/deploying/#publish-self-contained)。 

请参阅下面的[启用监视部分](#enable-monitoring )，开始为应用服务资源设置 Application Insights。 

---
 

### <a name="enable-monitoring"></a>启用监视 

1. 在应用服务的 Azure 控制面板中，选择“Application Insights”，然后选择“启用”。 

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="“Application Insights”选项卡的屏幕截图，其中选择了“启用”。"::: 

2. 选择创建新资源，或为此应用程序选择现有 Application Insights 资源。

    > [!NOTE]
    > 当单击“确定”来创建新资源时，将提示你 **应用监视设置**。 选择“继续”会将新的 Application Insights 资源链接到你的应用服务，这样做还会 **触发应用服务的重新启动**。 

    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="“更改资源”下拉菜单的屏幕截图。"::: 

2. 指定要使用哪些资源后，可以选择 Application Insights 根据平台为应用程序收集数据的方式。 ASP.NET Core 提供“建议集合”或“已禁用”。

    :::image type="content"source="./media/azure-web-apps-net-core/instrument-net-core.png" alt-text="“检测应用程序”部分的屏幕截图。"::: 


## <a name="enable-client-side-monitoring"></a>启用客户端监视

对于使用“建议的集合”的 ASP.NET Core 应用，默认已启用客户端监视，无论是否存在应用设置“APPINSIGHTS_JAVASCRIPT_ENABLED” 。

如果出于某种原因想要禁用客户端监视：

* “设置”>“配置”  
   * 在“应用程序设置”下，创建“新建应用程序设置”：

     名称：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值：`false`

   * **保存** 设置并 **重新启动** 应用。


## <a name="automate-monitoring"></a>自动监视

若要在 Application Insights 中启用遥测数据收集，只需设置“应用程序设置”：

:::image type="content"source="./media/azure-web-apps-net-core/application-settings-net-core.png" alt-text="应用服务应用程序设置的屏幕截图，其中包含 Application Insights 设置。"::: 


### <a name="application-settings-definitions"></a>应用程序设置定义

|应用设置名称 |  定义 | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 用于控制运行时监视的主扩展。 | `~2` (Windows) 或 `~3` (Linux) |
|XDT_MicrosoftApplicationInsights_Mode |  默认模式下，仅启用基本功能以确保最佳性能。 | `disabled` 或 `recommended`。 |
|XDT_MicrosoftApplicationInsights_PreemptSdk | 仅适用于 ASP.NET Core 应用。 启用与 Application Insights SDK 的互操作。 将此扩展与 SDK 并行加载，并使用它来发送遥测数据（禁用 Application Insights SDK）。 |`1`|


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>升级监视扩展/代理 - .NET 

### <a name="upgrade-from-versions-289-and-up"></a>从版本 2.8.9 及更高版本升级

从版本 2.8.9 的升级会自动发生，无需额外的操作。 新的监视位将在后台传送到目标应用服务，应用程序重启时，会拾取这些位。

若要查看正在运行的扩展的版本，请转到 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`。

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="用于查看正在运行的扩展版本的 URL 路径的屏幕截图。" border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>从版本 1.0.0 - 2.6.5 升级

从版本 2.8.9 开始，将使用预装的站点扩展。 如果使用更低的版本，可通过下述两种方法之一进行更新：

* [通过门户启用扩展进行升级](#enable-agent-based-monitoring)。 （即使已安装 Azure 应用服务的 Application Insights 扩展，UI 也只会显示“启用”按钮。 在幕后，旧的专用站点扩展将被删除。）

* [通过 PowerShell 升级](#enable-through-powershell)：

    1. 设置应用程序设置以启用预装的站点扩展 ApplicationInsightsAgent。 请参阅[通过 PowerShell 进行启用](#enable-through-powershell)。
    2. 手动删除名为“Azure 应用服务的 Application Insights 扩展”的专用站点扩展。

如果已从低于 2.5.1 的版本完成升级，请检查是否已从应用程序 bin 文件夹中删除了 ApplicationInsigths dll。[参阅故障排除步骤](#troubleshooting)。

## <a name="troubleshooting"></a>故障排除

下面是我们针对 Azure 应用服务中运行的基于 ASP.NET Core 的应用程序的基于扩展/代理的监视提供的分步故障排除指南。

# <a name="windows"></a>[Windows](#tab/windows)

1. 检查 `ApplicationInsightsAgent_EXTENSION_VERSION` 应用设置是否设置为值“~2”。
2. 浏览到 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`。  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="结果页上方的链接的屏幕截图。"border ="false"::: 
    
    - 确认 `Application Insights Extension Status` 为 `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.` 
    
         如果它未运行，请按照[启用 Application Insights 监视的说明](#enable-agent-based-monitoring)进行操作。

    - 确认状态源存在并类似于：`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         如果不存在类似的值，则表示应用程序当前未运行或不受支持。 为确保应用程序运行，请尝试手动访问应用程序 URL/应用程序终结点，以提供运行时信息。

    - 确认 `IKeyExists` 为 `true`，如果为 `false`，请在应用程序设置中添加 `APPINSIGHTS_INSTRUMENTATIONKEY` 和 `APPLICATIONINSIGHTS_CONNECTION_STRING` 以及你的 ikey guid。

    -  在应用程序引用任何 Application Insights 包时（例如，如果你先前已使用 [ASP.NET Core SDK](./asp-net-core.md) 检测（或尝试检测）应用），则启用应用服务集成可能不会生效，数据可能不会显示在 Application Insights 中。 若要解决此问题，请在门户中启用“与 Application Insights SDK 互操作”，你将开始在 Application Insights 中看到数据。 
    - 
        > [!IMPORTANT]
        > 此功能处于预览阶段 

        :::image type="content"source="./media/azure-web-apps-net-core/interop.png" alt-text="屏幕截图，显示已启用互操作设置。"::: 
        
        即使 Application Insights SDK 被最初使用或尝试使用，现在仍将使用无代码方法发送数据。

        > [!IMPORTANT]
        > 如果应用程序使用 Application Insights SDK 发送任何遥测数据，则将禁用此类遥测 - 也就是说，自定义遥测（如果有，例如任何 Track*() 方法）和任何自定义设置（例如采样）都将被禁用。 

# <a name="linux"></a>[Linux](#tab/linux)

1. 检查 `ApplicationInsightsAgent_EXTENSION_VERSION` 应用设置是否设置为值“~3”。
2. 导航到 /home\LogFiles\ApplicationInsights\status 并打开 status_557de146e7fa_27_1.json。

    确认 `AppAlreadyInstrumented` 设置为“false”，`AiHostingStartupLoaded` 设置为“true”，`IKeyExists` 设置为“true”。

    下面是一个 JSON 文件示例：

    ```json
        "AppType":".NETCoreApp,Version=v6.0",
                
        "MachineName":"557de146e7fa",
                
        "PID":"27",
                
        "AppDomainId":"1",
                
        "AppDomainName":"dotnet6demo",
                
        "InstrumentationEngineLoaded":false,
                
        "InstrumentationEngineExtensionLoaded":false,
                
        "HostingStartupBootstrapperLoaded":true,
                
        "AppAlreadyInstrumented":false,
                
        "AppDiagnosticSourceAssembly":"System.Diagnostics.DiagnosticSource, Version=6.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51",
                
        "AiHostingStartupLoaded":true,
                
        "IKeyExists":true,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "ConnectionString":"InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/"
    
    ```
    
    如果 `AppAlreadyInstrumented` 为 true，则表明扩展检测到 SDK 的某一方面已在应用程序中存在，并将退出。

##### <a name="no-data"></a>无数据

1. 列出并标识用于托管应用的进程。 导航到终端，并在命令行上键入 `ps ax`。 
    
    输出应如下所示： 

   ```bash
     PID TTY      STAT   TIME COMMAND
    
        1 ?        SNs    0:00 /bin/bash /opt/startup/startup.sh
    
       19 ?        SNs    0:00 /usr/sbin/sshd
    
       27 ?        SNLl   5:52 dotnet dotnet6demo.dll
    
       50 ?        SNs    0:00 sshd: root@pts/0
    
       53 pts/0    SNs+   0:00 -bash
    
       55 ?        SNs    0:00 sshd: root@pts/1
    
       57 pts/1    SNs+   0:00 -bash
   ``` 


1. 然后列出应用进程的环境变量。 在命令行上键入 `cat /proc/27/environ | tr '\0' '\n`。
    
    输出应如下所示： 

    ```bash
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
    
    DOTNET_STARTUP_HOOKS=/DotNetCoreAgent/2.8.39/StartupHook/Microsoft.ApplicationInsights.StartupHook.dll
    
    APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/
    
    ```
    
1. 验证 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_STARTUP_HOOKS` 和 `APPLICATIONINSIGHTS_CONNECTION_STRING` 是否已设置。

---

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>用 Web 应用部署的默认网站不支持自动客户端监视

使用 `ASP.NET Core` 运行时在 Azure 应用服务中创建 Web 应用时，该应用会部署单个静态 HTML 页面作为入门网站。 该静态网页还会在 IIS 中加载 ASP.NET 托管 Web 部件。 这样就能够测试无代码服务器端监视，但不支持自动客户端监视。

如果你希望在 Azure 应用服务 Web 应用中测试 ASP.NET Core 的无代码服务器和客户端监视，建议遵循有关[创建 ASP.NET Core Web 应用](../../app-service/quickstart-dotnetcore.md)的官方指南。 然后，按照当前文章中的说明启用监视。

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>不支持 PHP 和 WordPress

不支持 PHP 和 WordPress 站点。 目前没有官方支持的可用于在服务器端监视这些工作负荷的 SDK/代理。 但是，可以通过将客户端 javascript 添加到网页，使用 [JavaScript SDK](./javascript.md) 在 PHP 或 WordPress 站点上手动检测客户端事务。

下表更详细地解释了这些值的含义、其根本原因和建议的修复方法：

|问题值 |说明 |Fix |
|---- |----|---|
| `AppAlreadyInstrumented:true` | 此值表示扩展已检测到 SDK 的某个功能已在应用程序中存在，因此将会回退。 原因可能是引用了 `Microsoft.ApplicationInsights.AspNetCore` 或 `Microsoft.ApplicationInsights`  | 删除引用。 某中的某些引用是从特定的 Visual Studio 模板默认添加的，而旧版 Visual Studio 可能会添加对 `Microsoft.ApplicationInsights` 的引用。 |
|`AppAlreadyInstrumented:true` | 如果应用程序面向 ASP.NET Core 2.1 或 2.2，此值表示扩展已检测到 SDK 的某个功能已在应用程序中存在，因此将会回退 | [建议](https://github.com/aspnet/Announcements/issues/287) .NET Core 2.1、2.2 客户改用 Microsoft.AspNetCore.App 元包。 此外，在门户中打开“与 Application Insights SDK 互操作”（请参阅上面的说明）。|
|`AppAlreadyInstrumented:true` | 出现此值的原因还可能是前面的一个部署的应用文件夹中存在 Microsoft.ApplicationsInsights dll。 | 清除应用文件夹，以确保删除这些 dll。 检查本地应用的 bin 目录和应用服务的 wwwroot 目录。 （检查应用服务 Web 应用的 wwwroot 目录：高级工具(Kudu) > 调试控制台 > CMD > home\site\wwwroot）。 |
|`IKeyExists:false`|此值表示 AppSetting 中不存在检测密钥 `APPINSIGHTS_INSTRUMENTATIONKEY`。 可能的原因：可能意外删除了这些值，忘记了在自动化脚本中设置这些值，等等。 | 确保该设置在应用服务的应用程序设置中存在。 |

## <a name="release-notes"></a>发行说明

有关最新的更新和 bug 修复，请[参阅发行说明](web-app-extension-release-notes.md)。

## <a name="next-steps"></a>后续步骤
* [在实时应用上运行探查器](./profiler.md)。
* [使用 Application Insights 监视 Azure Functions](monitor-functions.md)。
* [将 Azure 诊断配置为](../agents/diagnostics-extension-to-application-insights.md)向 Application Insights 发送数据。
* [监视服务运行状况指标](../data-platform.md)以确保服务可用且做出快速响应。
* [接收警报通知](../alerts/alerts-overview.md) 。
* 若要从访问网页的浏览器获取客户端遥测数据，请使用[适用于 JavaScript 应用和网页的 Application Insights](javascript.md)。
* [设置可用性 Web 测试](monitor-web-app-availability.md)，以便在站点关闭时发出警报。
