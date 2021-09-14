---
title: 在不更改代码的情况下监视应用 - Azure Monitor Application Insights 自动检测功能 | Microsoft Docs
description: Azure Monitor Application Insights 自动检测功能概述 - 无代码应用程序性能管理
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 08/31/2021
ms.reviewer: mbullwin
ms.openlocfilehash: f7193489c35384a9ede10606a9ad335ea69bb974
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434873"
---
# <a name="what-is-auto-instrumentation-for-azure-monitor-application-insights"></a>什么是 Azure Monitor 应用程序洞察自动检测？

自动检测允许使用 Application Insights 来启用应用程序监视，无需更改代码。  

Application Insights 与各种资源提供程序集成，可在不同的环境中工作。 事实上，你只需启用代理并在某些情况下对其进行配置，该代理便可自动收集遥测数据。 你会立刻在 Application Insights 资源中看到指标、请求和依赖项，因此可以在潜在问题出现之前发现其来源，并通过端到端事务视图分析根本原因。

## <a name="supported-environments-languages-and-resource-providers"></a>支持的环境、语言和资源提供程序

随着我们添加新集成，自动检测功能矩阵会变得很复杂。 下表显示了与各种资源提供程序、语言和环境的支持相对应的项目的当前状态。

|环境/资源提供程序          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Windows 上的 Azure 应用服务           | GA，OnBD*       | GA，选择加入      | 公共预览版  | 公共预览版  | 不支持   |
|Linux 上的 Azure 应用服务             | 空值             | 不支持   | GA              | GA              | 不支持   |
|Azure Functions - 基本                | GA，OnBD*       | GA，OnBD*       | GA，OnBD*       | GA，OnBD*       | GA，OnBD*       |
|Azure Functions - 依赖项         | 不支持   | 不支持   | 公共预览版  | 不支持   | 通过[扩展](monitor-functions.md#distributed-tracing-for-python-function-apps)   |
|Azure Spring Cloud                     | 不支持   | 不支持   | 公共预览版  | 不支持   | 不支持   |
|Azure Kubernetes 服务               | 空值             | 不支持   | 通过代理   | 不支持   | 不支持   |
|Azure VM (Windows)                      | 公共预览版  | 不支持   | 通过代理   | 不支持   | 不支持   |
|本地 VM (Windows)                | GA，选择加入      | 不支持   | 通过代理   | 不支持   | 不支持   |
|独立代理 - 任何环境            | 不支持   | 不支持   | GA              | 不支持   | 不支持   |

*OnBD 是 On by Default（默认启用）的缩写 - 当你在受支持的环境中部署应用后，系统会自动启用 Application Insights。 

## <a name="azure-app-service"></a>Azure 应用服务

### <a name="windows"></a>Windows

Windows 上 Azure 应用服务的应用程序监视可用于 [.NET](./azure-web-apps.md?tabs=net)（默认启用）、[.NET Core](./azure-web-apps.md?tabs=netcore)、[Java](./azure-web-apps.md?tabs=java)和 [Node.js](./azure-web-apps.md?tabs=nodejs) 应用程序   。 若要监视 Python 应用，请将 [SDK](./opencensus-python.md) 添加到代码。

> [!NOTE]
> 对于 Windows，应用程序监视目前在应用服务中可用于基于代码的服务/托管的服务。 尚不支持通过与 Application Insights 的集成来监视应用服务上的 Windows 容器上的应用。

### <a name="linux"></a>Linux
可以通过门户在应用服务中为 Linux 上运行的 [Java](./azure-web-apps.md?tabs=java) 和 [Node.js](./azure-web-apps.md?tabs=nodejs) 应用启用监视，这两种语言的体验均为正式发布版本，在所有区域中都可用 。 

对于其他语言（[.Net Core](./asp-net-core.md) 和 [Python](./opencensus-python.md)），请使用 SDK。

## <a name="azure-functions"></a>Azure Functions

默认启用对 Azure Functions 的基本监视功能，该功能可以收集日志、性能、错误数据和 HTTP 请求。 对于 Java 应用程序，可以使用分布式跟踪来启用更丰富的监视功能，并获取端到端事务详细信息。 适用于 Java 的此功能在 Windows 中为公共预览版，你可以[在 Azure 门户中启用它](./monitor-functions.md)。

## <a name="azure-spring-cloud"></a>Azure Spring Cloud

### <a name="java"></a>Java 
在 Azure Spring Cloud 中运行的 Java 应用的应用程序监视已集成到门户中。对于现有的和新建的 Azure Spring Cloud 资源，你都可以直接从 Azure 门户启用 Application Insights。  

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

目前可以通过[独立代理](./java-in-process-agent.md)为 Java 应用程序提供 Azure Kubernetes 服务的无代码检测。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows VM 和虚拟机规模集

Azure VM 和虚拟机规模集的自动检测适用于 [.NET](./azure-vm-vmss-apps.md) 和 [Java](./java-in-process-agent.md) - 此体验未集成到门户中。 使用独立解决方案，只需几步便可启用监视，无需更改任何代码。  

## <a name="on-premises-servers"></a>本地服务器
可以轻松地为[适用于 .NET 应用程序的本地 Windows 服务器](./status-monitor-v2-overview.md)和 [Java 应用](./java-in-process-agent.md)启用监视功能。

## <a name="other-environments"></a>其他环境
通用 Java 独立代理适用于任何环境，无需检测代码。 [按指南](./java-in-process-agent.md)启用 Application Insights 并了解 Java 代理的强大功能。 此代理为公共预览版，在所有区域提供。 

## <a name="next-steps"></a>后续步骤

* [Application Insights 概述](./app-insights-overview.md)
* [应用程序映射](./app-map.md)
* [端到端性能监视](../app/tutorial-performance.md)
