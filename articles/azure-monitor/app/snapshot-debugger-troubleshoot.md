---
title: 排查 Azure Application Insights Snapshot Debugger 问题
description: 本文提供故障排除步骤和信息，帮助开发人员启用和使用 Application Insights Snapshot Debugger。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9ae4a7d070419aae974742802ec5b6b3ae958c87
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355527"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> 排查启用 Application Insights Snapshot Debugger 或查看快照时遇到的问题
如果为应用程序启用了 Application Insights Snapshot Debugger，但未看到出现异常的快照，则可以使用以下说明进行故障排除。

可能有许多不同的原因导致未生成快照。 可以先运行快照运行状况检查，确定一些可能的常见原因。

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>请确保使用了适当的 Snapshot Debugger 终结点

目前唯一需要修改终结点的区域是 [Azure 政府](../../azure-government/compare-azure-government-global-azure.md#application-insights)和 [Azure 中国](/azure/china/resources-developer-guide)。

对于使用 Application Insights SDK 的应用服务和应用程序，必须使用如下定义的 Snapshot Debugger 支持的替代来更新连接字符串：

|连接字符串属性    | 美国政府云 | 中国云 |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

若要详细了解其他连接替代，请参阅 [Application Insights 文档](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides)。

对于函数应用，必须使用下面支持的替代来更新 `host.json`：

|属性    | 美国政府云 | 中国云 |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

下面是使用美国政府云代理终结点更新的 `host.json` 示例：
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>使用快照运行状况检查
几个常见问题会导致不显示“打开调试快照”。 例如，使用过时的快照收集器；达到每日上传限制；或者可能快照只是需要很长时间上传。 使用“快照运行状况检查”解决常见问题。

端到端跟踪视图的异常窗格中有一个链接，可将你带到“快照运行状况检查”。

![进入“快照运行状况检查”](./media/snapshot-debugger/enter-snapshot-health-check.png)

这个交互式类似聊天的界面可查找常见问题并指导你解决问题。

![运行状况检查](./media/snapshot-debugger/healthcheck.png)

如果这样不能解决问题，请参阅以下手动故障排除步骤。

## <a name="verify-the-instrumentation-key"></a>验证检测密钥

请确保在发布的应用程序中使用正确的检测密钥。 通常，从 ApplicationInsights.config 文件中读取检测密钥。 请验证该值是否与在门户中看到的 Application Insights 资源的检测密钥相同。

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>检查 TLS/SSL 客户端设置 (ASP.NET)

如果有 ASP.NET 应用程序托管在虚拟机上的 Azure 应用服务或 IIS 中，则应用程序可能因缺少 SSL 安全协议而无法连接到 Snapshot Debugger 服务。

[Snapshot Debugger 终结点需要 TLS 版本 1.2](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json)。 SSL 安全协议集是由 web.config 的 system.web 部分中的 httpRuntime targetFramework 值启用的规定之一。如果 httpRuntime targetFramework 为 4.5.2 或更低版本，则默认不包含 TLS 1.2。

> [!NOTE]
> httpRuntime targetFramework 值与构建应用程序时使用的目标框架无关。

若要检查设置，请打开 web.config 文件，然后找到 system.web 部分。 请确保将 `httpRuntime` 的 `targetFramework` 设置为 4.6 或更高版本。

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> 如果修改 httpRuntime targetFramework 值，则会更改应用于应用程序的运行时规定，并可能导致其他细微的行为变化。 进行此更改后，请务必彻底测试你的应用程序。 有关兼容性更改的完整列表，请参阅[重定目标更改](/dotnet/framework/migration-guide/application-compatibility#retargeting-changes)。

> [!NOTE]
> 如果 targetFramework 为 4.7 或更高版本，则 Windows 将确定可用的协议。 Azure 应用服务中提供了 TLS 1.2。 但是，如果使用的是自己的虚拟机，则可能需要在操作系统中启用 TLS 1.2。

## <a name="preview-versions-of-net-core"></a>.NET Core 预览版
如果你使用的是 .NET Core 预览版，或者应用程序通过依赖程序集直接或间接引用 Application Insights SDK，请按照[为其他环境启用 Snapshot Debugger](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 中的说明进行操作。

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>查看诊断服务站点扩展的“状态”页
如果通过门户中的 [Application Insights 窗格](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)启用了 Snapshot Debugger，则它是由诊断服务站点扩展启用的。

> [!NOTE]
> Application Insights Snapshot Debugger 的无代码安装遵循 .NET Core 支持策略。
> 若要详细了解支持的运行时，请参阅 [.Net Core 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

可以转到以下 url 来查看此扩展的“状态”页：`https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> “状态”页链接的域将因云而异。
此域将与应用服务的 Kudu 管理站点相同。

此“状态”页显示 Profiler 和 Snapshot Collector 代理的安装状态。 如果出现意外错误，则会显示该错误，并显示如何修复此错误。

你可以使用应用服务的 Kudu 管理站点获取此“状态”页的基 url：
1. 在 Azure 门户中，打开应用服务应用程序。
2. 选择“高级工具”或搜索 Kudu 。
3. 选择“转到”。
4. 进入 Kudu 管理站点后，在 URL 中追加以下 `/DiagnosticServices` 并按 Enter。
 最终结果如下所示：`https://<kudu-url>/DiagnosticServices`

它将显示如下“状态”页：![诊断服务状态页](./media/diagnostic-services-site-extension/status-page.png)

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>升级到最新版本的 NuGet 包
根据 Snapshot Debugger 的启用方式，参阅以下选项：

* 如果已通过[门户中的 Application Insights 窗格](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)启用了快照调试器，那么应用程序应该已经在运行最新的 NuGet 包。

* 如果通过包含 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包启用了快照调试器，请使用 Visual Studio 的 NuGet 包管理器确保使用的是最新版本的 Microsoft.ApplicationInsights.SnapshotCollector。

有关最新的更新和 bug 修复，请[参阅发行说明](./snapshot-collector-release-notes.md)。

## <a name="check-the-uploader-logs"></a>检查上传程序日志

创建快照后，将在磁盘上创建一个小型转储文件 (.dmp)。 一个单独的上传程序进程会创建该小型转储文件，并将其连同所有关联的 PDB 一起上传到 Application Insights Snapshot Debugger 存储。 成功上传小型转储后，会将其从磁盘中删除。 上传程序进程的日志文件会保留在磁盘上。 在应用服务环境中，可在 `D:\Home\LogFiles` 中找到这些日志。 通过应用服务的 Kudu 管理站点查找这些日志文件。

1. 在 Azure 门户中，打开应用服务应用程序。
2. 选择“高级工具”或搜索 Kudu 。
3. 选择“转到”。
4. 在“调试控制台”下拉列表框中，选择“CMD”。
5. 选择“LogFiles”。

应至少看到一个名称以 `Uploader_` 或 `SnapshotUploader_` 开头，且扩展名为 `.log` 的文件。 选择相应图标，下载任意日志文件或在浏览器中打开文件。
文件名包括可标识应用服务实例的唯一后缀。 如果应用服务实例托管于多台计算机上，则每台计算机都有单独的日志文件。 当上传程序检测到新的小型转储文件时，会将其记录在日志文件中。 下面是成功的快照和上传的示例：

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> 上面的示例来自 1.2.0 版的 Microsoft.ApplicationInsights.SnapshotCollector NuGet 包。 在更早的版本中，上传程序进程名为 `MinidumpUploader.exe`，且日志不太详细。

在之前的示例中，检测密钥是 `c12a605e73c44346a984e00000000000`。 此值应与应用程序的检测密钥相匹配。
小型转储与 ID 为 `139e411a23934dc0b9ea08a626db16c5` 的快照相关联。 稍后可以使用此 ID 在 Application Insights Analytics 中找到关联的异常记录。

上传程序大约每 15 分钟扫描一次新 PDB。 下面是一个示例：

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

对于未托管于应用服务中的应用程序，上传程序日志与小型转储位于同一文件夹：`%TEMP%\Dumps\<ikey>`（其中 `<ikey>` 是检测密钥）。

## <a name="troubleshooting-cloud-services"></a>云服务故障排除
在云服务中，默认临时文件夹可能太小，无法容纳小型转储文件，从而导致丢失快照。

所需的空间取决于应用程序的总工作集量和并发快照数。

32 位 ASP.NET Web 角色的工作集通常在 200 MB 到 500 MB 之间。 允许存在至少两个并发快照。

例如，如果应用程序使用 1 GB 的总工作集，则应确保是否至少有 2 GB 的磁盘空间可用来存储快照。

按照这些步骤为云服务角色配置快照的专用本地资源。

1. 通过编辑云服务定义 (.csdef) 文件将新的本地资源添加到云服务中。 以下示例使用 5 GB 大小定义名为 `SnapshotStore` 的资源。
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. 修改角色的启动代码以添加指向 `SnapshotStore` 本地资源的环境变量。 对于辅助角色，应将代码添加到角色的 `OnStart` 方法：
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   对于 Web 角色 (ASP.NET)，应将代码添加到 Web 应用程序的 `Application_Start` 方 法：
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. 更新角色的 ApplicationInsights.config 文件以重写 `SnapshotCollector` 使用的临时文件夹位置
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>重写影子副本文件夹

当快照收集器启动时，它会尝试在磁盘上查找适合用于运行快照上传程序进程的文件夹。 所选的文件夹称为影子副本文件夹。

快照收集器检查几个已知位置，并确保它有权复制快照上传程序二进制文件。 使用了以下环境变量：
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

如果找不到合适的文件夹，则 Snapshot Collector 将报告一个错误，指出“找不到合适的影子副本文件夹。”

如果复制失败，则快照收集器会报告一个 `ShadowCopyFailed` 错误。

如果无法启动上传程序，则快照收集器会报告一个 `UploaderCannotStartFromShadowCopy` 错误。 消息的正文通常包含 `System.UnauthorizedAccessException`。 发生此错误通常是因为应用程序正在权限降低的帐户下运行。 此帐户有权向影子副本文件夹进行写入，但无权执行代码。

因为这些错误通常发生在启动期间，所以它们后面通常会跟有一个 `ExceptionDuringConnect` 错误，指出“上传程序无法启动。”

若要解决这些错误，可以通过 `ShadowCopyFolder` 配置选项手动指定影子副本文件夹。 例如，使用 ApplicationInsights.config：

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

或者，如果将 appsettings.json 用于 .NET Core 应用程序，则使用：

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>使用 Application Insights 搜索查找附带快照的异常

创建快照后，出现的异常标记有快照 ID。 向 Application Insights 报告异常时，该快照 ID 作为自定义属性包含在内。 通过 Application Insights 中的“搜索”，可借助 `ai.snapshot.id` 自定义属性找到所有记录。

1. 在 Azure 门户中浏览到 Application Insights 资源。
2. 选择“搜索”。
3. 在“搜索”文本框中输入 `ai.snapshot.id`，然后按 Enter。

![在门户中使用快照 ID 搜索遥测](./media/snapshot-debugger/search-snapshot-portal.png)

如果此搜索未返回任何结果，则表示在选定的时间范围内，未向 Application Insights 报告任何快照。

若要从上传程序日志中搜索特定快照 ID，请在“搜索”框中键入该 ID。 如果快照已上传但找不到该快照的记录，请按照以下步骤操作：

1. 请验证检测密钥，仔细检查正在查看的 Application Insights 资源是否正确。

2. 使用上传程序日志中的时间戳调整搜索的“时间范围”筛选器，以包含该时间范围。

如果仍然看不到任何带有该快照 ID 的异常，则表示未向 Application Insights 报告该异常记录。 如果应用程序在拍摄快照后，但在报告异常记录之前就崩溃，则会发生这种情况。 在这种情况下，请查看 `Diagnose and solve problems` 下的应用服务日志，了解是否存在意外重启或未处理的异常。

## <a name="edit-network-proxy-or-firewall-rules"></a>编辑网络代理或防火墙规则

如果应用程序通过代理或防火墙连接到 Internet，则可能需要更新规则以与 Snapshot Debugger 服务进行通信。

Application Insights Snapshot Debugger 使用的 IP 包含在 Azure Monitor 服务标记中。 有关详细信息，请参阅[服务标记文档](../../virtual-network/service-tags-overview.md)。