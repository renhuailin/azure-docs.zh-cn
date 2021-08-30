---
title: Azure Application Insights 代理 - 入门 | Microsoft Docs
description: Application Insights 代理的快速入门指南。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 01/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2918a9dc1bbf514b6ab915d9e1d691b9ae4f95d3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729334"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>本地服务器的 Azure Monitor Application Insights 代理入门

本文包含应适用于大多数环境的快速入门命令。
这些说明依赖于 PowerShell 库来分发更新。
这些命令支持 PowerShell `-Proxy` 参数。

有关这些命令的说明、自定义说明以及有关故障排除的信息，请参阅[详细说明](status-monitor-v2-detailed-instructions.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="download-and-install-via-powershell-gallery"></a>通过 PowerShell 库下载并安装

### <a name="install-prerequisites"></a>安装先决条件

- 若要启用监视，你需要一个连接字符串。 连接字符串显示在 Application Insights 资源的“概述”边栏选项卡上。 有关详细信息，请参阅[连接字符串](./sdk-connection-string.md?tabs=net#finding-my-connection-string)页。

> [!NOTE]
> 从 2020 年 4 月起，PowerShell 库已弃用了 TLS 1.1 和 TLS 1.0。
>
> 有关可能需要的其他先决条件，请参阅 [PowerShell 库 TLS 支持](https://devblogs.microsoft.com/powershell/powershell-gallery-tls-support)。
>

以管理员身份运行 PowerShell。
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
关闭 PowerShell。

### <a name="install-application-insights-agent"></a>安装 Application Insights 代理
以管理员身份运行 PowerShell。
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

> [!NOTE]
> 可通过 `Install-Module` cmdlet 中的 `AllowPrerelease` 开关安装 beta 版本。 
>
> 有关其他信息，请参阅[安装模块](/powershell/module/powershellget/install-module?view=powershell-7.1#parameters)。
>

### <a name="enable-monitoring"></a>启用监视

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -ConnectionString 'InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```
    
        
## <a name="download-and-install-manually-offline-option"></a>手动下载并安装（脱机选项）
### <a name="download-the-module"></a>下载模块
从 [PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)手动下载最新版本的模块。

### <a name="unzip-and-install-application-insights-agent"></a>解压缩并安装 Application Insights 代理
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>启用监视

```powershell
Enable-ApplicationInsightsMonitoring -ConnectionString 'InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```




## <a name="next-steps"></a>后续步骤

 查看遥测：

- [浏览指标](../essentials/metrics-charts.md)，以便监视性能和使用情况。
- [搜索事件和日志](./diagnostic-search.md)以诊断问题。
- [使用分析](../logs/log-query-overview.md)，以便进行更高级的查询。
- [创建仪表板](./overview-dashboard.md)。

 添加更多遥测：

- [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
- [添加 Web 客户端遥测](./javascript.md)，以查看网页代码中的异常并启用跟踪调用。
- [将 Application Insights SDK 添加到代码](./asp-net.md)，以便插入跟踪和日志调用。

使用 Application Insights 代理执行更多操作：

- 有关此处找到的命令的说明，请查看[详细说明](status-monitor-v2-detailed-instructions.md)。
- 使用我们的指南对 Application Insights 代理进行[故障排除](status-monitor-v2-troubleshoot.md)。