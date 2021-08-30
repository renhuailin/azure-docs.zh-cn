---
title: Azure Application Insights 代理故障排除和已知问题 | Microsoft Docs
description: Application Insights 代理和故障排除已知问题的示例。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b353409ed1cacfabc8ac407e4ad17f4b1e167fe8
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727438"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Application Insights 代理（以前称为状态监视器 v2）故障排除

启用监视时，可能会遇到阻止数据收集的问题。
本文列出了所有已知问题，并提供了故障排除示例。

## <a name="known-issues"></a>已知问题

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>应用的 bin 目录中存在冲突的 Dll

如果 bin 目录中存在以上任何 DLL，则监视可能会失败：

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

其中某些 DLL 包含在 Visual Studio 的默认应用模板中，即使应用不使用它们也是如此。
可以使用故障排除工具来查看症状行为：

- PerfView：
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset 和应用负载（不含遥测数据）。 使用 Sysinternals（Handle.exe 和 ListDLLs.exe）进行调查：
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>PowerShell 版本
此产品是使用 PowerShell v5.1 编写和测试的。
此模块与 PowerShell 版本 6 或 7 不兼容。
我们建议将 PowerShell v5.1 与较新版本一起使用。 有关详细信息，请参阅[将 PowerShell 7 与 PowerShell 5.1 并排使用](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51)。

### <a name="conflict-with-iis-shared-configuration"></a>与 IIS 共享配置冲突

如果拥有 Web 服务器群集，则可能会使用[共享配置](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
HttpModule 无法注入此共享配置。
在每个 Web 服务器上运行 Enable 命令，以将 DLL 安装到每个服务器的 GAC 中。

在运行 Enable 命令后，请完成以下步骤：
1. 转到共享配置目录，并找到 applicationHost.config 文件。
2. 将以下行添加到配置的模块部分中：
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS 嵌套应用程序

在 1.0 版中，我们不在 IIS 中检测嵌套应用程序。

### <a name="advanced-sdk-configuration-isnt-available"></a>高级 SDK 配置不可用。

在 1.0 版中，SDK 配置不向最终用户公开。

    
    
## <a name="troubleshooting"></a>疑难解答
    
### <a name="troubleshooting-powershell"></a>PowerShell 故障排除

#### <a name="determine-which-modules-are-available"></a>确定哪些模块可用
可以使用 `Get-Module -ListAvailable` 命令来确定安装了哪些模块。

#### <a name="import-a-module-into-the-current-session"></a>将模块导入到当前会话中
如果模块尚未加载到 PowerShell 会话中，你可以使用 `Import-Module <path to psd1>` 命令手动加载该模块。


### <a name="troubleshooting-the-application-insights-agent-module"></a>Application Insights 代理模块故障排除

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>列出 Application Insights 代理模块中可用的命令
运行 `Get-Command -Module Az.ApplicationMonitor` 命令来获取可用命令：

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>确定 Application Insights 代理模块的当前版本
运行 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 命令来显示有关该模块的以下信息：
   - PowerShell 模块版本
   - Application Insights SDK 版本
   - PowerShell 模块的文件路径
    
有关如何使用此 cmdlet 的详细说明，请查看 [API 参考](status-monitor-v2-api-reference.md)。


### <a name="troubleshooting-running-processes"></a>对正在运行的进程进行故障排除

可检查已检测的计算机上的进程，确定是否加载了所有 DLL 并设置了环境变量。
如果监视正常工作，则至少应加载 12 个 DLL。

* 使用 `Get-ApplicationInsightsMonitoringStatus -InspectProcess` 命令来检查 DLL。
* 使用 `(Get-Process -id {PID}).StartInfo.EnvironmentVariables` 命令检查环境变量。 以下是在工作进程或 dotnet core 进程中设置的环境变量：

```
COR_ENABLE_PROFILING=1
COR_PROFILER={324F817A-7420-4E6D-B3C1-143FBED6D855}
COR_PROFILER_PATH_32=Path to MicrosoftInstrumentationEngine_x86.dll
COR_PROFILER_PATH_64=Path to MicrosoftInstrumentationEngine_x64.dll
MicrosoftInstrumentationEngine_Host={CA487940-57D2-10BF-11B2-A3AD5A13CBC0}
MicrosoftInstrumentationEngine_HostPath_32=Path to Microsoft.ApplicationInsights.ExtensionsHost_x86.dll
MicrosoftInstrumentationEngine_HostPath_64=Path to Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
MicrosoftInstrumentationEngine_ConfigPath32_Private=Path to Microsoft.InstrumentationEngine.Extensions.config
MicrosoftInstrumentationEngine_ConfigPath64_Private=Path to Microsoft.InstrumentationEngine.Extensions.config
MicrosoftAppInsights_ManagedHttpModulePath=Path to Microsoft.ApplicationInsights.RedfieldIISModule.dll
MicrosoftAppInsights_ManagedHttpModuleType=Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule
ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
DOTNET_STARTUP_HOOKS=Path to Microsoft.ApplicationInsights.StartupHook.dll
```

有关如何使用此 cmdlet 的详细说明，请查看 [API 参考](status-monitor-v2-api-reference.md)。


### <a name="collect-etw-logs-by-using-perfview"></a>使用 PerfView 收集 ETW 日志

#### <a name="setup"></a>设置

1. 从 [GitHub](https://github.com/Microsoft/perfview/releases) 下载 PerfView.exe 和 PerfView64.exe。
2. 启动 PerfView64.exe。
3. 展开“高级选项”。
4. 清除这些复选框：
    - **Zip**
    - **合并**
    - **.NET 符号集合**
5. 设置以下“其他提供程序”：`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,252e28f4-43f9-5771-197a-e8c7e750a984,f9c04365-1d1f-5177-1cdc-a0b0554b6903`


#### <a name="collecting-logs"></a>收集日志

1. 在具有管理员权限的命令控制台中，运行 `iisreset /stop` 命令以禁用 IIS 和所有 Web 应用。
2. 在 PerfView 中，选择“开始收集”。
3. 在具有管理员权限的命令控制台中，运行 `iisreset /start` 命令以启动 IIS。
4. 尝试浏览到你的应用。
5. 在应用加载后，请返回到 PerfView，然后选择“停止收集”。

## <a name="next-steps"></a>后续步骤

- 请查看 [API 参考](status-monitor-v2-overview.md#powershell-api-reference)，以了解你可能错过的参数。
