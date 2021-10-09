---
title: Microsoft.ApplicationInsights.SnapshotCollector NuGet 包 - Application Insights 的发行说明
description: Application Insights Snapshot Debugger 使用的 Microsoft.ApplicationInsights.SnapshotCollector NuGet 包的发行说明。
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: a2b3dff6331e235d0c4b608a38038a89996c35b4
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217762"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Microsoft.ApplicationInsights.SnapshotCollector 的发行说明

本文包含适用于 .NET 应用程序的 Microsoft.ApplicationInsights.SnapshotCollector NuGet 包（由 Application Insights Snapshot Debugger 使用）的发行说明。

[详细了解](./snapshot-debugger.md)适用于 .NET 应用程序的 Application Insights Snapshot Debugger。

对于 bug 报告和反馈，请在 GitHub 上通过 https://github.com/microsoft/ApplicationInsights-SnapshotCollector 提出问题

## <a name="release-notes"></a>发行说明

## <a name="142"></a>[1.4.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.2)
用于解决用户报告的 bug 的单点版本。
### <a name="bug-fixes"></a>Bug 修复
- 修复 [ArgumentException：委托必须是同一类型。](https://github.com/microsoft/ApplicationInsights-SnapshotCollector/issues/16)

## <a name="141"></a>[1.4.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.1)
用于还原 1.4.0 中引入的中断性变更的单点版本。
### <a name="bug-fixes"></a>Bug 修复
- 修复[在 WebJobs 中找不到方法](https://github.com/microsoft/ApplicationInsights-SnapshotCollector/issues/15)

## <a name="140"></a>[1.4.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.0)
解决了多项改进，并且针对 Application Insights 引入添加了对 Azure Active Directory (AAD) 身份验证的支持。
### <a name="changes"></a>更改
- Snapshot Collector 包的大小减小了 60%。 从 10.34 MB 减小到了 4.11 MB。
- 在 Snapshot Collector 中只针对目标 netstandard2.0。
- 将 Application Insights SDK 依赖项升级到 2.15.0。
- 在写入转储时添加回 MinidumpWithThreadInfo。
- 添加了 CompatibilityVersion，以改进有关中断性变更的 Snapshot Collector 代理和上传程序之间的同步。
- 更改 SnapshotUploader LogFile 命名算法，以避免应用服务中文件 I/O 过多。
- 将 pid、角色名称和进程开始时间添加到上传的 Blob 元数据。
- 在 Snapshot Collector 和 Snapshot Uploader 中请尽可能使用 System.Diagnostics.Process。
### <a name="new-features"></a>新增功能
- 为 SnapshotCollector 添加了 Azure Active Directory 身份验证。 请在[此处](./azure-ad-authentication.md)详细了解 Application Insights 中的 Azure AD 身份验证。

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
用于从 1.4.0-pre 版本向后移植修补程序的若干修正发布。
### <a name="bug-fixes"></a>Bug 修复
- 修复[关闭时的 ObjectDisposedException](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097)。

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
用于解决在测试 Azure 应用服务的无代码附加方案时发现的问题的若干修正发布。
### <a name="changes"></a>更改
- netcoreapp3.0 目标现在依赖于 Microsoft.ApplicationInsights.AspNetCore > = 2.1.1（以前是 > = 2.1.2）。

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
用于解决几个具有重大影响的问题的若干修正发布。
### <a name="bug-fixes"></a>Bug 修复
- 修复了 wwwroot/bin 文件夹中的 PDB 发现，此发现在 1.3.6 中更改符号搜索算法时损坏。
- 修复了遥测中的干扰性 ExtractWasCalledMultipleTimesException。

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>更改
- SnapshotCollector 的 netcoreapp2.0 目标依赖 Microsoft.ApplicationInsights.AspNetCore >= 2.1.1（再一次）。 这会将行为恢复为 1.3.5 之前的行为。 我们尝试在 1.3.6 中对其进行升级，但它破坏了某些 Azure 应用服务场景。
### <a name="new-features"></a>新增功能
- Snapshot Collector 从 APPLICATIONINSIGHTS_CONNECTION_STRING 环境变量或 TelemetryConfiguration 读取和分析 ConnectionString。 这主要用于设置用于连接到快照服务的终结点。 有关详细信息，请参阅[连接字符串文档](./sdk-connection-string.md)。
### <a name="bug-fixes"></a>Bug 修复
- 切换为对所有目标使用 HttpClient，但 net45 除外，因为在某些环境中由于不兼容的 SecurityProtocol（需要 TLS 1.2）导致 WebRequest 失败。

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>更改
- 对于所有目标框架，SnapshotCollector 现在依赖 Microsoft.ApplicationInsights >= 2.5.1。 如果应用程序依赖较早版本的 Microsoft.ApplicationInsights SDK，这可能是一项中断性变更。
- 删除快照上传程序中对 TLS 1.0 和 1.1 的支持。
- 现在，PDB 扫描时间默认为 24 小时，而不是 15 分钟。 可通过 SnapshotCollectorConfiguration 上的 PdbRescanInterval 配置。
- PDB 扫描仅搜索顶级文件夹，而不执行递归搜索。 如果符号位于二进制文件文件夹的子文件夹中，则这可能是一项中断性变更。
### <a name="new-features"></a>新增功能
- SnapshotUploader 中的日志循环，用于避免用旧文件填充日志文件夹。
- .NET Core 3.0 应用程序的 Deoptimization 支持（通过 ReJIT 进行附加）。
- 将符号添加到 NuGet 包。
- 上传小型转储时设置其他元数据。
- 向 SnapshotCollectorTelemetryProcessor 添加了初始化的属性。 它是一个 CancellationToken，当 Snapshot Collector 完全初始化并连接到服务终结点时，它将被取消。
- 现在可以为动态生成的方法中的异常捕获快照。 例如，实体框架查询生成的已编译的表达式树。
### <a name="bug-fixes"></a>Bug 修复
- 由于状态监视器，AmbiguousMatchException 加载 Snapshot Collector。
- GetSnapshotCollector 扩展方法现在会搜索所有 TelemetrySinks。
- 请勿在不受支持的平台上启动 Snapshot Uploader。
- 取消优化动态方法（例如实体框架）时处理 InvalidOperationException

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- 添加对主权云的支持（较旧版本不适用于主权云）
- 通过使用 AddSnapshotCollector()，可以更轻松地添加快照收集器。 可在[此处](./snapshot-debugger-appservice.md)找到详细信息。
- 使用 FISMA MD5 设置验证 blob 块。 这可以避免使用默认的 .NET MD5 加密算法，当 OS 设置为符合 FIPS 的模式时，该算法不可用。
- 取消优化函数调用时忽略 .NET Framework 框架。 此行为可以通过 DeoptimizeIgnoredModules 配置设置来控制。
- 添加 `DeoptimizeMethodCount` 配置设置，该设置支持取消优化多个函数调用。 单击此处查看详细信息

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- 允许结构化检测密钥。
- 提高 SnapshotUploader 可靠性 - 即使旧的上传程序日志无法移动，也能继续启动。
- 当 SnapshotUploader.exe 立即退出时，重新启用报告其他遥测（1.3.3 中已禁用）。
- 简化内部遥测。
- _试验性功能_：快照集合计划：添加“snapshotOnFirstOccurence”。 请访问[此处](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)了解更多可用信息。

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- 修复了导致以下问题的 bug：SnapshotUploader.exe 停止响应且不上传 .NET Core 应用的快照。

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _试验性功能_：快照集合计划。 请访问[此处](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)了解更多可用信息。
- 当运行时上传 AppDomain（SnapshotCollector 从 AppDomain 加载）时，SnapshotUploader.exe 将退出，而不是等待进程退出。 这会在 IIS 中托管时改善收集器的可靠性。
- 添加配置，以允许使用相同检测密钥的多个 SnapshotCollector 实例共享同一 SnapshotUploader 进程：ShareUploaderProcess（默认为 `true`）。
- 当 SnapshotUploader.exe 立即退出时，报告其他遥测。
- 减少了 SnapshotUploader.exe 写入磁盘所需的支持文件的数量。

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- 删除对通过 RtlCloneUserProcess API 收集快照的支持，仅支持 PssCaptureSnapshots API。
- 提高在 10 分钟内可以捕获的快照数量的默认上限，从 1 增加到 3。
- 允许 SnapshotUploader.exe 协商 TLS 1.1 和 1.2
- 当 SnapshotUploader 记录警告或错误时报告其他遥测
- 当后端服务报告达到每日配额时停止拍摄快照（每天 50 个快照）
- 在 SnapshotUploader.exe 中添加额外的检查，以禁止同时运行两个实例。

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>更改
- 对于面向 .NET Framework 的应用程序，Snapshot Collector 现在依赖 Microsoft.ApplicationInsights 版本 2.3.0 或更高版本。
它以前依赖 2.2.0 或更高版本。
我们认为这对于大多数应用程序来说不是问题，但如果此更改使你无法使用最新的 Snapshot Collector，请告知我们。
- 重试失败的上传时，在 Snapshot Uploader 中使用指数回退延迟。
- 使用 ServerTelemetryChannel（如果可用）来更可靠地报告遥测。
- 在与 Snapshot Debugger 服务的初始连接上使用“SdkInternalOperationsMonitor”，使依赖项跟踪将其忽略。
- 围绕 Snapshot Debugger 服务的初始连接改进遥测。
- 报告以下项的其他遥测：
  - Azure 应用服务版本。
  - Azure 计算实例。
  - 容器。
  - Azure 函数应用。
### <a name="bug-fixes"></a>Bug 修复
- 如果问题计数器重置间隔设置为 24 天，则将其理解为 24 小时。
- 修复了以下 bug：在处置快照时，如果出现异常，Snapshot Uploader 将停止处理新的快照。

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- 修复 Snapshot Uploader 二进制文件的强名称签名。

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>更改
- SnapshotUploader(64).exe 所需的文件现在作为资源嵌入到主 DLL 中。 这意味着不再创建 SnapshotCollectorFiles 文件夹，从而可简化生成和部署，并减少解决方案资源管理器的混乱。 升级时仔细查看 `.csproj` 文件中的更改。 不再需要 `Microsoft.ApplicationInsights.SnapshotCollector.targets` 文件。
- 即使 ProvideAnonymousTelemetry 设置为 false，遥测也会记录到 Application Insights 资源。 这样，我们就可以在 Azure 门户中实现运行状况检查功能。 ProvideAnonymousTelemetry 仅影响发送到 Microsoft 用于产品支持和改进的遥测数据。
- 当 TempFolder 或 ShadowCopyFolder 重定向到环境变量时，使收集器保持空闲状态，直到设置好这些环境变量。
- 对于通过代理服务器连接到 Internet 的应用程序，Snapshot Collector 现在会自动检测任何代理设置并将其传递给 SnapshotUploader.exe。
- 降低 SnapshotUplaoder 进程的优先级（如果可能）。 可以通过 IsLowPrioirtySnapshotUploader 选项覆盖此优先级。
- 对于需要以编程方式配置 Snapshot Collector 的情况，在 TelemetryConfiguration 上添加了 GetSnapshotCollector 扩展方法。
- 在面向客户的遥测中设置 Application Insights SDK 版本（而不是应用程序版本）。
- 两分钟后发送首个检测信号事件。
### <a name="bug-fixes"></a>Bug 修复
- 修复当异常具有 null 或不可变的数据字典时的 NullReferenceException。
- 如果出现共享冲突，则在上传程序中重试 PDB 匹配几次。
- 修复启动时多个线程调用到遥测管道时的重复遥测。

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>更改
- XML 文档注释文件现在包含在 NuGet 包中。
- 在 `System.Exception` 上添加了一个 ExcludeFromSnapshotting 扩展方法，以应对遇到干扰性异常并希望避免为其创建快照的情况。
- 添加了 IsEnabledWhenProfiling 配置属性，默认值为 true。 这与以前的版本不同，在以前的版本中，如果 Application Insights Profiler 执行详细收集，则暂时禁用快照创建。 通过将此属性设置为 false，可以恢复旧行为。
### <a name="bug-fixes"></a>Bug 修复
- 对 SnapshotUploader64.exe 正确签名。
- 防止遥测处理器的双重初始化。
- 防止在具有多个管道的应用中对遥测进行双重日志记录。
- 修复收集计划的到期时间的 bug，此 bug 可能会在 24 小时后阻止快照。

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
此版本中的最大变化（因此更改为新的次要版本号）是重写快照创建和处理管道。 在以前的版本中，此功能是在本机代码中实现的（.dll 和 SnapshotHolder.exe）。 新实现是带有 P/Invoke 的完全托管代码。 对于这第一个使用新管道的版本，我们没有对原始行为进行太大的改动。 新的实现可提供更好的错误报告功能，使我们能够进一步改进。

### <a name="other-changes-in-this-version"></a>此版本中的其他更改
- MinidumpUploader.exe 已重命名为 SnapshotUploader.exe（或 SnapshotUploader64.exe）。
- 向 DeOptimize/ReOptimize 请求添加了计时遥测。
- 添加了用于小型转储上传的 gzip 压缩。
- 修复了阻止站点升级的 PDB 被锁定的问题。
- 在进行卷影复制时记录原始文件夹名称 (SnapshotCollectorFiles)。
- 调整 64 位进程的内存限制，以防止因 OOM 而重启站点。
- 修复以下问题：即使禁用后仍收集快照。
- 将检测信号事件记录到客户的 AI 资源。
- 通过删除问题 ID 中的“源”来提高快照速度。

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>更改
增强的使用遥测
- 检测和报告 .NET 版本和 OS
- 检测和报告其他 Azure 环境（云服务、Service Fabric）
- 记录和报告检测信号遥测中的异常指标（第一次机会异常数量和 TrackException 调用次数）。
### <a name="bug-fixes"></a>Bug 修复
- 不引发内部异常 (Win32Exception) 的 SqlException 的正确处理。
- 剪裁符号文件夹上的尾随空格，这会导致 MinidumpUploader 的命令行参数分析不正确。
- 防止对 Snapshot Debugger 代理终结点的失败连接进行无限次重试。

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>更改
- 添加了主机内存保护。 此功能降低了对主机内存的影响。
- 改善 Azure 门户快照查看体验。
