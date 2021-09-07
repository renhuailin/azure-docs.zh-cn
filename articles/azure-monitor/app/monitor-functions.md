---
title: 使用 Application Insights 监视 Azure Functions 上运行的应用程序 - Azure Monitor | Microsoft Docs
description: Azure Monitor 与 Azure Functions 上运行的应用程序无缝集成，可用来监视应用的性能并立即查明应用中的问题。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 08/27/2021
ms.openlocfilehash: d8364915518104581a9f9ffb03b7ee7eda86c447
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252215"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>使用 Azure Monitor Application Insights 监视 Azure Functions

[Azure Functions](../../azure-functions/functions-overview.md) 内置集成了 Azure Application Insights 来监视函数。 对于 .NET 和 .NETCore 以外的语言，需要额外的特定于语言的辅助角色/扩展才能充分利用分布式跟踪。 

Application Insights 收集日志、性能和错误数据，并自动检测性能异常。 Application Insights 包含功能强大的分析工具，可帮助你诊断问题并了解函数的使用方式。 当你能够洞察应用程序数据后，你可以持续改善性能和可用性。 你甚至可以在本地函数应用项目开发过程中使用 Application Insights。 

Azure Functions 中内置了必需的 Application Insights 检测。 你只需提供有效的检测密钥便可将函数应用连接到 Application Insights 资源。 在 Azure 中创建函数应用资源时，应将检测密钥添加到应用程序设置中。 如果函数应用还没有此密钥，你可以手动设置此密钥。 有关详细信息，请参阅[监视 Azure Functions](../../azure-functions/functions-monitoring.md?tabs=cmd)。

## <a name="distributed-tracing-for-java-applications-public-preview"></a>针对 Java 应用程序的分布式跟踪（公共预览版）

> [!IMPORTANT]
> 适用于 Java Azure Functions（Windows 和 Linux）的这个功能目前为公共预览版。

如果应用程序以 Java 编写，你可以通过函数应用程序查看更丰富的数据（包括请求、依赖项、日志和指标）。 借助更多数据，你还可以查看并诊断端到端事务，以及查看应用程序映射，该映射聚合了许多事务来显示一个拓扑视图，可以在其中了解各个系统如何交互，以及平均性能和错误率。

端到端诊断和应用程序映射可以洞察每个事务/请求。 将这两个功能结合使用，有助于基于每个请求查找可靠性问题和性能瓶颈的根本原因。

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>如何为 Java 函数应用启用分布式跟踪？

导航到函数应用的“概述”边栏选项卡并转到“配置”。 在“应用程序设置”下，单击“+ 新建应用程序设置”。 

> [!div class="mx-imgBorder"]
> ![在“设置”下添加新的应用程序设置](./media//functions/create-new-setting.png)

使用以下值添加下面的应用程序设置，然后单击左上角的“保存”。 大功告成！

#### <a name="windows"></a>Windows
```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

#### <a name="linux"></a>Linux
```
ApplicationInsightsAgent_EXTENSION_VERSION -> ~3
```

## <a name="distributed-tracing-for-python-function-apps"></a>Python 函数应用的分布式跟踪

若要从 Redis、Memcached、MongoDB 等服务收集自定义遥测数据，可以使用 [OpenCensus Python 扩展](https://github.com/census-ecosystem/opencensus-python-extensions-azure)并[记录遥测数据](https://docs.microsoft.com/azure/azure-functions/functions-reference-python?tabs=azurecli-linux%2Capplication-level#log-custom-telemetry)。 可以在[此处](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib)找到支持的服务的列表。

## <a name="next-steps"></a>后续步骤

* 在[监视 Azure Functions](../../azure-functions/functions-monitoring.md) 中了解有关监视功能的更多说明和信息。
* 查看[分布式跟踪](./distributed-tracing.md)的概述
* 了解[应用程序映射](./app-map.md?tabs=net)对业务起到的作用
* 了解 [Java 应用的请求和依赖项](./java-in-process-agent.md)
* 详细了解 [Azure Monitor](../overview.md) 和 [Application Insights](./app-insights-overview.md)
