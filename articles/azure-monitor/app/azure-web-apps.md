---
title: 监视 Azure 应用服务性能 | Microsoft Docs
description: Azure 应用服务的应用程序性能监视。 对加载和响应时间、依赖项信息绘制图表，并针对性能设置警报。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 03ee74851f82fd8ff7833fc5d27c901cf0b2317a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788736"
---
# <a name="application-monitoring-for-azure-app-service-overview"></a>Azure 应用服务的应用程序监视概述

现在，可以比过去更轻松地针对 [Azure 应用服务](../../app-service/index.yml)中运行的基于 ASP.NET、ASP.NET Core、Java 和 Node.js 的 Web 应用启用监视。 以前需要手动检测某个应用，而现在应用服务映像中默认会内置最新的扩展/代理。 

## <a name="enable-application-insights"></a>启用 Application Insights

可通过两种方法为 Azure 应用服务托管的应用程序启用应用程序监视：

- **基于代理的应用程序监视** (ApplicationInsightsAgent)。 
 
    - 此方法最容易实现，无需更改代码或进行高级配置。 这种监视通常称为“运行时”监视。 对于 Azure 应用服务，我们建议至少启用此级别的监视，然后可根据具体的方案，评估是否需要通过手动检测来启用更高级的监视。

    - 以下项支持基于代理的监视：
        - [.NET Core](./azure-web-apps-net-core.md)
        - [.NET](./azure-web-apps-net.md)
        - [Java](./azure-web-apps-java.md)
        - [Nodejs](./azure-web-apps-nodejs.md)
    
* 安装 Application Insights SDK 以 **通过代码手动检测应用程序**。

    * 此方法更具可自定义性，但它需要以下方法：适用于 [.NET Core](./asp-net-core.md)、[.NET](./asp-net.md)、[Node.js](./nodejs.md)、[Python](./opencensus-python.md) 的 SDK，以及适用于 [Java](./java-in-process-agent.md) 的独立代理。 使用此方法还需要自行管理对最新版本的包的更新。

    * 如果需要发出自定义 API 调用来跟踪基于代理的监视在默认情况下不会捕获的事件/依赖项，则需要使用此方法。 有关详细信息，请查看 [自定义事件和指标的 API](./api-custom-events-metrics.md) 一文。 

> [!NOTE]
> 如果同时检测到基于代理的监视和基于 SDK 的手动检测，则在 .NET 中，只会采用手动检测设置，而在 Java 中，只有基于代理的检测将发出遥测。 这是为了防止发送重复数据。

> [!NOTE]
> 快照调试程序和探查器仅适用于 .NET 和 .NET Core

## <a name="next-steps"></a>后续步骤
- 了解如何为在应用服务上运行的 [.NET Core](./azure-web-apps-net-core.md)、[.NET](./azure-web-apps-net.md)、[Java](./azure-web-apps-java.md) 或 [Nodejs](./azure-web-apps-nodejs.md) 应用程序启用基于代理的应用程序监视。 
