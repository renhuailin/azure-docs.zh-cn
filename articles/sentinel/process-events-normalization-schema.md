---
title: Azure Sentinel 进程事件规范化架构参考 | Microsoft Docs
description: 本文介绍 Azure Sentinel 进程事件规范化架构。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/22/2021
ms.author: bagol
ms.openlocfilehash: dd9f0c69b610b54ae6f07661ba15d9f0cf22b3ea
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407202"
---
# <a name="azure-sentinel-process-event-normalization-schema-reference-public-preview"></a>Azure Sentinel 进程事件规范化架构参考（公共预览版）

进程事件规范化架构用于描述运行和终止进程的操作系统活动。 此类事件由操作系统和安全系统报告，例如 EDR（终结点检测和响应）系统。

由 OSSEM 定义的进程是表示程序的运行实例的包含和管理对象。 虽然进程自身不运行，但会管理运行和执行代码的线程。

有关 Azure Sentinel 中的规范化的详细信息，请参阅[规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)。

> [!IMPORTANT]
> 进程事件规范化架构目前以预览版提供。 此功能不附带服务级别协议，不建议将其用于生产工作负荷。
>
> [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="parsers"></a>分析器

Azure Sentinel 提供以下内置的特定于产品的进程事件分析器：

- 使用 Log Analytics 代理或 Azure Monitor 代理收集的“安全事件进程创建（事件 4688）”
- 使用 Log Analytics 代理或 Azure Monitor 代理收集的“安全事件进程终止（事件 4689）”
- 使用 Log Analytics 代理或 Azure Monitor 代理收集的“系统进程创建（事件 1）”
- 使用 Log Analytics 代理或 Azure Monitor 代理收集的“系统进程终止（事件 5）”
- “用于终结点进程创建的 Microsoft 365 Defender”

若要使用统一所有列出的分析器的与源无关的分析器，并确保针对所有配置的源进行分析，请在查询中使用以下表名称：

- “imProcessCreate”，用于需要进程创建信息的查询。 这些查询是最常见的案例。
- “imProcessTerminate”，用于需要进程终止信息的查询。
- “imProcessEvents”，用于需要进程创建和终止信息的查询。 在这种情况下，通过 `EventType` 字段可区分事件，并分别设置为 `ProcessCreate` 或 `ProcessTerminate`。 进程终止事件通常包含的信息比进程创建事件要少得多。

从 [Azure Sentinel GitHub 存储库](https://aka.ms/AzSentinelProcessEvents)部署[与源无关的分析器和特定于源的分析器](normalization-about-parsers.md)。

## <a name="add-your-own-normalized-parsers"></a>添加自己的规范化分析器

为[进程事件](normalization-about-schemas.md#the-process-entity)信息模型实现自定义分析器时，请使用以下语法命名 KQL 函数：`imProcess<Type><vendor><Product>`，其中 `Type` 为 `Create`、`Terminate` 或`Event`（如果分析器同时实现创建和终止事件）。

将 KQL 函数添加到 `imProcess<Type>` 和 `imProcess` 与源无关的分析器，以确保使用[进程事件](normalization-about-schemas.md#the-process-entity)模型的任何内容也使用新的分析器。

## <a name="normalized-content-for-process-activity-data"></a>进程活动数据的规范化内容

以下 Azure Sentinel 内容适用于使用 Azure Sentinel 信息模型规范化的任何进程活动：

- “分析规则”：

   - [可能的 AdFind Recon 工具使用情况（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
   - [Base64 编码 Windows 进程命令行（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
   - [回收站中的恶意软件（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
   - [NOBELIUM - vbscript 的可疑 rundll32.exe 执行（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
   - [SUNBURST 可疑 SolarWinds 子进程（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

   有关详细信息，请参阅[创建自定义分析规则以检测威胁](detect-threats-custom.md)。

- “搜寻查询”：
    - [Cscript 脚本每日摘要明细（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
    - [用户和组的枚举（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_enumeration_user_and_group.yaml)
    - [已添加 Exchange PowerShell 管理单元（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ExchangePowerShellSnapin.yaml)
    - [主机导出邮箱和删除导出（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_HostExportingMailboxAndRemovingExport.yaml)
    - [Invoke-PowerShellTcpOneLine 使用情况（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Invoke-PowerShellTcpOneLine.yaml)
    - [Base64 的 Nishang 反向 TCP Shell（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_NishangReverseTCPShellBase64.yaml)
    - [使用非通用/未记录的命令行开关创建的用户摘要（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_persistence_create_account.yaml)
    - [Powercat 下载（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_PowerCatDownload.yaml)
    - [PowerShell 下载（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_powershell_downloads.yaml)
    - [给定主机的进程熵（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ProcessEntropy.yaml)
    - [SolarWinds 清单（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_SolarWindsInventory.yaml)
    - [使用 Adfind 工具进行可疑枚举（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Suspicious_enumeration_using_adfind.yaml)
    - [Windows 系统关闭/重启（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Windows%20System%20Shutdown-Reboot(T1529).yaml)
    - [Certutil（LOLBin 和 LOLScript，规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Certutil-LOLBins.yaml)
    - [Rundll32（LOLBin 和 LOLScript，规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
    - [非通用进程 - 下 5%（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
    - [命令行中的 Unicode 模糊处理](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)


    有关详细信息，请参阅[通过 Azure Sentinel 搜寻威胁](hunting.md)。

## <a name="schema-details"></a>架构详细信息

进程事件信息模型与 [OSSEM 进程实体架构](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/process.md)相一致。

### <a name="log-analytics-fields"></a>Log Analytics 字段

Log Analytics 针对每条记录生成以下字段，在创建自定义连接器时可以重写这些字段。

| 字段         | 类型     | 讨论 (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>“TimeGenerated” | datetime | 报告设备生成事件的时间。|
| “_ResourceId”   | guid     | 报告设备或服务的 Azure 资源 ID，或使用 Syslog、CEF 或 WEF 转发的事件的日志转发器资源 ID。 |
| **类型** | String | 从中提取记录的原始表。 当同一事件可通过多个通道传入不同的表，并且具有相同的 EventVendor 和 EventProduct 值时，此字段很有用。<br><br>例如，Sysmon 事件可以传入 Event 表或 WindowsEvent 表。 |
| | | |

> [!NOTE]
> Log Analytics 还会添加与安全用例不太相关的其他字段。 有关详细信息，请参阅 [Azure Monitor 日志中的标准列](../azure-monitor/logs/log-standard-columns.md)。
>

## <a name="event-fields"></a>事件字段

事件字段通用于所有架构，描述活动本身和报告设备。

| 字段               | 类       | 类型       |  说明        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 可选    | 字符串     |     一般消息或说明，包含在记录中或者根据记录生成。   |
| “EventCount”          | 必需   | Integer    |     记录描述的事件数。 <br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。 <br><br>对于其他源，设置为 `1`。   |
| **EventStartTime**      | 必需   | 日期/时间  |      如果源支持聚合且记录表示多个事件，则此字段将指定生成第一个事件的时间。 否则，此字段将别名化 [TimeGenerated](#timegenerated) 字段。 |
| **EventEndTime**        | 必需   | Alias      |      [TimeGenerated](#timegenerated) 字段的别名。    |
| **EventType**           | 必需   | Enumerated |    描述记录报告的操作。 <br><br>对于进程记录，支持的值包括： <br>- `ProcessCreated` <br>- `ProcessTerminated` |
| **EventResult**         | 必需   | Enumerated |  描述事件的结果，规范化为以下支持的值之一： <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA`（不适用） <br><br>源可以只提供“EventResultDetails”字段的值，必须分析该字段才能获取“EventResult”值。 <br><br>**注意**：进程事件通常只报告成功。 |
| **EventOriginalUid**    | 可选    | 字符串     |   原始记录的唯一 ID（如果已由源提供）。<br><br>示例： `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| “EventOriginalType”   | 可选    | 字符串     |   原始事件类型或 ID（如果已由源提供）。<br><br>示例： `4688`|
| <a name ="eventproduct"></a>“EventProduct”        | 必需   | 字符串     |             生成事件的产品。 <br><br>示例： `Sysmon`<br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。           |
| **EventProductVersion** | 可选    | 字符串     | 生成事件的产品的版本。 <br><br>示例： `12.1`      |
| **EventVendor**         | 必需   | 字符串     |           生成事件的产品的供应商。 <br><br>示例： `Microsoft`  <br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。  |
| **EventSchemaVersion**  | 必需   | 字符串     |    架构的版本。 此处记录的架构版本为 `0.1`         |
| **EventReportUrl**      | 可选    | 字符串     | 在资源的事件中提供的 URL，提供有关该事件的其他信息。|
| “Dvc” | 必需       | 字符串     |                发生该事件的设备的唯一标识符。 <br><br>此字段可能又称为 [DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确的设备的云源，请使用与 [EventProduct](#eventproduct) 字段相同的值。            |
| <a name ="dvcipaddr"></a>“DvcIpAddr”           | 建议 | IP 地址 |         发生进程事件的设备的 IP 地址。  <br><br>示例： `45.21.42.12`    |
| <a name ="dvchostname"></a>“DvcHostname”         | 建议 | 主机名   |               发生进程事件的设备的主机名。 <br><br>示例： `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>“DvcId”               | 可选    | 字符串     |  发生进程事件的设备的唯一 ID。 <br><br>示例： `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 可选    | MAC        |   发生进程事件的设备的 MAC。  <br><br>示例： `00:1B:44:11:3A:B7`       |
| “DvcOs”               | 可选    | 字符串     |         发生进程事件的设备上运行的操作系统。    <br><br>示例： `Windows`    |
| “DvcOsVersion”        | 可选    | 字符串     |   发生进程事件的设备上的操作系统版本。 <br><br>示例： `10` |
| **AdditionalFields**    | 可选    | 动态    | 如果源提供了值得保留的其他信息，请使用原始字段名称保留这些信息，或者创建动态“AdditionalFields”字段，并在其中以键/值对的形式添加这些额外信息。    |
| | | | |

### <a name="process-event-specific-fields"></a>特定于进程事件的字段

下表中列出的字段特定于进程事件，但类似于其他架构中的字段，并遵循类似的命名约定。

进程事件架构引用以下实体，这些实体是进程创建和终止活动的中心：

- “Actor”。 启动进程创建或终止的用户。
- “ActingProcess”。 Actor 用来启动进程创建或终止的进程。
- “TargetProcess”。 新进程。
- “TargetUser”。 其凭据用于创建新进程的用户。
- “ParentProcess”。 启动 Actor 进程的进程。

| 字段          | 类        | 类型       | 说明   |
|---------------|--------------|------------|-----------------|
| **用户**           | Alias        |            | [TargetUsername](#targetusername) 的别名。 <br><br>示例： `CONTOSO\dadmin`     |
| **处理**        | Alias        |            | [TargetProcessName](#targetprocessname) 的别名 <br><br>示例： `C:\Windows\System32\rundll32.exe`|
| **CommandLine**    | Alias        |            |     [TargetProcessCommandLine](#targetprocesscommandline) 的别名  |
| **哈希**           | Alias        |            |       最佳可用哈希的别名。 |
| <a name="actorusername"></a>“ActorUsername”  | 必需    | 字符串     | 发起事件的用户的用户名。 <br><br>示例： `CONTOSO\WIN-GG82ULGC9GO$`     |
| “ActorUsernameType”              | 必需    | Enumerated |   指定 [ActorUsername](#actorusername) 字段中存储的用户名的类型。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。 <br><br>示例： `Windows`       |
| <a name="actoruserid"></a>“ActorUserId”    | 建议  | 字符串     |   Actor 的唯一 ID。 特定的 ID 取决于生成事件的系统。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。  <br><br>示例： `S-1-5-18`    |
| “ActorUserIdType”| 建议  | 字符串     |  [ActorUserId](#actoruserid) 字段中存储的 ID 的类型。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。 <br><br>示例： `SID`         |
| “ActorSessionId” | 可选     | 字符串     |   Actor 登录会话的唯一 ID。  <br><br>示例： `999`<br><br>注意：类型定义为“字符串”以支持不同的系统，但在 Windows 上，此值必须是数字。 <br><br>如果使用的是 Windows 计算机并使用了其他类型，请务必转换值。 例如，如果使用了十六进制值，请将其转换为十进制值。   |
| “ActingProcessCommandLine”       | 可选     | 字符串     |   用于运行操作进程的命令行。 <br><br>示例： `"choco.exe" -v`    |
| “ActingProcessName”              | 可选     | string     |   操作进程的名称。 此名称通常是从映像或可执行文件派生的，该映像或可执行文件用于定义映射到进程虚拟地址空间的初始代码和数据。<br><br>示例： `C:\Windows\explorer.exe`  |
| “ActingProcessFileCompany”       | 可选     | 字符串     |           创建了操作进程映像文件的公司。  <br><br> 示例： `Microsoft`    |
| “ActingProcessFileDescription”   | 可选     | 字符串     |  嵌入在操作进程映像文件的版本信息中的说明。 <br><br>示例：`Notepad++ : a free (GPL) source code editor` |
| “ActingProcessFileProduct”       | 可选     | 字符串     |操作进程映像文件中的版本信息中的产品名称。 <br><br> 示例： `Notepad++`           |
| “ActingProcessFileVersion”       | 可选     | 字符串     |               操作进程映像文件的版本信息中的产品版本。 <br><br>示例： `7.9.5.0`   |
| “ActingProcessFileInternalName”  | 可选     | 字符串     |      操作进程映像文件的版本信息中的产品内部文件名。 |
| “ActingProcessFileOriginalName” | 可选     | 字符串     |操作进程映像文件的版本信息中的产品原始文件名。       <br><br> 示例：`Notepad++.exe` |
| “ActingProcessIsHidden”          | 可选     | 布尔    |      指示操作进程是否处于隐藏模式。  |
| “ActingProcessInjectedAddress”   | 可选     | 字符串     |      在其中存储负责的操作进程的内存地址。           |
| “ActingProcessId”| 必需    | 字符串        | 操作进程的进程 ID (PID)。<br><br>示例：`48610176`           <br><br>注意：类型定义为“字符串”以支持不同的系统，但在 Windows 和 Linux 上，此值必须是数字。 <br><br>如果使用的是 Windows 或 Linux 计算机并使用了其他类型，请务必转换值。 例如，如果使用了十六进制值，请将其转换为十进制值。    |
| “ActingProcessGuid”              | 可选     | string     |  操作进程的生成的唯一标识符 (GUID)。 可用于跨系统标识进程。  <br><br> 示例： `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| “ActingProcessIntegrityLevel”    | 可选     | 字符串     |       每个进程都有一个在其令牌中表示的完整性级别。 完整性级别确定保护或访问的进程级别。 <br><br> Windows 定义了以下完整性级别：“低”、“中”、“高”和“系统”。    标准用户接收“中”完整性级别，提升的用户接收“高”完整性级别。  <br><br> 有关详细信息，请参阅[必需完整性控制 - Win32 应用](/windows/win32/secauthz/mandatory-integrity-control)。 |
| “ActingProcessMD5”               | 可选     | 字符串     |操作进程映像文件的 MD5 哈希。  <br><br>示例：`75a599802f1fa166cdadb360960b1dd0`|
| “ActingProcessSHA1”              | 可选     | SHA1       | 操作进程映像文件的 SHA-1 哈希。             <br><br>  示例： `d55c5a4df19b46db8c54c801c4665d3338acdab0`  |
| “ActingProcessSHA256”            | 可选     | SHA256     | 操作进程映像文件的 SHA-256 哈希。    <br><br> 示例： <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`   |
| “ActingProcessSHA512”            | 可选     | SHA521     |       操作进程映像文件的 SHA-512 哈希。       |
| “ActingProcessIMPHASH”           | 可选     | 字符串     |       操作进程使用的所有库 DLL 的导入哈希。    |
| “ActingProcessCreationTime”      | 可选     | DateTime   |       操作进程的开始日期和时间。 |
| “ActingProcessTokenElevation”    | 可选     | 字符串     | 指示是否存在应用于操作进程的用户访问控制 (UAC) 特权提升的令牌。   <br><br>示例：`None`|
| “ActingProcessFileSize”          | 可选     | Long       |      运行操作进程的文件的大小。   |
| “ParentProcessName”              | 可选     | string     | 父进程的名称。 此名称通常是从映像或可执行文件派生的，该映像或可执行文件用于定义映射到进程虚拟地址空间的初始代码和数据。<br><br>示例： `C:\Windows\explorer.exe` |
| “ParentProcessFileCompany”       | 可选     | 字符串     |创建了父进程映像文件的公司的名称。            <br><br>    示例：`Microsoft`   |
| “ParentProcessFileDescription”   | 可选     | 字符串     |  父进程映像文件中的版本信息中的说明。    <br><br>示例： `Notepad++ : a free (GPL) source code editor`|
| “ParentProcessFileProduct”       | 可选     | 字符串     |父进程映像文件中的版本信息中的产品名称。    <br><br>  示例：`Notepad++`  |
| “ParentProcessFileVersion”       | 可选     | 字符串     | 父进程映像文件中的版本信息中的产品版本。    <br><br> 示例：`7.9.5.0` |
| “ParentProcessIsHidden”          | 可选     | Boolean    |   指示父进程是否处于隐藏模式。  |
| “ParentProcessInjectedAddress”   | 可选     | 字符串     |    在其中存储负责的父进程的内存地址。           |
| “ParentProcessId”| 必需    | 字符串    | 父进程的进程 ID (PID)。   <br><br>     示例：`48610176`    |
| “ParentProcessGuid”              | 可选     | 字符串     |  父进程的生成的唯一标识符 (GUID)。  可用于跨系统标识进程。    <br><br> 示例： `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |
| “ParentProcessIntegrityLevel”    | 可选     | 字符串     |   每个进程都有一个在其令牌中表示的完整性级别。 完整性级别确定保护或访问的进程级别。 <br><br> Windows 定义了以下完整性级别：“低”、“中”、“高”和“系统”。    标准用户接收“中”完整性级别，提升的用户接收“高”完整性级别。  <br><br> 有关详细信息，请参阅[必需完整性控制 - Win32 应用](/windows/win32/secauthz/mandatory-integrity-control)。 |
| “ParentProcessMD5”               | 可选     | MD5        | 父进程映像文件的 MD5 哈希。  <br><br>示例： `75a599802f1fa166cdadb360960b1dd0`|
| “ParentProcessSHA1”              | 可选     | SHA1       | 父进程映像文件的 SHA-1 哈希。       <br><br> 示例：`d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| “ParentProcessSHA256”            | 可选     | SHA256     |父进程映像文件的 SHA-256 哈希。      <br><br>  示例： <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| “ParentProcessSHA512”            | 可选     | SHA512     |    父进程映像文件的 SHA-512 哈希。       |
| “ParentProcessIMPHASH”           | 可选     | 字符串     |    父进程使用的所有库 DLL 的导入哈希。    |
| “ParentProcessTokenElevation”    | 可选     | 字符串     |指示是否存在应用于父进程的用户访问控制 (UAC) 特权提升的令牌。     <br><br>  示例： `None` |
| “ParentProcessCreationTime”      | 可选    | DateTime   |    父进程的开始日期和时间。 |
| <a name="targetusername"></a>“TargetUserName” | 进程创建事件所必需。 | 字符串     | 目标用户的用户名。  <br><br>示例：`CONTOSO\WIN-GG82ULGC9GO$`      |
| “TargetUsernameType”             | 进程创建事件所必需。   | Enumerated | 指定 [TargetUsername](#targetusername) 字段中存储的用户名的类型。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。          <br><br>  示例：`Windows`        |
|<a name="targetuserid"></a>“TargetUserId”   | 建议 | 字符串     | 目标用户的唯一 ID。 特定的 ID 取决于生成事件的系统。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。            <br><br> 示例： `S-1-5-18`    |
| “TargetUserIdType”               | 建议 | 字符串     | [TargetUserId](#targetuserid) 字段中存储的用户 ID 的类型。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。            <br><br> 示例：`SID`  |
| “TargetUserSessionId”            | 可选     | 字符串     |目标用户的登录会话的唯一 ID。 <br><br>示例： `999`          <br><br>注意：类型定义为“字符串”以支持不同的系统，但在 Windows 上，此值必须是数字。 <br><br>如果使用的是 Windows 或 Linux 计算机并使用了其他类型，请务必转换值。 例如，如果使用了十六进制值，请将其转换为十进制值。     |
| <a name="targetprocessname"></a>“TargetProcessName”              | 必需    | 字符串     |目标进程的名称。 此名称通常是从映像或可执行文件派生的，该映像或可执行文件用于定义映射到进程虚拟地址空间的初始代码和数据。   <br><br>     示例：`C:\Windows\explorer.exe`     |
| “TargetProcessFileCompany”       | 可选     | 字符串     |创建了目标进程映像文件的公司的名称。   <br><br>   示例：`Microsoft` |
| “TargetProcessFileDescription”   | 可选     | 字符串     | 目标进程映像文件中的版本信息中的说明。   <br><br>示例：`Notepad++ : a free (GPL) source code editor` |
| “TargetProcessFileProduct”       | 可选     | 字符串     |目标进程映像文件中的版本信息中的产品名称。  <br><br>  示例： `Notepad++`  |
| “TargetProcessFileSize”          | 可选     | 字符串     |    运行负责事件的进程的文件的大小。 |
| “TargetProcessFileVersion”       | 可选     | 字符串     |目标进程映像文件中的版本信息中的产品版本。   <br><br>  示例： `7.9.5.0` |
| “TargetProcessFileInternalName”  |    可选          | 字符串  |   目标进程的映像文件的版本信息中的产品内部文件名。 |
| “TargetProcessFileOriginalName” |       可选       | 字符串   |   目标进程的映像文件的版本信息中的产品原始文件名。 |
| “TargetProcessIsHidden”          | 可选     | 布尔    |   指示目标进程是否处于隐藏模式。  |
| “TargetProcessInjectedAddress”   | 可选     | 字符串     |    在其中存储负责的目标进程的内存地址。           |
| “TargetProcessMD5”               | 可选     | MD5        | 目标进程映像文件的 MD5 哈希。   <br><br> 示例：`75a599802f1fa166cdadb360960b1dd0`|
| “TargetProcessSHA1”              | 可选     | SHA1       | 目标进程映像文件的 SHA-1 哈希。       <br><br>  示例：`d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| “TargetProcessSHA256”            | 可选     | SHA256     | 目标进程映像文件的 SHA-256 哈希。      <br><br>  示例： <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| TargetProcessSHA512            | 可选     | SHA512     |   目标进程映像文件的 SHA-512 哈希。       |
| “TargetProcessIMPHASH”           | 可选     | 字符串     |    目标进程使用的所有库 DLL 的导入哈希。    |
| <a name="targetprocesscommandline"></a>“TargetProcessCommandLine”       | 必需    | 字符串     | 用于运行目标进程的命令行。   <br><br> 示例：`"choco.exe" -v`  |
| <a name="targetprocesscurrentdirectory"></a>“TargetProcessCurrentDirectory”       | 可选    | 字符串     | 在其中执行目标进程的当前目录。  <br><br> 示例：`c:\windows\system32`  |
| “TargetProcessCreationTime”      | 必需    | DateTime   |    目标进程映像文件的版本信息中的产品版本。   |
| “TargetProcessId”| 必需    | 字符串     |  目标进程的进程 ID (PID)。     <br><br>示例： `48610176`<br><br>注意：类型定义为“字符串”以支持不同的系统，但在 Windows 和 Linux 上，此值必须是数字。 <br><br>如果使用的是 Windows 或 Linux 计算机并使用了其他类型，请务必转换值。 例如，如果使用了十六进制值，请将其转换为十进制值。         |
| “TargetProcessGuid”              | 可选    | 字符串     |目标进程的生成的唯一标识符 (GUID)。 可用于跨系统标识进程。   <br><br>  示例：`EF3BD0BD-2B74-60C5-AF5C-010000001E00`  |
| “TargetProcessIntegrityLevel”    | 可选    | 字符串     |   每个进程都有一个在其令牌中表示的完整性级别。 完整性级别确定保护或访问的进程级别。 <br><br> Windows 定义了以下完整性级别：“低”、“中”、“高”和“系统”。    标准用户接收“中”完整性级别，提升的用户接收“高”完整性级别。  <br><br> 有关详细信息，请参阅[必需完整性控制 - Win32 应用](/windows/win32/secauthz/mandatory-integrity-control)。 |
| “TargetProcessTokenElevation”    | 可选    | 字符串     |指示是否存在应用于所创建或终止的进程的用户访问控制 (UAC) 特权提升的令牌类型。   <br><br>    示例：`None`     |
| | | | |



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Sentinel 中的规范化](normalization.md)
- [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)
- [Azure Sentinel DNS 规范化架构参考](dns-normalization-schema.md)
- [Azure Sentinel 文件事件规范化架构参考（公共预览版）](file-event-normalization-schema.md)
- [Azure Sentinel 网络规范化架构参考](normalization-schema.md)
- [Azure Sentinel 注册表事件规范化架构参考（公共预览版）](registry-event-normalization-schema.md)
