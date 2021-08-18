---
title: 排查无数据问题 - 用于 .NET 的 Application Insights
description: 在 Azure Application Insights 中看不到数据？ 试试这里。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: 828eab154c09e3e623b5cfb84d6fc72d2bd480f5
ms.sourcegitcommit: 8154d7f8642d783f637cf6d857b4abbe28033f53
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2021
ms.locfileid: "113616138"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>排查无数据问题 - 用于 .NET/.NET Core 的 Application Insights

## <a name="some-of-my-telemetry-is-missing"></a>缺少一些遥测数据
*在 Application Insights 中，我只看到应用生成的一部分事件。*

* 如果持续看到同一个部分，可能是由于自适应[采样](../../azure-monitor/app/sampling.md)所导致。 要确认这一点，请打开“搜索”（通过“概述”边栏选项卡），并查看请求或其他事件的实例。 若要查看完整的属性详细信息，请选择“属性”部分底部的省略号图标 (...) 。 如果“请求计数”大于 1，则表示采样正在进行。
* 有可能你达到了定价计划的[数据率限制](../../azure-monitor/app/pricing.md#limits-summary)。 系统每隔一分钟应用这些限制。

我不时地遇到数据丢失的问题。

* 检查数据丢失是否发生在[遥测通道](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)中。

* 检查是否发生了遥测通道 [GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)中所述的任何已知问题。

当应用即将停止时，控制台应用或 Web 应用中出现了数据丢失问题。

* SDK 通道在缓冲区中保留遥测数据，并分批发送这些数据。 如果应用程序正在关闭，你可能需要显式调用 [Flush()](api-custom-events-metrics.md#flushing-data)。 `Flush()` 的行为取决于使用的实际[通道](telemetry-channels.md#built-in-telemetry-channels)。

## <a name="request-count-collected-by-application-insights-sdk-does-not-match-the-iis-log-count-for-my-application"></a>Application Insights SDK 收集的请求计数与应用程序的 IIS 日志计数不匹配

Internet Information Services (IIS) 记录到达 IIS 的所有请求的计数，本质上可能不同于到达应用程序的请求总数。 因此，不能保证 SDK 收集的请求计数与 IIS 日志总数相匹配。 

## <a name="no-data-from-my-server"></a>服务器未提供数据
* 我已在 Web 服务器上安装应用，但未看到服务器提供任何遥测数据。 该应用在开发计算机上正常运行。
* 可能是防火墙有问题。 [为 Application Insights 设置防火墙例外即可发送数据](../../azure-monitor/app/ip-addresses.md)。
* IIS 服务器可能缺少某些必备组件，例如 .NET Extensibility 4.5 或 ASP.NET 4.5。

我已在 Web 服务器上安装 [Azure Monitor Application Insights 代理](./status-monitor-v2-overview.md)来监视现有应用，但未看到任何结果。

* 请参阅[排查状态监视器问题](./status-monitor-v2-troubleshoot.md)。

> [!IMPORTANT]
> 建议使用[连接字符串](./sdk-connection-string.md?tabs=net)替代检测密钥。 新的 Azure 区域要求使用连接字符串而不是检测密钥。 连接字符串用于标识要与遥测数据关联的资源。 它还允许你修改可供你的资源将其用作遥测目标的终结点。 你需要复制连接字符串，并将其添加到应用程序的代码或环境变量中。


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException：无法加载文件或程序集“Microsoft.AspNet TelemetryCorrelation”

有关此错误的详细信息，请参阅 [GitHub 问题 1610] (https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610) )。

从 (2.4) 之前的 SDK 升级时，需要确保将以下更改应用到 `web.config` 和 `ApplicationInsights.config`：

1. 两个 http 模块，而不是一个。 在 `web.config` 中，应该有两个 http 模块。 对于某些场景，顺序很重要：

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. 在 `ApplicationInsights.config` 中，除了 `RequestTrackingTelemetryModule` 之外，还应具有以下遥测模块：

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

如果未能正确升级，可能会导致意外异常或无法收集遥测数据。


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Visual Studio 中没有“添加 Application Insights”选项
*在解决方案资源管理器中右键单击现有项目时，未看到任何 Application Insights 选项。*

* 工具并非支持所有类型的 .NET 项目。 支持 Web 和 WCF 项目。 对于其他项目类型，例如桌面或服务应用程序，仍可以[手动将 Application Insights SDK 添加到项目](./windows-desktop.md)。
* 请务必使用 [Visual Studio 2013 Update 3 或更高版本](/visualstudio/releasenotes/vs2013-update3-rtm-vs)。 该软件预装了开发人员分析工具，其中提供了 Application Insights SDK。
* 选择“工具”、“扩展和更新”，检查“开发人员分析工具”是否已安装并启用。   如果是，请单击“更新”查看是否有可用的更新。
* 打开“新建项目”对话框，选择“ASP.NET Web 应用程序”。 如果看到了 Application Insights 选项，则表示工具已安装。 如果未看到，请尝试卸载并重新安装 Developer Analytics Tools。

## <a name="adding-application-insights-failed"></a><a name="q02"></a>添加 Application Insights 失败
*尝试将 Application Insights 添加到现有项目时看到错误消息。*

可能的原因：

* 与 Application Insights 门户通信失败；或者
* Azure 帐户存在某种问题；
* 对于在其中创建新资源的订阅或组，只拥有[读取访问权限](./resources-roles-access-control.md)。

解决方法：

* 检查是否为适当的 Azure 帐户提供了登录凭据。
* 在浏览器中，检查是否可以访问 [Azure 门户](https://portal.azure.com)。 打开“设置”并查看是否有任何限制。
* [将 Application Insights 添加到现有项目](./asp-net.md)：在解决方案资源管理器中，右键单击项目并选择“添加 Application Insights”。

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>遇到错误“检测密钥不能为空”
可能是在安装 Application Insights 或日志记录适配器时发生了问题。

在解决方案资源管理器中右键单击项目，并选择“Application Insights”>“配置 Application Insights”。 将得到一个对话框，邀请你登录 Azure 并创建 Application Insights 资源或重复使用现有资源。

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> 生成服务器上出现“缺少 NuGet 包”
*在开发计算机上调试时所有生成都没有问题，但生成服务器上出现 NuGet 错误。*

请参阅 [NuGet Package Restore](https://docs.nuget.org/Consume/Package-Restore)（NuGet 包还原）和 [Automatic Package Restore](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)（自动包还原）。

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>缺少用于通过 Visual Studio 打开 Application Insights 的菜单命令
*在解决方案资源管理器中右键单击我的项目时，未看到任何 Application Insights 命令，或者未看到“打开 Application Insights”命令。*

可能的原因：

* Application Insights 资源是手动创建的，或者项目是 Application Insights 工具不支持的类型。
* 开发人员分析工具已在 Visual Studio 中禁用。
* Visual Studio 版本低于 2013 Update 3。

解决方法：

* 确保 Visual Studio 版本为 2013 Update 3 或更高。
* 选择“工具”、“扩展和更新”，检查“开发人员分析工具”是否已安装并启用。   如果是，请单击“更新”查看是否有可用的更新。
* 在解决方案资源管理器中右键单击项目。 如果看到命令“Application Insights”>“配置 Application Insights”，请使用它将项目连接到 Application Insights 服务中的资源。

否则，Developer Analytics Tools 不会直接支持你的项目类型。 要查看遥测数据，请登录到 [Azure 门户](https://portal.azure.com)，在左侧导航栏中选择“Application Insights”，然后选择应用程序。

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>从 Visual Studio 打开 Application Insights 时出现“拒绝访问”
*使用“打开 Application Insights”菜单命令时转到了 Azure 门户，但同时出现“拒绝访问”错误。*

上一次在默认浏览器中使用的 Microsoft 登录名无权访问[将 Application Insights 添加到此应用时所创建的资源](./asp-net.md)。 有两个可能的原因：

* 有多个的 Microsoft 帐户 - 也许同时拥有工作帐户和个人 Microsoft 帐户。 上一次在默认浏览器中使用的登录名的关联帐户不是有权[将 Application Insights 添加到项目](./asp-net.md)的帐户。
  * 解决方法：单击浏览器窗口右上角的用户名并注销。使用拥有访问权限的帐户登录。 然后，在左侧导航栏中单击“Application Insights”并选择应用。
* 其他人将 Application Insights 添加到了项目，但他们忘记向你提供创建项目的[资源组的访问权限](./resources-roles-access-control.md)。
  * 解决方法：如果他们使用的是组织帐户，便可以将你添加到团队；或者，他们可以单独向你授予对资源组的访问权限。

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>从 Visual Studio 打开 Application Insights 时出现“找不到资产”
*使用“打开 Application Insights”菜单命令时转到了 Azure 门户，但同时出现“找不到资产”错误。*

可能的原因：

* 应用程序的 Application Insights 资源已删除；或
* 检测密钥是通过直接编辑 ApplicationInsights.config 设置或更改的，但未更新项目文件。

ApplicationInsights.config 中的检测密钥控制遥测数据发送到的位置。 项目文件中的某个行控制在 Visual Studio 中使用命令时打开哪个资源。

解决方法：

* 在解决方案资源管理器中右键单击项目，并依次选择“Application Insights”、“配置 Application Insights”。 在对话框中，可以选择将遥测数据发送到现有资源，或创建新资源。 或：
* 直接打开资源。 登录到 [Azure 门户](https://portal.azure.com)，在左侧导航栏中单击“Application Insights”，然后选择应用。

## <a name="where-do-i-find-my-telemetry"></a>在哪里可以找到我的遥测数据？
*我已登录到 [Microsoft Azure 门户](https://portal.azure.com)，并且看到了 Azure 主页仪表板。但是，可以在哪里找到我的 Application Insights 数据？*

* 在左侧导航栏中单击“Application Insights”，并选择应用名称。 如果未看到任何项目，则需要[在 Web 项目中添加或配置 Application Insights](./asp-net.md)。  
  然后，会看到一些摘要图表。 可以逐个单击这些图表查看详细信息。
* 在 Visual Studio 中调试应用时，请单击“Application Insights”按钮。

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a>没有服务器数据（或根本没有数据）
*我运行了应用，并在 Microsoft Azure 中打开 Application Insights 服务，但所有图表都显示“了解如何收集...”或“未配置”。* 或者，*只有页面视图和用户数据，但没有任何服务器数据。*

* 在 Visual Studio 中以调试模式运行应用程序 (F5)。 使用应用程序，以便生成一些遥测。 检查是否可以在 Visual Studio 的“输出”窗口中看到记录的事件。  
  ![屏幕截图中显示了在 Visual Studio 中的调试模式下运行应用程序。](./media/asp-net-troubleshoot-no-data/output-window.png)
* 在 Application Insights 门户中，打开[诊断搜索](./diagnostic-search.md)。 数据通常会先显示在此处。
* 单击“刷新”按钮。 边栏选项卡会定期自行刷新，但你也可以手动刷新。 时间范围越大，刷新间隔就越长。
* 检查检测密钥是否匹配。 在 Application Insights 门户的应用主边栏选项卡中，查看“概要”下拉列表中的“检测密钥”。  然后，在 Visual Studio 的项目中，打开 ApplicationInsights.config 并找到 `<instrumentationkey>`。 检查两个密钥是否相同。 如果不同：  
  * 在门户中单击“Application Insights”，并找到具有正确密钥的应用资源；或
  * 在 Visual Studio 解决方案资源管理器中右键单击项目，并依次选择“Application Insights”、“配置”。 重置应用，将遥测数据发送到正确的资源。
  * 如果找不到匹配的密钥，请检查在 Visual Studio 中使用的登录凭据是否与门户中使用的相同。
* 在 [Microsoft Azure 主页仪表板](https://portal.azure.com)中，查看服务运行状况地图。 如果看到警报指示，请等待它们恢复“正常”，关闭再重新打开 Application Insights 应用程序边栏选项卡。
* 另请查看[我们的状态博客](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog)。
* 是否针对[服务器端 SDK](./api-custom-events-metrics.md) 编写了可能更改 `TelemetryClient` 实例或 `TelemetryContext` 中的检测密钥的任何代码？ 或者，是否编写了可能筛选掉过多内容的[筛选或采样配置](./api-filtering-sampling.md)？
* 如果编辑了 ApplicationInsights.config，请仔细检查 [TelemetryInitializers 和 TelemetryProcessors](./api-filtering-sampling.md) 的配置。 命名不当的类型或参数可能导致 SDK 不发送任何数据。

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>未提供有关页面视图、浏览器和使用情况的任何数据
*在“服务器响应时间”和“服务器请求”图表中看到了数据，但“页面视图加载时间”或者“浏览器”或“使用情况”边栏选项卡中没有任何数据。*

这些数据来自网页中的脚本。 

* 如果将 Application Insights 添加到现有 Web 项目，则[必须手动添加脚本](./javascript.md)。
* 确保 Internet Explorer 不是以兼容模式显示站点。
* 使用浏览器的调试功能（在某些浏览器中请按 F12，并选择“网络”）验证数据是否发送到了 `dc.services.visualstudio.com`。

## <a name="no-dependency-or-exception-data"></a>没有依赖项或异常数据
请参阅[依赖项遥测](./asp-net-dependencies.md)和[异常遥测](asp-net-exceptions.md)。

## <a name="no-performance-data"></a>没有性能数据
性能数据（CPU、IO 速率等）适用于 [Java Web 服务](java-2x-collectd.md)、[Windows 桌面应用](./windows-desktop.md)、[IIS Web 应用和服务（如果安装了 Application Insights 代理）](./status-monitor-v2-overview.md)以及 [Azure 云服务](./app-insights-overview.md)。 可在“设置”、“服务器”下面看到这些数据。

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>将应用发布到服务器后未看到（服务器）数据
* 请检查是否确实将 Microsoft. ApplicationInsights DLL 连同 Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll 一起复制到了服务器。
* 在防火墙中，可能需要[打开某些 TCP 端口](./ip-addresses.md)。
* 如果必须使用代理在企业网络外部发送数据，请在 Web.config 中设置 [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71))
* Windows Server 2008：请确保已安装以下更新：[KB2468871](https://support.microsoft.com/kb/2468871)、[KB2533523](https://support.microsoft.com/kb/2533523)、[KB2600217](https://www.microsoft.com/download/details.aspx?id=28936)。

## <a name="i-used-to-see-data-but-it-has-stopped"></a>我以前看到了数据，但现在看不到
* 是否达到了数据点的每月配额？ 打开“设置/配额和定价”即可检查。如果达到了配额，可以升级计划，或付费购买更多的容量。 请参阅[定价方案](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="i-dont-see-all-the-data-im-expecting"></a>未按预期看到所有数据
如果应用程序发送大量数据，并且使用的是用于 ASP.NET 的 Application Insights SDK 2.0.0-beta3 或更高版本，则[自适应采样](./sampling.md)功能可以正常运行，只发送一部分遥测数据。

可以禁用该功能，但不建议这样做。 采样旨在正确传输相关遥测数据，以便进行诊断。

## <a name="client-ip-address-is-0000"></a>客户端 IP 地址为 0.0.0.0

在 2018 年 2 月 5 日，我们宣布我们删除了客户端 IP 地址的日志记录。 这不会影响地理位置。

> [!NOTE]
> 如果需要 IP 地址的前 3 个八位字节，则可以使用[遥测初始化程序](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)添加自定义属性。
> 这不会影响 2018 年 2 月 5 日之前收集的数据。

## <a name="wrong-geographical-data-in-user-telemetry"></a>用户遥测数据包含错误的地理数据
城市、区域和国家/地区维度派生自 IP 地址，因此不一定始终都是准确的。 首先针对位置处理这些 IP 地址，然后将其更改为 0.0.0.0 以进行存储。

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>在 Azure 云服务中运行时发生“找不到方法”异常
生成的项目是否面向 .NET 4.6？ Azure 云服务角色不能现成地支持 4.6。 请先[在每个角色上安装 4.6](../../cloud-services/cloud-services-dotnet-install-dotnet.md)，再运行应用。

## <a name="troubleshooting-logs"></a>故障排除日志

按照这些说明来捕获框架的故障排除日志。

### <a name="net-framework"></a>.NET Framework

1. 从 NuGet 安装 [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) 包。 安装的版本必须与当前安装的 `Microsoft.ApplicationInsighs` 版本匹配

2. 修改 applicationinsights.config 文件以包括以下内容：

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.FileDiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    应用程序必须对配置的位置具有写入权限

3. 重新启动进程，以便 SDK 获取这些新设置

4. 完成后还原这些更改。

### <a name="net-core"></a>.NET Core

1. 安装 NuGet 中[适用于 ASP.NET Core 的 Application Insights SDK NuGet 包](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 安装的版本必须与当前安装的 `Microsoft.ApplicationInsights` 版本匹配。

   Microsoft.ApplicationInsights.AspNetCore 的最新版本为 2.14.0，它引用 Microsoft.ApplicationInsights 版本 2.14.0。 因此，要安装的 Microsoft.ApplicationInsights.AspNetCore 版本应该是 2.14.0。

2. 修改 `Startup.cs` 类中的 `ConfigureServices` 方法：

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    应用程序必须对配置的位置具有写入权限

3. 重新启动进程，以便 SDK 获取这些新设置

4. 完成后还原这些更改。


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> 使用 PerfView 收集日志
[PerfView](https://github.com/Microsoft/perfview) 是一个免费的诊断和性能分析工具，它可以从许多源收集诊断信息并将其可视化，从而帮助你查明 CPU、内存和其他问题。

PerfView 可以捕获 Application Insights SDK 日志和 EventSource 自我故障排除日志。

若要收集日志，请下载 PerfView 并运行以下命令：
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

可根据需要修改这些参数：
- **MaxCollectSec**。 设置此参数可防止 PerfView 无限期运行并影响服务器的性能。
- **OnlyProviders**。 设置此参数可以仅从 SDK 收集日志。 可根据自己的具体调查自定义此列表。 
- **NoGui**。 设置此参数可以在不使用 GUI 的情况下收集日志。


有关详细信息，请参阅：
- [使用 PerfView 记录性能跟踪](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView)。
- [Application Insights 事件源](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/troubleshooting/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>使用 dotnet-trace 收集日志

此外，客户还可以使用跨平台的 .NET Core 工具 [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace) 来收集日志，以便进一步进行故障排除。 这对于基于 Linux 的环境可能特别有用。

安装 [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace) 之后，在 bash 中执行以下命令。

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>如何删除 Application Insights

按照[此文](./remove-application-insights.md)中提供的步骤，了解如何在 Visual Studio 中删除 Application Insights。

## <a name="still-not-working"></a>仍然无法解决问题...
* [有关 Application Insights 的 Microsoft 问答页](/answers/topics/azure-monitor.html)
