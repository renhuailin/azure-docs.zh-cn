---
title: Azure Application Insights 代理概述 | Microsoft Docs
description: Application Insights 代理的概述。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 1fcdca5aaedb9d3b0e86e02e931eb7e38c16cc25
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114731849"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>为本地服务器部署 Azure Monitor Application Insights 代理

> [!IMPORTANT]
> 对于 Application Insights 代理的本地和非 Azure 云部署，建议使用本指南。 下面是建议用于 [Azure 虚拟机和虚拟机规模集部署](./azure-vm-vmss-apps.md)的方法。

Application Insights 代理（以前称为状态监视器 V2）是发布到 [PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)的 PowerShell 模块。
它将替换状态监视器。
遥测数据将发送到 Azure 门户，你可以在其中[监视](./app-insights-overview.md)应用。

> [!NOTE]
> 本模块当前支持通过 IIS 托管的 .NET 和 .NET Core web 应用的无代码检测。 使用 SDK 检测 Java 和 Node.js 应用程序。

## <a name="powershell-gallery"></a>PowerShell 库

Application Insights 代理位于此处：https://www.powershellgallery.com/packages/Az.ApplicationMonitor。

![PowerShell 库](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>说明
- 请参阅[入门说明](status-monitor-v2-get-started.md)，从简明的代码示例开始学习。
- 请参阅[详细说明](status-monitor-v2-detailed-instructions.md)，以深入了解如何开始使用。

## <a name="powershell-api-reference"></a>PowerShell API 参考
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>疑难解答
- [故障排除](status-monitor-v2-troubleshoot.md)
- [已知问题](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常见问题解答

- Application Insights 代理是否支持代理安装？

  *是* 可以通过多种方式下载 Application Insights 代理。 如果计算机可以访问 Internet，则可以使用 `-Proxy` 参数登录到 PowerShell 库。
还可以手动下载此模块，并将其安装到计算机上或直接使用它。
上述每个选项都在[详细说明](status-monitor-v2-detailed-instructions.md)中进行了说明。

- 状态监视器 v2 是否支持 ASP.NET Core 应用程序？

  *是* 从 [Application Insights Agent 2.0.0-beta1](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/2.0.0-beta1) 开始，支持在 IIS 中托管的 ASP.NET Core 应用程序。

- 如何验证启用是否成功？

  - [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) cmdlet 可用于验证启用是否成功。
  - 我们建议你使用[实时指标](./live-stream.md)来快速确定应用是否正在发送遥测数据。

  - 还可以使用 [Log Analytics](../logs/log-analytics-tutorial.md) 列出当前正在发送遥测数据的所有云角色：
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>后续步骤

查看遥测：

* [浏览指标](../essentials/metrics-charts.md)，以便监视性能和使用情况。
* [搜索事件和日志](./diagnostic-search.md)以诊断问题。
* [使用分析](../logs/log-query-overview.md)，以便进行更高级的查询。
* [创建仪表板](./overview-dashboard.md)。

添加更多遥测：

* [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
* [添加 Web 客户端遥测](./javascript.md)，以查看网页代码中的异常并启用跟踪调用。
* [将 Application Insights SDK 添加到代码](./asp-net.md)，以便插入跟踪和日志调用。
