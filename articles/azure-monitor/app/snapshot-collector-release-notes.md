---
title: Applicationinsights.config. Microsoft.applicationinsights.snapshotcollector NuGet 包的发行说明-Application Insights
description: Snapshot Debugger Application Insights 使用的 Applicationinsights.config NuGet 包的发行说明。
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: e4fffe0efa9f6900ef8d83c0a1b150b4249c22b3
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008565"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Applicationinsights.config. Microsoft.applicationinsights.snapshotcollector 的发行说明

本文包含适用于 .NET 应用程序的 Applicationinsights.config Microsoft.applicationinsights.snapshotcollector NuGet 包的发行说明，由 Application Insights Snapshot Debugger 使用。

详细[了解](./snapshot-debugger.md).net 应用程序的 Application Insights Snapshot Debugger。

对于 bug 报告和反馈，请在 GitHub 上打开一个问题 https://github.com/microsoft/ApplicationInsights-dotnet

## <a name="release-notes"></a>发行说明

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
用于向后移植修复的单点版本（1.4.0）。
### <a name="bug-fixes"></a>Bug 修复
- [关闭时修复 ObjectDisposedException](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097)。

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
用于解决在测试 Azure App Service 的无代码置备附加方案中发现的问题的点版本。
### <a name="changes"></a>更改
- 现在，netcoreapp 3.0 目标依赖于 AspNetCore >= 2.1.1 (以前 >= 2.1.2) 。

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
解决几个大影响问题的点版本。
### <a name="bug-fixes"></a>Bug 修复
- 修复了 wwwroot/bin 文件夹中的 PDB 发现，此操作在1.3.6 中更改符号搜索算法时断开。
- 修复了遥测数据的 ExtractWasCalledMultipleTimesException。

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>更改
- Microsoft.applicationinsights.snapshotcollector 的 netcoreapp 2.0 目标依赖 Applicationinsights.config >= 2.1.1 () 。 这会将行为恢复为1.3.5 之前的行为。 我们尝试在1.3.6 中对其进行升级，但它破坏了某些 Azure App Service 方案。
### <a name="new-features"></a>新增功能
- Snapshot Collector 从 APPLICATIONINSIGHTS_CONNECTION_STRING 环境变量或从 TelemetryConfiguration 读取 ConnectionString 并对其进行分析。 这主要用于设置用于连接到快照服务的端点。 有关详细信息，请参阅 [连接字符串文档](./sdk-connection-string.md)。
### <a name="bug-fixes"></a>Bug 修复
- 切换为对所有目标使用 HttpClient，但 net45 除外，因为由于不兼容的 SecurityProtocol (需要 TLS 1.2) ，WebRequest 在某些环境中失败。

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>更改
- Microsoft.applicationinsights.snapshotcollector 现在依赖于 Applicationinsights.config >= 2.5.1 用于所有目标框架。 如果你的应用程序依赖于较早版本的 Applicationinsights.config SDK，这可能是一项重大更改。
- 删除快照上载程序中对 TLS 1.0 和1.1 的支持。
- 默认情况下，PDB 扫描时间默认为24小时，而不是15分钟。 可通过 SnapshotCollectorConfiguration 上的 PdbRescanInterval 配置。
- PDB 扫描仅搜索顶级文件夹，而不搜索递归。 如果符号位于 binary 文件夹的子文件夹中，则这可能是一项重大更改。
### <a name="new-features"></a>新增功能
- SnapshotUploader 中的日志循环，以避免用旧文件填充日志文件夹。
- Deoptimization 支持通过 ReJIT on .NET Core 3.0 应用程序的附加) 支持 (。
- 将符号添加到 NuGet 包。
- 上传小型转储时设置其他元数据。
- 向 Itelemetryprocessorfactory 添加了初始化的属性。 这是一个 CancellationToken，当 Snapshot Collector 完全初始化并连接到服务终结点时，此操作将被取消。
- 现在可以为动态生成的方法中的异常捕获快照。 例如，实体框架查询生成的已编译的表达式树。
### <a name="bug-fixes"></a>Bug 修复
- 由于状态监视器，AmbiguousMatchException 加载 Snapshot Collector。
- GetSnapshotCollector 扩展方法现在会搜索所有 TelemetrySinks。
- 不要在不受支持的平台上启动快照上载。
- 当 deoptimizing 动态方法 (例如，实体框架时处理 InvalidOperationException) 

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- 添加对主权云的支持 (较早版本不能在主权云中运行) 
- 通过使用 AddSnapshotCollector ( # A1，增加了快照收集器。 可在[此处](./snapshot-debugger-appservice.md)找到详细信息。
- 使用 FISMA MD5 设置验证 blob 块。 这可以避免使用默认的 .NET MD5 加密算法，当 OS 设置为符合 FIPS 的模式时，该算法不可用。
- Deoptimizing 函数调用时忽略 .NET Framework 的帧。 此行为可以通过 DeoptimizeIgnoredModules 配置设置来控制。
- 添加 `DeoptimizeMethodCount` 允许 deoptimization 多个函数调用的配置设置。 详细信息请参见此处

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- 允许结构化检测密钥。
- 提高 SnapshotUploader 稳定性-即使无法移动旧的上载程序日志也继续启动。
- 如果在 1.3.3) 中禁用 SnapshotUploader.exe 立即退出 (，则重新启用报告其他遥测数据。
- 简化内部遥测。
- _实验性功能_：吸附点收集计划：添加 "snapshotOnFirstOccurence"。 请访问[此处](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)了解更多可用信息。

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- 修复了导致 SnapshotUploader.exe 停止响应的 bug，但未上载 .NET Core 应用的快照。

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _实验性功能_：吸附点收集计划。 请访问[此处](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)了解更多可用信息。
- 当运行时卸载从中加载 Microsoft.applicationinsights.snapshotcollector 的 AppDomain 时，将退出 SnapshotUploader.exe，而不是等待进程退出。 这会在 IIS 中承载时改善收集器的可靠性。
- 添加配置，以允许使用相同检测密钥的多个 Microsoft.applicationinsights.snapshotcollector 实例共享同一 SnapshotUploader 进程： ShareUploaderProcess (默认为 `true`) 。
- SnapshotUploader.exe 立即退出时报告其他遥测数据。
- 减少了 SnapshotUploader.exe 需要写入磁盘的支持文件的数量。

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- 删除支持通过 RtlCloneUserProcess API 收集快照，并且仅支持 PssCaptureSnapshots API。
- 增加在10分钟内可以捕获多少个快照的默认限制，范围为1到3。
- 允许 SnapshotUploader.exe 协商 TLS 1.1 和1。2
- 当 SnapshotUploader 记录警告或错误时报告其他遥测数据
- 当后端服务报告每日配额 50 (每日快照时停止拍摄快照) 
- 在 SnapshotUploader.exe 中添加额外的检查，以不允许同时运行两个实例。

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>更改
- 对于面向 .NET Framework 的应用程序，Snapshot Collector 现在取决于 Applicationinsights.config 版本2.3.0 或更高版本。
它被2.2.0 或更高版本。
我们认为这对于大多数应用程序来说不是一个问题，但如果此更改使你无法使用最新 Snapshot Collector，请告知我们。
- 重试失败的上载时，在快照上载程序中使用指数回退延迟。
- 如果) 可用，请使用 ServerTelemetryChannel (，以便更可靠地报告遥测数据。
- 在与 Snapshot Debugger 服务的初始连接上使用 "SdkInternalOperationsMonitor"，以便依赖项跟踪将其忽略。
- 围绕与 Snapshot Debugger 服务的初始连接，提高遥测。
- 为以下内容报告附加遥测：
  - Azure App Service 版本。
  - Azure 计算实例。
  - 容器。
  - Azure Function app。
### <a name="bug-fixes"></a>Bug 修复
- 如果将问题计数器重置间隔设置为24天，则将其解释为24小时。
- 修复了一个 bug，在释放快照时，如果出现异常，快照上载程序将停止处理新的快照。

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- 修复带有快照上载程序二进制文件的强名称签名。

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>更改
- SnapshotUploader (64) 所需的文件现在作为资源嵌入到主 DLL 中。 这意味着不再创建 SnapshotCollectorFiles 文件夹，从而简化了生成和部署，并减少了解决方案资源管理器的混乱。 升级时请小心，查看文件中的更改 `.csproj` 。 `Microsoft.ApplicationInsights.SnapshotCollector.targets`不再需要此文件。
- 即使 ProvideAnonymousTelemetry 设置为 false，也会将遥测记录到 Application Insights 资源。 这样，我们就可以在 Azure 门户中实现运行状况检查功能。 ProvideAnonymousTelemetry 仅影响发送到 Microsoft 的遥测数据，以进行产品支持和改进。
- 当 TempFolder 或 ShadowCopyFolder 重定向到环境变量时，使收集器保持空闲状态，直到设置这些环境变量。
- 对于通过代理服务器连接到 Internet 的应用程序，Snapshot Collector 现在会自动检测任何代理设置并将其传递到 SnapshotUploader.exe。
- 降低 SnapshotUplaoder 进程的优先级 (尽可能) 。 可以通过 IsLowPrioirtySnapshotUploader 选项覆盖此优先级。
- 在要以编程方式配置 Snapshot Collector 的情况下，在 TelemetryConfiguration 上添加了 GetSnapshotCollector 扩展方法。
- 将 Application Insights SDK 版本设置 (，而不是面向客户的遥测中) 应用程序版本。
- 两分钟后发送首个检测信号事件。
### <a name="bug-fixes"></a>Bug 修复
- 当异常具有 null 或不可变的数据字典时修复 NullReferenceException。
- 如果出现共享冲突，则在上载者中，重试 PDB 匹配几次。
- 如果多个线程在启动时调用遥测管道，则修复重复的遥测。

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>更改
- XML 文档注释文件现在包含在 NuGet 包中。
- 在上添加了一个 ExcludeFromSnapshotting 扩展方法，在 `System.Exception` 这种情况下，你知道存在干扰性异常，并希望避免为其创建快照。
- 添加了 IsEnabledWhenProfiling 配置属性，默认值为 true。 这与以前的版本相比，如果 Application Insights Profiler 执行详细收集，则暂时禁用快照创建。 通过将此属性设置为 false，可以恢复旧行为。
### <a name="bug-fixes"></a>Bug 修复
- 正确签名 SnapshotUploader64.exe。
- 防止遥测处理器的双初始化。
- 防止在具有多个管道的应用中对遥测进行双重日志记录。
- 使用收集计划的过期时间修复 bug，这可能会在24小时后阻止快照。

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
此版本中的最大更改 (因此，移动到新的次要版本号) 会重写快照创建和处理管道。 在以前的版本中，此功能是在本机代码 (ProductionBreakpoints *和 SnapshotHolder*) 中实现的。 新实现是带有 P/Invoke 的所有托管代码。 对于第一个使用新管道的版本，我们没有从原始行为 strayed。 新的实现提供了更好的错误报告功能，使我们能够进一步改进。

### <a name="other-changes-in-this-version"></a>此版本中的其他更改
- 已将 MinidumpUploader.exe 重命名为 SnapshotUploader.exe (或 SnapshotUploader64.exe) 。
- 已将计时遥测添加到 DeOptimize/重新优化请求。
- 添加了用于小型转储的 gzip 压缩。
- 修复了阻止站点升级的 Pdb 锁定的问题。
- 在进行卷影复制时，记录原始文件夹名称 (SnapshotCollectorFiles) 。
- 调整64位进程的内存限制，以防止因 OOM 而重新启动站点。
- 修复即使在禁用后仍收集快照的问题。
- 将检测信号事件记录到客户的 AI 资源。
- 通过删除问题 ID 中的 "源" 来提高快照速度。

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>更改
扩充的使用遥测
- 检测和报告 .NET 版本和 OS
-  (云服务 Service Fabric) 检测和报告其他 Azure 环境
- 记录和报告异常指标 (在检测信号遥测) 的第一次机会异常和 TrackException 调用数。
### <a name="bug-fixes"></a>Bug 修复
- 正确处理 SqlException，其中不引发内部异常 (Win32Exception) 。
- 剪裁符号文件夹上的尾随空格，这会导致 Minidumpuploader.exe 的命令行参数分析不正确。
- 防止对 Snapshot Debugger 代理的终结点的失败连接进行无限次重试。

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>更改
- 添加了主机内存保护。 此功能降低了对主机计算机内存的影响。
- 改善 Azure 门户快照观看体验。