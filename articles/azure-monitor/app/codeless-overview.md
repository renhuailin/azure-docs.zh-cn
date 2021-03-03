---
title: 在不更改代码的情况下监视应用 - Azure Monitor Application Insights 自动检测功能 | Microsoft Docs
description: Azure Monitor Application Insights 自动检测功能概述 - 无代码应用程序性能管理
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 9ead123338a410daf53569ff577dfc8c728a8ddf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708486"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>什么是自动检测或无代码附加 - Azure Monitor Application Insights？

自动检测或无代码附加允许使用 Application Insights 来启用应用程序监视，无需更改代码。  

Application Insights 与各种资源提供程序集成，可在不同的环境中工作。 实质上，你只需启用和-在某些情况下-配置代理，该代理将自动收集遥测数据。 你会立刻在 Application Insights 资源中看到指标、数据和依赖项，因此可以在潜在问题出现之前发现其来源，并通过端到端事务视图分析根本原因。

## <a name="supported-environments-languages-and-resource-providers"></a>支持的环境、语言和资源提供程序

在添加更多的集成时，自动检测功能矩阵变得很复杂。 下表显示了与各种资源提供程序、语言和环境的支持相对应的项目的当前状态。

|环境/资源提供程序          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Windows 上的 Azure 应用服务           | GA，OnBD*       | GA，选择加入      | 正在进行     | 正在进行     | 不支持   |
|Linux 上的 Azure 应用服务             | 空值             | 不支持   | 正在学习     | 公共预览版  | 不支持   |
|AKS 上的 Azure 应用服务               | 空值             | 设计中       | 设计中       | 设计中       | 不支持   |
|Azure Functions - 基本                | GA，OnBD*       | GA，OnBD*       | GA，OnBD*       | GA，OnBD*       | GA，OnBD*       |
|Azure Functions Windows - 依赖项 | 不支持   | 不支持   | 公共预览版  | 不支持   | 不支持   |
|Azure Kubernetes 服务               | 空值             | 设计中       | 通过代理   | 设计中       | 不支持   |
|Azure VM (Windows)                      | 公共预览版  | 不支持   | 不支持   | 不支持   | 不支持   |
|本地 VM (Windows)                | GA，选择加入      | 不支持   | 通过代理   | 不支持   | 不支持   |
|独立代理 - 任何环境            | 不支持   | 不支持   | GA              | 不支持   | 不支持   |

*OnBD 是 On by Default（默认启用）的缩写 - 当你在受支持的环境中部署应用后，系统会自动启用 Application Insights。 

## <a name="azure-app-service"></a>Azure 应用服务

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
Windows 上的 Azure App Service 上的应用程序监视适用于 [.net 应用程序](./azure-web-apps.md?tabs=net) ，并且默认情况下处于启用状态。

#### <a name="netcore"></a>.NETCore
监视 [。](./azure-web-apps.md?tabs=netcore) 只需要单击一次即可启用 NETCore 应用程序。

#### <a name="java"></a>Java
用于监视 Windows 应用服务上的 Java 应用程序的门户集成目前不可用，但你可以在将应用部署到应用服务之前，将 Application Insights [Java 3.0 独立代理](./java-in-process-agent.md) 添加到你的应用程序，而无需进行任何代码更改。 Application Insights Java 3.0 代理现已正式发布。

#### <a name="nodejs"></a>Node.js
当前无法从门户中启用对 Windows 上的 Node.js 应用程序的监视。 若要监视 Node.js 应用程序，请使用 [SDK](./nodejs.md)。

### <a name="linux"></a>Linux

#### <a name="netcore"></a>.NETCore
监视。NETCore 在 Linux 上运行的应用程序，请使用 [SDK](./asp-net-core.md)。

#### <a name="java"></a>Java 
为 Linux 上的应用服务启用 Java 应用程序监视功能不可用，但你可以在将应用部署到应用服务之前，将 [Application Insights Java 3.0 代理](./java-in-process-agent.md) 添加到应用。 Application Insights Java 3.0 代理现已正式发布。

#### <a name="nodejs"></a>Node.js
在[Linux 应用服务中监视 Node.js 应用程序](./azure-web-apps.md?tabs=nodejs)以公共预览版提供，可在 Azure 门户中启用，所有区域均提供此功能。 

#### <a name="python"></a>Python
使用 SDK [监视你的 Python 应用](./opencensus-python.md) 

## <a name="azure-functions"></a>Azure Functions

默认启用对 Azure Functions 的基本监视功能，该功能可以收集日志、性能、错误数据和 HTTP 请求。 对于 Java 应用程序，可以使用分布式跟踪来启用更丰富的监视功能，并获取端到端事务详细信息。 适用于 Java 的此功能为公共预览版，你可以[在 Azure 门户中启用它](./monitor-functions.md)。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

目前可以通过[独立代理](./java-in-process-agent.md)为 Java 应用程序提供 Azure Kubernetes 服务的无代码检测。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows VM 和虚拟机规模集

适用于 Azure Vm 和虚拟机规模集的自动检测适用于 [.net](./azure-vm-vmss-apps.md) 和 [Java](./java-in-process-agent.md)。  

## <a name="on-premises-servers"></a>本地服务器
可以轻松地为[适用于 .NET 应用程序的本地 Windows 服务器](./status-monitor-v2-overview.md)和 [Java 应用](./java-in-process-agent.md)启用监视功能。

## <a name="other-environments"></a>其他环境
通用 Java 独立代理适用于任何环境，无需检测代码。 [按指南](./java-in-process-agent.md)启用 Application Insights 并了解 Java 代理的强大功能。 此代理为公共预览版，在所有区域提供。 

## <a name="next-steps"></a>后续步骤

* [Application Insights 概述](./app-insights-overview.md)
* [应用程序映射](./app-map.md)
* [端到端性能监视](../app/tutorial-performance.md)