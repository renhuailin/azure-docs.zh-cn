---
title: 监视 Azure 应用服务性能 ASP.NET | Microsoft Docs
description: 使用 ASP.NET 为 Azure 应用服务进行应用程序性能监视。 对加载和响应时间、依赖项信息绘制图表，并针对性能设置警报。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 5e85a12d017f5fc812ac4f910ce9517d73cc41c7
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154580"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet"></a>为 Azure 应用服务和 ASP.NET 进行应用程序监视 

现在，可以比过去更轻松地针对 [Azure 应用服务](../../app-service/index.yml)中运行的基于 ASP.NET 的 Web 应用程序启用监视。 以前需要手动检测某个应用，而现在应用服务映像中默认会内置最新的扩展/代理。 本文将逐步讲解如何启用 Azure Monitor Application Insights 监视，并提供有关如何自动完成大规模部署过程的初步指导。

> [!NOTE]
> 通过“开发工具” > “扩展”手动添加 Application Insights 站点扩展的功能已弃用。  此扩展安装方法依赖于每个新版本的手动更新。 扩展的最新稳定版现在会[预装](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)在应用服务映像中。 这些文件位于 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` 中，每发布一个稳定版本，它们都会自动更新。 如果你按照下面的基于代理的说明来启用监视，它会自动为你删除已弃用的扩展。

> [!NOTE]
> 如果同时检测到了基于代理的监视和基于手动 SDK 的检测，则只会采用手动检测设置， 这是为了防止发送重复数据。 有关详细信息，请查看下面的[故障排除部分](#troubleshooting)。

## <a name="enable-agent-based-monitoring"></a>启用基于代理的监视

> [!NOTE]
> 不支持 APPINSIGHTS_JAVASCRIPT_ENABLED 和 urlCompression 的组合。 有关详细信息，请参阅[故障排除部分](#appinsights_javascript_enabled-and-urlcompression-is-not-supported)中的说明。

1. 在应用服务的 Azure 控制面板中，选择“Application Insights”，然后选择“启用”。 

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="“Application Insights”选项卡的屏幕截图，其中选择了“启用”。"::: 

2. 选择创建新资源，或为此应用程序选择现有 Application Insights 资源。 

    > [!NOTE]
    > 当单击“确定”来创建新资源时，将提示你 **应用监视设置**。 选择“继续”会将新的 Application Insights 资源链接到你的应用服务，这样做还会 **触发应用服务的重新启动**。 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="“更改资源”下拉菜单的屏幕截图。"::: 

3. 指定要使用哪些资源后，可以选择 Application Insights 根据平台为应用程序收集数据的方式。 ASP.NET 应用监视默认已启用，它提供两种不同级别的集合。

    :::image type="content"source="./media/azure-web-apps-net/instrument-net.png" alt-text="屏幕截图显示 Application Insights 站点扩展页面，其中选择了“创建新资源”。"::: 
 
     下面是针对每个路由收集的数据的摘要：
            
    | 数据 | ASP.NET 基本集合 | ASP.NET 建议集合 |
    | --- | --- | --- |
    | 添加 CPU、内存和 I/O 使用情况趋势 |是 |是 |
    | 收集使用情况趋势，并启用从可用性结果到事务的关联 | 是 |是 |
    | 收集未经主机进程处理的异常 | 是 |是 |
    | 提高使用采样时，负载下的 APM 指标准确性 | 是 |是 |
    | 跨请求/依赖项边界关联微服务 | 否（仅单实例 APM 功能） |是 |

4. 若要配置采样，以前可以通过 applicationinsights.config 文件来控制，现在可以通过具有相应前缀 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor` 的应用程序设置与之交互。 

    - 例如，若要更改初始采样百分比，可以创建名为 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`、值为 `100` 的应用程序设置。
    - 若要禁用采样，请将 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage` 设置为值 `100`。
    - 支持的设置包括：
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_EvaluationInterval`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MaxTelemetryItemsPerSecond`
        
    - 有关支持的自适应采样遥测处理器设置和定义的列表，可以参阅[代码](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs)和[采样文档](./sampling.md#configuring-adaptive-sampling-for-aspnet-applications)。


## <a name="enable-client-side-monitoring"></a>启用客户端监视

可以选择为 ASP.NET 启用客户端监视。 若要启用客户端监视：

* “设置”>“配置”  
   * 在“应用程序设置”下，创建“新建应用程序设置”：

     名称：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值：`true`

   * **保存** 设置并 **重新启动** 应用。

若要禁用客户端监视，请从“应用程序设置”中删除关联的键值对，或将值设置为 false。

## <a name="automate-monitoring"></a>自动监视

若要在 Application Insights 中启用遥测数据收集，只需设置“应用程序设置”：

:::image type="content"source="./media/azure-web-apps-net/application-settings-net.png" alt-text="应用服务应用程序设置的屏幕截图，其中包含 Application Insights 设置。"::: 

### <a name="application-settings-definitions"></a>应用程序设置定义

|应用设置名称 |  定义 | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 用于控制运行时监视的主扩展。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  默认模式下，仅启用基本功能以确保最佳性能。 | `default` 或 `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | 控制是否要启用二进制重写引擎 `InstrumentationEngine`。 此设置会对性能以及冷启动/启动时间造成影响。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | 控制是否要随依赖项调用一起捕获 SQL 和 Azure 表文本。 性能警告：应用程序冷启动时间将会受到影响。 此设置需要 `InstrumentationEngine`。 | `~1` |

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>升级监视扩展/代理 - .NET 

### <a name="upgrade-from-versions-289-and-up"></a>从版本 2.8.9 及更高版本升级

从版本 2.8.9 的升级会自动发生，无需额外的操作。 新的监视位将在后台传送到目标应用服务，应用程序重启时，会拾取这些位。

若要查看正在运行的扩展的版本，请转到 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`。

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="用于查看正在运行的扩展版本的 URL 路径的屏幕截图。" border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>从版本 1.0.0 - 2.6.5 升级

从版本 2.8.9 开始，将使用预装的站点扩展。 如果使用更低的版本，可通过两种方法之一进行更新：

* [通过门户启用扩展进行升级](#enable-agent-based-monitoring)。 （即使已安装 Azure 应用服务的 Application Insights 扩展，UI 也只会显示“启用”按钮。 在幕后，旧的专用站点扩展将被删除。）

* [通过 PowerShell 升级](#enable-through-powershell)：

    1. 设置应用程序设置以启用预装的站点扩展 ApplicationInsightsAgent。 请参阅[通过 PowerShell 进行启用](#enable-through-powershell)。
    2. 手动删除名为“Azure 应用服务的 Application Insights 扩展”的专用站点扩展。

如果已从低于 2.5.1 的版本完成升级，请检查是否已从应用程序 bin 文件夹中删除了 ApplicationInsigths dll。[参阅故障排除步骤](#troubleshooting)。

## <a name="troubleshooting"></a>故障排除

下面是我们针对 Azure 应用服务中运行的基于 ASP.NET 的应用程序的基于扩展/代理的监视提供的分步故障排除指南。

1. 检查 `ApplicationInsightsAgent_EXTENSION_VERSION` 应用设置是否设置为值“~2”。
2. 浏览到 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`。  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="结果页上方的链接的屏幕截图。"border ="false"::: 
    
    - 确认 `Application Insights Extension Status` 为 `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.` 
    
         如果未在运行，请遵照[启用 Application Insights 监视的说明](#enable-agent-based-monitoring)。

    - 确认状态源存在并类似于：`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         如果不存在类似的值，则表示应用程序当前未运行或不受支持。 为确保应用程序运行，请尝试手动访问应用程序 URL/应用程序终结点，以提供运行时信息。

    - 确认 `IKeyExists` 为 `true`，如果为 `false`，请在应用程序设置中添加 `APPINSIGHTS_INSTRUMENTATIONKEY` 和 `APPLICATIONINSIGHTS_CONNECTION_STRING` 以及你的 ikey guid。

    - 确认 `AppAlreadyInstrumented`、`AppContainsDiagnosticSourceAssembly` 和 `AppContainsAspNetTelemetryCorrelationAssembly` 没有任何对应的条目。

         如果存在其中的任何条目，请从应用程序中删除以下包：`Microsoft.ApplicationInsights`、`System.Diagnostics.DiagnosticSource` 和 `Microsoft.AspNet.TelemetryCorrelation`。

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>用 Web 应用部署的默认网站不支持自动客户端监视

使用 `ASP.NET` 运行时在 Azure 应用服务中创建 Web 应用时，该应用会部署单个静态 HTML 页面作为入门网站。 该静态网页还会在 IIS 中加载 ASP.NET 托管 Web 部件。 这使得能够测试无代码服务器端监视，但不支持自动客户端监视。

如果你希望在 Azure 应用服务 Web 应用中测试 ASP.NET 的无代码服务器和客户端监视，建议遵循[创建ASP.NET Framework Web 应用](../../app-service/quickstart-dotnetcore.md?tabs=netframework48)官方指南，然后按照当前文章中的说明来启用监视。


### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>不支持 APPINSIGHTS_JAVASCRIPT_ENABLED 和 urlCompression

如果在对内容进行编码的情况下使用 APPINSIGHTS_JAVASCRIPT_ENABLED=true，可能会出现如下所示的错误：

- 500 URL 重写错误
- 发生 500.53 URL 重写模块错误，并出现消息“对 HTTP 响应的内容进行编码(gzip)时，无法应用出站重写规则”。

这是因为 APPINSIGHTS_JAVASCRIPT_ENABLED 应用程序设置指定为 true，同时存在内容编码。 目前不支持此方案。 解决方法是从应用程序设置中删除 APPINSIGHTS_JAVASCRIPT_ENABLED。 遗憾的是，这意味着，如果仍然需要客户端/浏览器端 JavaScript 检测，则需要对网页使用手动 SDK 引用。 按照适用于 JavaScript SDK 的手动检测的[说明](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)进行操作。

有关 Application Insights 代理/扩展的最新信息，请查看[发行说明](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)。

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>不支持 PHP 和 WordPress

不支持 PHP 和 WordPress 站点。 目前没有官方支持的 SDK/代理可用于在服务器端监视这些工作负荷。 但是，可以通过将客户端 javascript 添加到网页，使用 [JavaScript SDK](./javascript.md) 在 PHP 或 WordPress 站点上手动检测客户端事务。

下表更详细地解释了这些值的含义、其根本原因和建议的修复方法：

|问题值|说明|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | 此值表示扩展已检测到 SDK 的某个功能已在应用程序中存在，因此将会回退。 原因可能是引用了 `System.Diagnostics.DiagnosticSource`、`Microsoft.AspNet.TelemetryCorrelation` 或 `Microsoft.ApplicationInsights`  | 删除引用。 某中的某些引用是从特定的 Visual Studio 模板默认添加的，而旧版 Visual Studio 可能会添加对 `Microsoft.ApplicationInsights` 的引用。
|`AppAlreadyInstrumented:true` | 如果应用程序面向 ASP.NET Core 2.1 或 2.2，此值表示扩展已检测到 SDK 的某个功能已在应用程序中存在，因此将会回退 | [建议](https://github.com/aspnet/Announcements/issues/287) .NET Core 2.1、2.2 客户改用 Microsoft.AspNetCore.App 元包。 此外，在门户中打开“与 Application Insights SDK 互操作”（请参阅上面的说明）。|
|`AppAlreadyInstrumented:true` | 出现此值的原因还可能是前一部署的应用文件夹中存在上述 dll。 | 清除应用文件夹，以确保删除这些 dll。 检查本地应用的 bin 目录和应用服务的 wwwroot 目录。 （检查应用服务 Web 应用的 wwwroot 目录：高级工具(Kudu) > 调试控制台 > CMD > home\site\wwwroot）。
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 此值表示扩展已检测到对应用程序中的 `Microsoft.AspNet.TelemetryCorrelation` 的引用，因此将会回退。 | 删除引用。
|`AppContainsDiagnosticSourceAssembly**:true`|此值表示扩展已检测到对应用程序中的 `System.Diagnostics.DiagnosticSource` 的引用，因此将会回退。| 对于 ASP.NET，删除引用。 
|`IKeyExists:false`|此值表示 AppSetting 中不存在检测密钥 `APPINSIGHTS_INSTRUMENTATIONKEY`。 可能的原因：可能意外删除了这些值，忘记了在自动化脚本中设置这些值，等等。 | 确保该设置在应用服务的应用程序设置中存在。

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