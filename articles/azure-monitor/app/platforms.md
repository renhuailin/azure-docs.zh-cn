---
title: Application Insights：语言、平台和集成 | Microsoft Docs
description: 适用于 Application Insights 的语言、平台和集成
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d388914badbd9ac8870a9d5e23370cd5b0319eea
ms.sourcegitcommit: 8154d7f8642d783f637cf6d857b4abbe28033f53
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2021
ms.locfileid: "113616390"
---
# <a name="supported-languages"></a>支持的语言

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-in-process-agent.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>支持的平台和框架

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>已部署应用程序的检测（无代码、基于代理）
* [Azure VM 和 Azure 虚拟机规模集](./azure-vm-vmss-apps.md)
* [Azure 应用服务](./azure-web-apps.md)
* [ASP.NET - 适用于使用 IIS 托管的 Web 应用](./status-monitor-v2-overview.md)
* [Azure 云服务](./cloudservices.md)，包括 Web 角色和辅助角色
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>通过代码进行检测 (SDK)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-in-process-agent.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [通用 Windows 应用](../app/mobile-center-quickstart.md) (App Center)
* [Windows 桌面应用程序、服务和辅助角色](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>记录框架
* [ILogger](./ilogger.md)
* [Log4Net、NLog 或 System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java、Log4J 或 Logback](java-2x-trace-logs.md)
* [LogStash 插件](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>导出和数据分析
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [流分析](./export-power-bi.md)

## <a name="unsupported-sdks"></a>不受支持的 SDK
我们知道还有其他几个社区支持的 SDK。 但是，Azure Monitor 仅在使用此页上列出的受支持 SDK 时提供支持。 我们一直在评估扩展对其他语言支持的机会，因此请关注我们的 [GitHub 公告](https://github.com/microsoft/ApplicationInsights-Announcements/issues)页，以获得最新的 SDK 消息。 

