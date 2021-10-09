---
title: Azure Sentinel 文件事件规范化架构参考 | Microsoft Docs
description: 本文介绍 Azure Sentinel 文件事件规范化架构。
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
ms.openlocfilehash: d5928cf93aed6e1a887be07f2befd27df9a8e276
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407829"
---
# <a name="azure-sentinel-file-event-normalization-schema-reference-public-preview"></a>Azure Sentinel 文件事件规范化架构参考（公共预览版）

文件事件规范化架构用于描述创建、修改或者删除文件或文档等文件活动。 此类事件由操作系统、文件存储系统（例如 Azure 文件存储）和文档管理系统（例如 Microsoft SharePoint）报告。

有关 Azure Sentinel 中的规范化的详细信息，请参阅[规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)。

> [!IMPORTANT]
> 文件事件规范化架构目前以预览版提供。 此功能不附带服务级别协议，不建议将其用于生产工作负荷。
>
> [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="parsers"></a>分析器

Azure Sentinel 提供以下内置的特定于产品的文件事件分析器：

- 使用 Log Analytics 代理或 Azure Monitor 代理收集的 Sysmon 文件活动事件（事件 11、23 和 26）。
- 使用 Office 活动连接器收集的 Microsoft Office 365 SharePoint 和 OneDrive 事件。
- Microsoft 365 Defender for Endpoints 文件事件
- Azure 存储，包括 Blob、文件、队列和表存储。

若要使用统一了所有内置分析器的与源无关的分析器，并确保针对所有配置的源中运行分析，请在查询中使用 imFileEvent 作为表名称。

从 [Azure Sentinel GitHub 存储库](https://aka.ms/AzSentinelFileEvent)部署[与源无关的分析器和特定于源的分析器](normalization-about-parsers.md)。

## <a name="add-your-own-normalized-parsers"></a>添加自己的规范化分析器


为文件事件信息模型实现自定义分析器时，请使用以下语法来命名 KQL 函数：`imFileEvent<vendor><Product`。

将 KQL 函数添加到与源无关的分析器 `imFileEvent`，以确保使用文件事件模型的任何内容也使用你的新分析器。

## <a name="normalized-content-for-file-activity-data"></a>文件活动数据的规范化内容

对文件活动 ASIM 架构的支持还包括对以下内置分析规则和规范化文件活动分析程序的支持。 虽然下面提供了 Azure Sentinel GitHub 存储库的链接作为参考，但也可在 [Azure Sentinel Analytics 规则库](detect-threats-built-in.md)中找到这些规则。 使用链接的 GitHub 页复制所列规则的任何相关搜寻查询。


- [SUNBURST 和 SUPERNOVA 后门程序哈希（规范化文件事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [2021 年 3 月 IoC 匹配中披露的 Exchange Server 漏洞](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [HAFNIUM UM 服务写入可疑文件](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM - 域、哈希和 IP IOC - 2021 年 5 月](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [SUNSPOT 日志文件创建](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [已知的 ZINC Comebacker 和 Klackring 恶意软件哈希](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

有关详细信息，请参阅[创建自定义分析规则以检测威胁](detect-threats-custom.md)。


## <a name="schema-details"></a>架构详细信息

文件事件信息模型与 [OSSEM 进程实体架构](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/file.md)相一致。

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

| 字段               | 类       | 类型       |  说明       |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 可选    | 字符串     |     一般消息或说明，包含在记录中或者根据记录生成。   |
| “EventCount”          | 必需   | Integer    |     记录描述的事件数。 <br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。 <br><br>对于其他源，设置为 `1`。   |
| **EventStartTime**      | 必需   | 日期/时间  |      如果源支持聚合且记录表示多个事件，则此字段将指定生成第一个事件的时间。 否则，此字段将别名化 [TimeGenerated](#timegenerated) 字段。 |
| **EventEndTime**        | 必需   | Alias      |      [TimeGenerated](#timegenerated) 字段的别名。    |
| **EventType**           | 必需   | Enumerated |    描述记录报告的操作。 <br><br>对于文件记录，支持的值包括： <br><br>- `FileCreated`<br>- `FileModified`<br>- `FileDeleted`<br>- `FileRenamed`<br>- `FileCopied`<br>- `FileMoved`<br>- `FolderCreated`<br>- `FolderDeleted` |
| **EventResult**         | 必需   | Enumerated |  描述事件的结果，规范化为以下支持的值之一： <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA`（不适用） <br><br>源可以只提供 [EventOriginalResultDetails](#eventoriginalresultdetails) 字段的值，必须分析该字段才能获取 EventResult 值。 |
| <a name="eventoriginalresultdetails"></a>EventOriginalResultDetails  | 可选    | 字符串     | 描述事件的结果。 <br><br>**注意**：此值未规范化，旨在用作数据源提供的原始值。 文件事件规范化架构目前没有相关的规范化字段，如 EventResultDetails。 |
| **EventOriginalUid**    | 可选    | 字符串     | 原始记录的唯一 ID（如果已由源提供）。<br><br>示例： `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| “EventOriginalType”   | 可选    | 字符串     | 原始事件类型或 ID（如果已由源提供）。<br><br>示例： `4663`|
| <a name ="eventproduct"></a>“EventProduct”       | 必需   | 字符串     | 生成事件的产品。 <br><br>示例： `Sysmon`<br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。           |
| **EventProductVersion** | 可选    | 字符串     | 生成事件的产品的版本。 <br><br>示例： `12.1`<br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。           |
| **EventVendor**         | 必需   | 字符串     | 生成事件的产品的供应商。 <br><br>示例： `Microsoft`  <br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。  |
| **EventSchemaVersion**  | 必需   | 字符串     | 架构的版本。 此处记录的架构版本为 `0.1`         |
| **EventReportUrl**      | 可选    | 字符串     | 在资源的事件中提供的 URL，提供有关该事件的其他信息。|
| Dvc                 | Alias       | 字符串     | 发生该事件的设备的唯一标识符。 <br><br>例如：`ContosoDc.Contoso.Azure`<br><br>此字段可以别名化 [DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确的设备的云源，请使用与 [EventProduct](#eventproduct) 字段相同的值。            |
| <a name ="dvcipaddr"></a>DvcIpAddr   | 建议 | IP 地址 | 发生文件事件的设备的 IP 地址。  <br><br>示例： `45.21.42.12`    |
| <a name ="dvchostname"></a>“DvcHostname”    | 建议 | 主机名   | 发生文件事件的设备的主机名。 <br><br>示例： `ContosoDc.Contoso.Azure` |
| <a name ="dvcid"></a>“DvcId”  | 可选    | 字符串     |  发生文件事件的设备的唯一 ID。 <br><br>示例： `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 可选    | MAC        |   发生文件事件的设备的 MAC。  <br><br>示例： `00:1B:44:11:3A:B7`       |
| “DvcOs”               | 可选    | 字符串     |         发生文件事件的设备上运行的操作系统。    <br><br>示例： `Windows`    |
| “DvcOsVersion”        | 可选    | 字符串     |   发生文件事件的设备上的操作系统版本。 <br><br>示例： `10` |
| **AdditionalFields**    | 可选    | 动态    | 如果源提供了值得保留的附加信息，请使用原始字段名称保留这些信息，或者创建动态 AdditionalFields 字段，并在其中以键/值对的形式添加这些附加信息。    |
| | | | |



### <a name="file-event-specific-fields"></a>特定于文件事件的字段

下表中列出的字段特定于文件事件，但类似于其他架构中的字段，并遵循类似的命名约定。

文件事件架构引用以下对于文件活动极为重要的实体：

- Actor。 启动文件活动的用户
- ActingProcess。 由操作者 (Actor) 用来启动文件活动的进程
- TargetFile。 对其执行了操作的文件
- 源文件 (SrcFile)。 在执行操作之前存储文件信息。

下面最适当地演示了这些实体之间的关系：操作者使用操作进程执行文件操作，而操作进程将源文件修改为目标文件   。 

例如：`JohnDoe`（操作者）使用 `Windows File Explorer`（操作进程）将 `new.doc`（源文件）重命名为 `old.doc`（目标文件）   。

| 字段          | 类        | 类型       | 说明   |
|---------------|--------------|------------|-----------------|
| ActingProcessCommandLine |可选  |字符串  | 用于运行操作进程的命令行。 <br><br>示例： `"choco.exe" -v` |
|ActingProcessGuid |可选     | 字符串     |  为操作进程生成的唯一标识符 (GUID)。 可用于跨系统标识进程。  <br><br> 示例： `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessId**| 必需    | 字符串        | 操作进程的进程 ID (PID)。<br><br>示例：`48610176`           <br><br>注意：类型定义为“字符串”以支持不同的系统，但在 Windows 和 Linux 上，此值必须是数字。 <br><br>如果使用的是 Windows 或 Linux 计算机并使用了其他类型，请务必转换值。 例如，如果使用了十六进制值，请将其转换为十进制值。    |
| <a name="actingprocessname"></a>ActingProcessName              | 可选     | 字符串     |   操作进程的名称。 此名称通常是从映像或可执行文件派生的，该映像或可执行文件用于定义映射到进程虚拟地址空间的初始代码和数据。<br><br>示例： `C:\Windows\explorer.exe`  |
|**处理**| Alias| | [ActingProcessName](#actingprocessname) 的别名|
| <a name="actoruserid"></a>ActorUserId    | 建议  | 字符串     |   操作者的唯一 ID。 具体的 ID 取决于生成事件的系统。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。  <br><br>示例： `S-1-5-18`    |
| “ActorUserIdType”| 建议  | 字符串     |  [ActorUserId](#actoruserid) 字段中存储的 ID 的类型。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。 <br><br>示例： `SID`         |
| <a name="actorusername"></a>ActorUsername  | 必需    | 字符串     | 发起事件的用户的用户名。 <br><br>示例： `CONTOSO\WIN-GG82ULGC9GO$`     |
| “ActorUsernameType”              | 必需    | Enumerated |   指定 [ActorUsername](#actorusername) 字段中存储的用户名的类型。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。 <br><br>示例： `Windows`       |
|**用户** | Alias| | [ActorUsername](#actorusername) 字段的别名。 <br><br>示例： `CONTOSO\dadmin`|
| ActorUserType|可选 | Enumerated| 操作者的类型。 支持的值包括： <br><br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other` <br><br>注意：源可以只提供 [ActorOriginalUserType](#actororiginalusertype) 字段的值，必须分析该字段才能获取 ActorUserType 值 。|
|<a name="actororiginalusertype"></a>ActorOriginalUserType |可选 |字符串 | 操作者用户类型，由报告设备提供。 |
|HttpUserAgent |可选 | 字符串 |当远程系统通过 HTTP 或 HTTPS 启动操作时使用的用户代理。<br><br>例如：<br>`Mozilla/5.0 (Windows NT 10.0; Win64; x64)`<br>`AppleWebKit/537.36 (KHTML, like Gecko)`<br>` Chrome/42.0.2311.135`<br>`Safari/537.36 Edge/12.246`|
| **NetworkApplicationProtocol**| 可选|字符串 | 当远程系统启动操作时，此值是 OSI 模型中使用的应用程序层协议。 <br><br>虽然这不是一个枚举字段并接受任何值，但首选的值包括：`HTTP`、`HTTPS`、`SMB`、`FTP` 和 `SSH`<br><br>示例： `SMB`|
|**SrcIpAddr** |建议 |IP 地址 | 当远程系统启动操作时此系统的 IP 地址。<br><br>示例： `185.175.35.214`|
| SrcFileCreationTime|可选 |日期/时间 |创建源文件的时间。 |
|SrcFileDirectory | 可选| 字符串| 源文件所在的文件夹或位置。 此字段应类似于 [SrcFilePath](#srcfilepath) 字段，但不包含最后一个元素。 <br><br>注意：如果此值已在日志源中提供，而不需要从完整路径中提取，则分析器可以提供此值。|
| SrcFileExtension|可选 | 字符串|源文件扩展名。 <br><br>注意：如果此值已在日志源中提供，而不需要从完整路径中提取，则分析器可以提供此值。|
|SrcFileMimeType |可选 |Enumerated |    源文件的 Mime 或媒体类型。 [IANA 媒体类型](https://www.iana.org/assignments/media-types/media-types.xhtml)存储库中列出了支持的值。 |
|SrcFileName |可选 |字符串 | 源文件的名称，不带路径或位置，但带有扩展名（如果相关）。 此字段应类似于 [SrcFilePath](#srcfilepath) 字段中的最后一个元素。 <br><br>注意：如果此值已在日志源中提供，而不需要从完整路径中提取，则分析器可以提供此值。|
| <a name="srcfilepath"></a>SrcFilePath| 必需|字符串 |源文件的完整规范化路径，包括文件夹或位置、文件名和扩展名。 <br><br>有关详细信息，请参阅[路径结构](#path-structure)。<br><br>示例： `/etc/init.d/networking` |
|SrcFilePathType |必需 | Enumerated| [SrcFilePath](#srcfilepath) 的类型。 有关详细信息，请参阅[路径结构](#path-structure)。|
|SrcFileMD5|可选 |MD5 | 源文件的 MD5 哈希。 <br><br>示例：`75a599802f1fa166cdadb360960b1dd0` |
|SrcFileSHA1|可选 |SHA1 |源文件的 SHA-1 哈希。<br><br>示例：<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
|SrcFileSHA256 | 可选|SHA256 |源文件的 SHA-256 哈希。 <br><br>示例：<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`|
|SrcFileSHA512 |可选 | SHA512|源文件的 SHA-512 哈希。 |
|SrcFileSize| 可选|整数 | 源文件的大小，以字节为单位。|
|TargetFileCreationTime | 可选|日期/时间 |创建目标文件的时间。 |
|TargetFileDirectory | 可选|字符串 |目标文件所在的文件夹或位置。 此字段应类似于 [TargetFilePath](#targetfilepath) 字段，但不包含最后一个元素。 <br><br>注意：如果此值已在日志源中提供，而不需要从完整路径中提取，则分析器可以提供此值。|
|TargetFileExtension |可选 |字符串 | 目标文件扩展名。<br><br>注意：如果此值已在日志源中提供，而不需要从完整路径中提取，则分析器可以提供此值。|
| TargetFileMimeType|可选 | Enumerated| 目标文件的 Mime 或媒体类型。 [IANA 媒体类型](https://www.iana.org/assignments/media-types/media-types.xhtml)存储库中列出了允许的值。|
| **TargetFileName**|可选 |字符串 |目标文件的名称，不带路径或位置，但带有扩展名（如果相关）。 此字段应类似于 [TargetFilePath](#targetfilepath) 字段中的最后一个元素。<br><br>注意：如果此值已在日志源中提供，而不需要从完整路径中提取，则分析器可以提供此值。|
|<a name="targetfilepath"></a>TargetFilePath | 必需| 字符串| 目标文件的完整规范化路径，包括文件夹或位置、文件名和扩展名。 有关详细信息，请参阅[路径结构](#path-structure)。 <br><br>注意：如果记录中不包含文件夹或位置信息，则仅在此处存储文件名。 <br><br>示例： `C:\Windows\System32\notepad.exe`|
|TargetFilePathType | 必需|Enumerated | [TargetFilePath](#targetfilepath) 的类型。 有关详细信息，请参阅[路径结构](#path-structure)。    |
|**文件路径** |Alias | | [TargetFilePath](#targetfilepath) 字段的别名。|
| TargetFileMD5| 可选| MD5|目标文件的 MD5 哈希。 <br><br>示例： `75a599802f1fa166cdadb360960b1dd0` |
|TargetFileSHA1 |可选 |SHA1 |目标文件的 SHA-1 哈希。 <br><br>示例：<br> `d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0`|
|TargetFileSHA256 | 可选|SHA256 |目标文件的 SHA-256 哈希。 <br><br>示例：<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`  |
| TargetFileSHA512| 可选| SHA512|源文件的 SHA-512 哈希。 |
|**哈希**|Alias | |最佳可用目标文件哈希的别名。 |
|TargetFileSize |可选 | 整数|目标文件的大小，以字节为单位。 |
| TargetUrl|可选 | 字符串|通过 HTTP 或 HTTPS 启动操作时使用的 URL。 <br><br>示例： `https://onedrive.live.com/?authkey=...` |
| | | | |



## <a name="path-structure"></a>路径结构

应规范化路径以便与以下格式之一相匹配。 值在规范化后的格式将反映在相应的 FilePathType 字段中。

|类型  |示例  |说明  |
|---------|---------|---------|
|Windows 本地     |   `C:\Windows\System32\notepad.exe`      |      由于 Windows 路径名称不区分大小写，因此，此类型意味着值不区分大小写。   |
|Windows 共享     |      `\\Documents\My Shapes\Favorites.vssx`   | 由于 Windows 路径名称不区分大小写，因此，此类型意味着值不区分大小写。        |
|Unix     |  `/etc/init.d/networking`       |     由于 Unix 路径名称区分大小写，因此，此类型意味着值区分大小写。  <br><br>- 对于 AWS S3，请使用此类型。 连接桶和密钥名称以创建路径。 <br><br>- 对于 Azure Blob 存储对象密钥，请使用此类型。   |
|**URL**     |  `https://1drv.ms/p/s!Av04S_*********we`       | 在以 URL 形式提供文件路径时使用。 URL 并不限于 http 或 https，任何值（包括 FTP 值）都是有效的。  |
|     |         |         |



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Sentinel 中的规范化](normalization.md)
- [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)
- [Azure Sentinel DNS 规范化架构参考](dns-normalization-schema.md)
- [Azure Sentinel 网络规范化架构参考](normalization-schema.md)
- [Azure Sentinel 进程事件规范化架构参考（公共预览版）](process-events-normalization-schema.md)
- [Azure Sentinel 注册表事件规范化架构参考（公共预览版）](registry-event-normalization-schema.md)