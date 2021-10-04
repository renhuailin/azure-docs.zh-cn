---
title: Azure Web 应用扩展发行说明 - Application Insights
description: Application Insights 的用于运行时检测的 Azure Web 应用扩展的发行说明。
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 8b0ddf90f5e6ffbe685c4aea1239323beea6c106
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828823"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Application Insights 的 Azure Web 应用扩展发行说明

本文包含 Application Insights 的用于运行时检测的 Azure Web 应用扩展的发行说明。 这仅适用于预安装的扩展。

详细了解 [Application Insights 的 Azure Web 应用扩展](azure-web-apps.md)。

## <a name="frequently-asked-questions"></a>常见问题

- 如何查找当前正在使用的扩展版本？
    - 转到  `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights` 。 有关详细信息，请访问 [ASP.NET Core](./azure-web-apps-net-core.md#troubleshooting)、[ASP.NET](./azure-web-apps-net.md#troubleshooting)、[Java](./azure-web-apps-java.md#troubleshooting) 或 [Node.js](./azure-web-apps-nodejs.md#troubleshooting) 的基于扩展/代理的监视的分步故障排除指南。

- 如果我使用的是专用扩展，该怎么办？
    - 卸载专用站点扩展，因为它不再受支持。

## <a name="release-notes"></a>发行说明

### <a name="2838"></a>2.8.38

- JAVA 扩展：已从 Java 代理 2.5.1 升级到 [Java 代理 3.0.2（正式发布版）](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2)。
- Node.js 扩展：已将 AI SDK 从 1.8.7 更新到 [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8)。
- .NET Core：删除了不支持的版本（2.0、2.2、3.0）。 支持的版本为 2.1 和 3.1。

### <a name="2837"></a>2.8.37

- AppSvc Windows 扩展：让 .NET Core 可以与任何版本的 System.Diagnostics.DiagnosticSource.dll 一起使用。

### <a name="2836"></a>2.8.36

- AppSvc Windows 扩展：在 .NET Core 中启用了与 AI SDK 的互操作。

### <a name="2835"></a>2.8.35

- AppSvc Windows 扩展：添加了 .NET Core 3.1 支持。

### <a name="2833"></a>2.8.33

- .NET、.NET Core、Java 和 Node.js 代理以及 Windows 扩展：支持主权云。 可使用连接字符串向主权云发送数据。

### <a name="2831"></a>2.8.31

- ASP.NET Core 代理：修复了与更新后的 Application Insights SDK 的参考之一相关的问题（请参阅 2.8.26 的已知问题）。 如果运行时已加载了 `System.Diagnostics.DiagnosticSource.dll` 的错误版本，则无代码扩展现在不会使应用程序崩溃，它会退出。 对于受该问题影响的客户，建议从 bin 文件夹中删除 `System.Diagnostics.DiagnosticSource.dll`，或通过设置“ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24”来使用较旧版本的扩展；否则，不会启用应用程序监视。

### <a name="2826"></a>2.8.26

- ASP.NET Core 代理：修复了与更新后的 Application Insights SDK 相关的问题。 如果 ApplicationInsights.dll 已存在于 bin 文件夹中，则代理不会尝试加载 `AiHostingStartup`。 这通过 Assembly\<AiHostingStartup\>.GetTypes() 解决了与反射相关的问题。
- 已知问题：如果加载了 `DiagnosticSource` dll 的另一个版本，则可能会引发异常“`System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'`”。 例如，如果发布文件夹中存在 `System.Diagnostics.DiagnosticSource.dll`，则可能会发生这种情况。 作为缓解措施，请在应用服务中设置应用设置以使用以前版本的扩展：ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24。

### <a name="2824"></a>2.8.24

- 2\.8.21 的重新打包版本。

### <a name="2823"></a>2.8.23

- 添加了 ASP.NET Core 3.0 无代码监视支持。
- 对于运行时版本 2.1、2.2 和 3.0，已将 ASP.NET Core SDK 更新为 [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0)。 面向 .NET Core 2.0 的应用继续使用 2.1.1 版的 SDK。

### <a name="2814"></a>2.8.14

- 对于面向 .NET Core 2.1、2.2 的应用，已将 ASP.NET Core SDK 版本从 2.3.0 更新为最新版 (2.6.1)。 面向 .NET Core 2.0 的应用继续使用 2.1.1 版的 SDK。

### <a name="2812"></a>2.8.12

- 支持 ASP.NET Core 2.2 应用。
- 修复了 ASP.NET Core 扩展中的一个 bug，该 bug 会引发 SDK 的注入，即使已通过 SDK 检测了应用程序。 对于 2.1 和 2.2 版应用，application 文件夹中存在 ApplicationInsights.dll 现在会导致扩展退出。 对于 2.0 版应用，仅当通过 `UseApplicationInsights()` 调用启用了 ApplicationInsights 时，扩展才会退出。

- 永久修复了 ASP.NET Core 应用的不完整 HTML 响应。 此修补程序现已扩展为适用于 .NET Core 2.2 应用。

- 添加了以下支持：为 ASP.NET Core 应用关闭 JavaScript 注入 (`APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting`)。 对于 ASP.NET Core，默认情况下，JavaScript 注入处于“选择退出”模式，除非显式关闭。 （进行此默认设置是为了保持当前行为。）

- 修复了即使 ikey 不存在也会导致注入的 ASP.NET Core 扩展 bug。
- 修复了 SDK 版本前缀逻辑中导致遥测中的 SDK 版本不正确的 bug。

- 为 ASP.NET Core 应用添加了 SDK 版本前缀，以识别遥测数据的收集方式。
- 修复了 SCM- ApplicationInsights 页面，以正确显示预安装的扩展的版本。

### <a name="2810"></a>2.8.10

- 修复了 ASP.NET Core 应用的不完整 HTML 响应。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何为 Azure 应用服务配置监视，请访问 [Azure 应用服务的应用程序监视文档](azure-web-apps.md)。 
