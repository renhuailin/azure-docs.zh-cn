---
title: Azure Sentinel 网络会话规范化架构参考（公共预览版）| Microsoft Docs
description: 本文显示了 Azure Sentinel 网络会话规范化架构。
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/17/2021
ms.author: ofshezaf
ms.openlocfilehash: 388f7b0d3ed26c10ad91e17ccbbeabf705b3894c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560690"
---
# <a name="azure-sentinel-network-session-normalization-schema-reference-public-preview"></a>Azure Sentinel 网络会话规范化架构参考（公共预览版）

网络会话规范化架构用于描述 IP 网络活动。 这包括网络连接和网络会话。 例如，操作系统、路由器、防火墙、入侵防护系统和 Web 安全网关会报告此类事件。

有关 Azure Sentinel 中的规范化的详细信息，请参阅[规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)。

> [!IMPORTANT]
> 本文介绍网络规范化架构版本 0.2。[版本 0.1](normalization-schema-v1.md) 是在 ASIM 推出之前发布的，在某些方面与 ASIM 不相符。 有关详细信息，请参阅[网络规范化架构版本之间的差异](normalization-schema-v1.md#changes)。 
>

> [!IMPORTANT]
> 网络规范化架构目前为预览版。 此功能不附带服务级别协议，不建议将其用于生产工作负荷。
>
> [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="support-for-common-network-telemetry-sources"></a>对常见网络遥测源的支持

网络规范化架构可以代表任何 IP 网络会话，但专门设计用于提供对常见源类型的支持，包括：

- Netflow
- 防火墙
- 入侵防护系统
- Web 服务器
- Web 安全网关

以下部分提供了有关规范化和使用不同源类型的架构的指导。 每种源类型可以：
- 支持辅助字段列表中的附加字段：[中间设备字段](#Intermediary)、[HTTP 会话字段](#http-session-fields)和[检查字段](#inspection-fields)。 某些字段可能仅在特定源类型的上下文中是必填的。
- 允许在 `EventType` 和 `EventResult` 等常见事件字段中使用特定于源类型的值。
- 除了支持 `imNetworkSession` 分析器以外，还支持 `imWebSession` 和/或 `inNetworkNotable` 分析器。

### <a name="netflow-log-sources"></a>Netflow 日志源

| 任务 | 说明|
| --- | --- |
| 规范化 Netflow 事件 | 若要规范化 Netflow 事件，请将其映射到[网络会话字段](#network-session-fields)。 Netflow 遥测支持聚合，`EventCount` 字段值应反映这一点，并且应设置为 Netflow Flows 值。 |
| 使用 Netflow 事件 | Netflow 事件作为 [imNetworkSession](#use-parsers) 源不可知分析器的一部分显示。 创建聚合查询时，请确保考虑 `EventCount` 字段值，该值不能始终设置为 `1`。 |
| | |

### <a name="firewall-log-sources"></a>防火墙日志源

| 任务 | 说明 |
| --- | --- |
| 规范化防火墙事件 | 若要规范来自防火墙的事件，请将相关事件映射到[事件](#event-fields)字段、[网络会话](#network-session-fields)字段和[会话检查](#inspection-fields)字段。 筛选这些事件，并将其添加到 [inNetworkNotables](#use-parsers) 源不可知分析器。 |
| 使用防火墙事件 | 防火墙事件作为 [imNetworkSession](#use-parsers) 源不可知分析器的一部分显示。 由防火墙检查引擎识别到的相关事件也作为 [inNetworkNotables](#use-parsers) 源不可知分析器的一部分显示。 |
| | |

### <a name="intrusion-prevention-systems-ips-log-sources"></a>入侵防护系统 (IPS) 日志源

| 任务 | 说明 |
| --- | --- |
| 规范化 IPS 事件 | 若要规范化来自入侵防护系统的事件，请映射[事件字段](#event-fields)、[网络会话字段](#network-session-fields)和[会话检查字段](#inspection-fields)。 确保在 [imNetworkSession](#use-parsers) 和 [inNetworkNotables](#use-parsers) 源不可知分析器中都包含特定于源的分析器。 |
| 使用 IPS 事件 | IPS 事件作为 [imNetworkSession](#use-parsers) 和 [inNetworkNotables](#use-parsers) 源不可知分析器的一部分显示。 |
| | |

### <a name="web-servers"></a>Web 服务器

| 任务 | 说明 |
| --- | --- |
| 规范化 Web 服务器事件 | 若要规范化来自 Web 服务器的事件，请映射[事件字段](#event-fields)、[网络会话字段](#network-session-fields)和 [HTTP 会话字段](#http-session-fields)。 确保将 `EventType` 值设置为 `HTTP Session`，并遵循有关 `EventResult` 和 `EventResultDetails` 字段的特定于 HTTP 会话的指导。 <br><br>确保在 [imNetworkSession](#use-parsers) 和 [imWebSession](#use-parsers) 源不可知分析器中都包含特定于源的分析器。 |
| 使用 Web 服务器事件 | Web 服务器事件作为 [imNetworkSession](#use-parsers) 源不可知分析器的一部分显示。 但是，若要使用任何特定于 HTTP 的字段，请使用 [imWebSession](#use-parsers) 分析器。 |
| | |

### <a name="web-security-gateways"></a>Web 安全网关

| 任务 | 说明 |
| --- | --- |
| 规范化 Web 安全网关事件 | 若要规范化来自 Web 服务器网关的事件，请映射[事件字段](#event-fields)、[网络会话字段](#network-session-fields)、[HTTP 会话字段](#http-session-fields)、[会话检查字段](#inspection-fields)，并选择性地映射中间字段。 <br><br>确保将 `EventType` 设置为 `HTTP`，并遵循有关 `EventResult` 和 `EventResultDetails` 字段的特定于 HTTP 会话的指导。 <br><br>确保在 [imNetworkSession](#use-parsers) 和 [imWebSession](#use-parsers) 源不可知分析器中都包含特定于源的分析器。 此外，筛选检查引擎检测到的任何事件，并将其添加到 [inNetworkNotables](#use-parsers) 源不可知分析器。 |
| 使用 Web 安全网关事件 | Web 服务器事件作为 [imNetworkSession](#use-parsers) 源不可知分析器的一部分显示。 <br><br>- 若要使用任何特定于 HTTP 的字段，请使用 [imWebSession](#use-parsers) 分析器。<br>- 若要分析检测到的会话，请使用 [inNetworkNotables](#use-parsers) 源不可知分析器。 |
| | |


## <a name="use-parsers"></a>使用分析器

若要使用统一了所有内置分析器的源不可知分析器，并确保针对所有配置的源运行分析，请使用以下任一分析器：


- imNetworkSession，用于所有网络会话
- imWebSession，用于通常由 Web 服务器、Web 代理和 Web 安全网关报告的 HTTP 会话
- inNetworkNotables，用于检测引擎检测到的会话，通常是可疑会话。 值得注意的事件通常由入侵防护系统、防火墙和 Web 安全网关报告。

从 [Azure Sentinel GitHub 存储库](https://aka.ms/AzSentinelNetworkSession)部署[与源无关的分析器和特定于源的分析器](normalization-about-parsers.md)。

### <a name="built-in-source-specific-parsers"></a>内置特定于源的分析器

Azure Sentinel 提供以下内置的特定于产品的网络会话分析器：

- 特定于源的分析器：
  - Microsoft 365 Defender for Endpoints - vimNetworkSessionMicrosoft365Defender
  - Microsoft Defender for IoT - Endpoint (MD4IoT) - vimNetworkSessionMD4IoT
  - Microsoft Sysmon for Linux - vimNetworkSessionSysmonLinux
  - Windows 事件防火墙 - 使用 Windows 事件 515x、Log Analytics 代理或 Azure Monitor 代理在 Event 或 WindowsEvent 表中收集的 Windows 防火墙活动 - vimNetworkSessionMicrosoftWindowsEventFirewall 

可以从 [Azure Sentinel GitHub 存储库](https://aka.ms/AzSentinelNetworkSession)部署分析程序。

### <a name="add-your-own-normalized-parsers"></a>添加自己的规范化分析器

为网络会话信息模型实现自定义分析器时，请使用以下语法来命名 KQL 函数：`imNetworkSession<vendor><Product>`。 此函数应映射与源相关的所有字段。

根据相关源不可知分析器的日志源，按需将 KQL 函数添加到这些分析器。 有关详细信息，请参阅：

- [Netflow 日志源](#netflow-log-sources)
- [防火墙日志源](#firewall-log-sources)
- [入侵防护系统 (IPS) 日志源](#intrusion-prevention-systems-ips-log-sources)
- [Web 服务器](#web-servers)
- [Web 安全网关](#web-security-gateways)

## <a name="schema-details"></a>架构详细信息

网络会话信息模型符合 [OSSEM 网络实体架构](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md)。

为了符合行业最佳做法，网络会话架构使用描述符 Src 和 Dst 来标识网络会话源和目标设备，且不会在字段名称中包含标记 Dvc  。

因此，举例而言，源设备主机名和 IP 地址分别命名为 SrcHostname 和 SrcIpAddr，而不是 SrcDvcHostname 和 SrcDvcIpAddr   。 前缀 Dvc 仅用于报告设备或中间设备（如果适用）。

描述与源和目标设备关联的用户和应用程序的字段也使用 Src 和 Dst 描述符 。

其他 ASIM 架构通常使用 Target 而不是 Dst 。

### <a name="log-analytics-fields"></a>Log Analytics 字段

Log Analytics 针对每条记录生成以下字段，在[创建自定义连接器](create-custom-connector.md)时可以替代这些字段。


| 字段 | 类型 | 讨论 (Discussion) |
|-------|------|------------|
| <a name="timegenerated"></a>“TimeGenerated” | datetime | 报告设备生成事件的时间。 |
| **\_ResourceId** | GUID | 报告设备或服务的 Azure 资源 ID，或使用 Syslog、CEF 或 WEF 转发的事件的日志转发器资源 ID。 |
| **类型** | String | 从中提取记录的原始表。 当同一事件可以通过多个通道传入不同的表，但具有相同的 `EventVendor` 和 `EventProduct` 值时，此字段很有用。 <br><br>例如，Sysmon 事件可以传入 Event 表或 SecurityEvent 表 。 |
| | | |

> [!NOTE]
> Log Analytics 还会添加与安全用例不太相关的其他字段。 有关详细信息，请参阅 [Azure Monitor 日志中的标准列](../azure-monitor/logs/log-standard-columns.md)。
>

### <a name="event-fields"></a>事件字段

事件字段通用于所有架构，描述活动本身和报告设备。

| 字段 | 类 | 类型 | 说明 |
|-------|-------|------|-------------|
| **EventMessage** | 可选 | 字符串 | 一般消息或说明，包含在记录中或者根据记录生成。 |
| “EventCount” | 必需 | Integer | 记录描述的事件数。 <br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。 <br><br>注意：Netflow 源支持聚合，EventCount 字段应设置为 Netflow FLOWS 字段的值  。 对于其他源，该值通常设置为 `1`。 |
| **EventStartTime** | 必需 | 日期/时间 | 如果源支持聚合且记录表示多个事件，则此字段将指定生成第一个事件的时间。 否则，此字段将别名化 [TimeGenerated](#timegenerated) 字段。 |
| **EventEndTime** | 必需 | Alias | [TimeGenerated](#timegenerated) 字段的别名。 |
| **EventType** | 必需 | Enumerated | 描述记录报告的操作。<br><br> 对于网络会话记录，支持的值包括：<br>- `NetworkConnection`<br>- `NetworkSession`<br>- `HTTPsession` |
| **EventSubType** | 可选 | 字符串 | 事件类型的附加说明（如果适用）。 <br> 对于网络会话记录，支持的值包括：<br>- `Start`<br>- `End` |
| **EventResult** | 必需 | Enumerated | 描述事件结果，规范化为以下值之一： <br> - `Success` <br> - `Partial` <br> - `Failure` <br> - `NA`（不适用） <br><br>对于 HTTP 会话，`Success` 定义为低于 `400` 的状态代码，`Failure` 定义为高于 `400` 的状态代码。 有关 HTTP 状态代码的列表，请参阅 [W3 Org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。<br><br>源可以只提供 [EventResultDetails](#eventresultdetails) 字段的值，必须分析该字段才能获取 EventResult 值。 |
| <a name="eventresultdetails"></a>EventResultDetails | 可选 | 字符串 | 对于 HTTP 会话，值应为 HTTP 状态代码。 <br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 原始值应存储在 EventOriginalResultDetails 字段中。|
| EventOriginalResultDetails    | 可选    | 字符串     |  [EventResultDetails](#eventresultdetails) 的原始记录中提供的值（如果已由源提供）。|
| **EventSeverity** | 必需 | Enumerated | 事件的严重性（如果该事件表示检测到的威胁或警报）。 可能的值包括 `Informational`、`Low`、`Medium` 和 `High`。 <br><br>如果该事件不表示威胁，请使用 `Informational` 值。<br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 将原始值存储在 [EventOriginalSeverity](#eventoriginalseverity) 字段中。 |
| <a name="eventoriginalseverity"></a>EventOriginalSeverity | 可选 | String | 源记录中提供的原始严重性值。 |
| **EventOriginalUid** | 可选 | 字符串 | 原始记录的唯一 ID（如果已由源提供）。<br><br>示例： `69f37748-ddcd-4331-bf0f-b137f1ea83b` |
| “EventOriginalType” | 可选 | 字符串 | 原始事件类型或 ID（如果已由源提供）。 <br><br>示例： `5031` |
| <a name="eventproduct"></a>“EventProduct” | 必需 | 字符串 | 生成事件的产品。<br><br>示例： `Sysmon`<br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。 |
| **EventProductVersion** | 可选 | 字符串 | 生成事件的产品的版本。<br><br>示例： `12.1` |
| **EventVendor** | 必需 | 字符串 | 生成事件的产品的供应商。<br><br>示例： `Microsoft`<br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。 |
| EventSchema | 必需 | 字符串 | 架构的名称。 此处所述的架构名称为 `NetworkSession`。 |
| **EventSchemaVersion** | 必需 | 字符串 | 架构的版本。 此处所述的架构版本为 `0.2`。 |
| **EventReportUrl** | 可选 | 字符串 | 在资源的事件中提供的 URL，提供有关该事件的其他信息。 |
| Dvc | Alias | 字符串 | 报告设备或中间设备的唯一标识符。<br><br>示例：`ContosoDc.Contoso.Azure`<br><br>此字段的别名可以是 [DvcFQDN](#dvcfqdn)、[DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确的设备的云源，请使用与 [EventProduct](#eventproduct) 字段相同的值。 |
| <a name="dvcipaddr"></a>“DvcIpAddr” | 建议 | IP 地址 | 报告设备或中间设备的 IP 地址。<br><br>示例： `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>“DvcHostname” | 必需 | 字符串 | 报告设备或中间设备的主机名，不包括域信息。 如果没有可用的设备名称，请在此字段中存储相关的 IP 地址。<br><br>示例：`DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>DvcDomain | 建议 | 字符串 | 报告设备或中间设备的域。<br><br>示例：`Contoso` |
| <a name="dvcdomaintype"></a>DvcDomainType | 建议 | Enumerated | [DvcDomain](#dvcdomain) 的类型（如果已知）。 可能的值包括：<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>注意：如果使用 [DvcDomain](#dvcdomain) 字段，则此字段是必填的。 |
| <a name="dvcfqdn"></a>DvcFQDN | 可选 | String | 报告设备或中间设备的主机名，包括域信息（如果可用）。 <br><br> 示例：`Contoso\DESKTOP-1282V4D`<br><br>注意：此字段支持传统的 FQDN 格式和 Windows 域\主机名格式。 [DvcDomainType](#dvcdomaintype) 字段反映使用的格式。  |
| <a name="dvcid"></a>“DvcId” | 可选 | 字符串 | 记录中报告的报告设备或中间设备 ID。<br><br>示例：`ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| DvcIdType | 可选 | Enumerated | [DvcId](#dvcid) 的类型（如果已知）。 可能的值包括：<br> - `AzureResourceId`<br>- `MDEid`<br><br>如果有多个可用 ID，请使用列表中的第一个 ID，并分别使用字段名称 DvcAzureResourceId 和 DvcMDEid 存储其他 ID 。<br><br>注意：如果使用 [DvcId](#dvcid) 字段，则此字段是必填的。 |
| **AdditionalFields** | 可选 | 动态 | 如果源提供了值得保留的其他信息，请使用原始字段名称保留这些信息，或者创建动态 AdditionalFields 字段，并在其中以键/值对的形式添加这些额外信息。 |
| | | | |

### <a name="network-session-fields"></a>网络会话字段

以下字段通用于所有网络会话活动日志记录：

| 字段 | 类 | 类型 | 说明 |
|-------|-------|------|-------------|
|<a name="dstipaddr"></a> **DstIpAddr** | 建议 | IP 地址 | 连接或会话目标的 IP 地址。 <br><br>示例：`2001:db8::ff00:42:8329`<br><br>注意：如果指定了 [DstHostname](#dsthostname)，则此值是必需的。 |
| <a name="dstportnumber"></a>DstPortNumber | 可选 | 整数 | 目标 IP 端口。<br><br>示例：`443` |
| <a name="dsthostname"></a>DstHostname | 建议 | 字符串 | 目标设备主机名，不包括域信息。 如果没有可用的设备名称，请在此字段中存储相关的 IP 地址。<br><br>示例：`DESKTOP-1282V4D`<br><br>注意：如果指定了 [DstIpAddr](#dstipaddr)，则此值是必需的。 |
| **主机名** | Alias | | [DstHostname](#dsthostname) 的别名 |
| <a name="dstdomain"></a>DstDomain | 建议 | 字符串 | 目标设备的域。<br><br>示例：`Contoso` |
| <a name="dstdomaintype"></a>DstDomainType | 建议 | Enumerated | [DstDomain](#dstdomain) 的类型（如果已知）。 可能的值包括：<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>如果使用了 [DstDomain](#dstdomain)，则该字段是必填的。 |
| DstFQDN | 可选 | String | 目标设备主机名，包括域信息（如果可用）。 <br><br>示例：`Contoso\DESKTOP-1282V4D` <br><br>注意：此字段支持传统的 FQDN 格式和 Windows 域\主机名格式。 [DstDomainType](#dstdomaintype) 反映使用的格式。   |
| <a name="dstdvcid"></a>DstDvcId | 可选 | String | 记录中报告的目标设备的 ID。<br><br>示例：`ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| DstDvcIdType | 可选 | Enumerated | [DstDvcId](#dstdvcid) 的类型（如果已知）。 可能的值包括：<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>如果有多个可用 ID，请使用上述列表中的第一个 ID，并分别在 DstDvcAzureResourceId 或 DstDvcMDEid 字段中存储其他 ID 。<br><br>如果使用了 DstDeviceId，则该字段是必填的。|
| DstDeviceType | 可选 | Enumerated | 目标设备的类型。 可能的值包括：<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="dstuserid"></a>DstUserId | 可选 | String | 目标用户的计算机可读的唯一字母数字表示形式。 <br><br>支持的格式和类型包括：<br>- SID (Windows)：`S-1-5-21-1377283216-344919071-3415362939-500`<br>- UID (Linux)：`4578`<br>-  AADID (Azure Active Directory)：`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  OktaId：`00urjk4znu3BcncfY0h7`<br>-  AWSId：`72643944673`<br><br>在 [DstUserIdType](#dstuseridtype) 字段中存储 ID 类型。 如果其他 ID 可用，我们建议将字段名称分别规范化为 DstUserSid、DstUserUid、DstUserAADID、DstUserOktaId 和 UserAwsId    。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。<br><br>示例： `S-1-12` |
| <a name="dstuseridtype"></a>DstUserIdType | 可选 | Enumerated | [DstUserId](#dstuserid) 字段中存储的 ID 的类型。 <br><br>支持的值为 `SID`、`UIS`、`AADID`、`OktaId` 和 `AWSId`。 |
| <a name="dstusername"></a>DstUsername | 可选 | String | 目标用户名，包括域信息（如果可用）。 <br><br>使用以下格式之一并遵循以下优先顺序：<br>- UPN/电子邮件：`johndow@contoso.com`<br>- Windows：`Contoso\johndow`<br>- DN：`CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- 简单：`johndow`。 仅当未提供域信息时才使用简单格式。<br><br>在 [DstUsernameType](#dstusernametype) 字段中存储用户名类型。 如果其他 ID 可用，我们建议将字段名称规范化为 DstUserUpn、DstUserWindows 和 DstUserDn。有关更多信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)  。<br><br>示例： `AlbertE` |
| **用户** | Alias | | [DstUsername](#dstusername) 的别名 |
| <a name="dstusernametype"></a>DstUsernameType | 可选 | Enumerated | 指定 [DstUsername](#dstusername) 字段中存储的用户名的类型。 支持的值包括：`UPN`、`Windows`、`DN` 和 `Simple`。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。<br><br>示例： `Windows` |
| DstUserType | 可选 | Enumerated | 操作者的类型。 支持的值包括：<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 在 [DstOriginalUserType](#dstoriginalusertype) 字段中存储原始值。 |
| <a name="dstoriginalusertype"></a>DstOriginalUserType | 可选 | String | 原始目标用户类型（如果源已提供）。 |
| **DstUserDomain** | 可选 | String | 此字段只是为了实现后向兼容而保留的。 ASIM 要求提供域信息（如果可用）作为 [DstUsername](#dstusername) 字段的一部分。 |
| <a name="dstappname"></a>DstAppName | 可选 | 字符串 | 目标应用程序的名称。<br><br>示例：`Facebook` |
| <a name="dstappid"></a>DstAppId | 可选 | 字符串 | 目标应用程序的 ID，由报告设备报告。<br><br>示例：`124` |
| DstAppType | 可选 | 字符串 | 代表参与者授权的应用程序的类型。 支持的值包括：<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `URL`<br>- `SaaS application`<br>- `Other`<br><br>如果使用了 [DstAppName](#dstappname) 或 [DstAppId](#dstappid)，则此字段是必填的。 |
| **DstZone** | 可选 | String | 目标的网络区域，由报告设备定义。<br><br>示例：`Dmz` |
| **DstInterfaceName** | 可选 | 字符串 | 由目标设备用来建立连接或会话的网络接口。<br><br>示例：`Microsoft Hyper-V Network Adapter` |
| **DstInterfaceGuid** | 可选 | String | 在目标设备上使用的网络接口的 GUID。<br><br>示例：<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **DstMacAddr** | 可选 | 字符串 | 由目标设备用来建立连接或会话的网络接口的 MAC 地址。<br><br>示例：`06:10:9f:eb:8f:14` |
| **DstGeoCountry** | 可选 | 国家/地区 | 与目标 IP 地址关联的国家/地区。 有关详细信息，请参阅[逻辑类型](normalization-about-schemas.md#logical-types)。<br><br>示例：`USA` |
| **DstGeoRegion** | 可选 | 区域 | 与目标 IP 地址关联的国家/地区中的区域或州/省。 有关详细信息，请参阅[逻辑类型](normalization-about-schemas.md#logical-types)。<br><br>示例：`Vermont` |
| **DstGeoCity** | 可选 | 城市 | 与目标 IP 地址关联的城市。 有关详细信息，请参阅[逻辑类型](normalization-about-schemas.md#logical-types)。<br><br>示例：`Burlington` |
| **DstGeoLatitude** | 可选 | 纬度 | 与目标 IP 地址关联的地理坐标的纬度。 有关详细信息，请参阅[逻辑类型](normalization-about-schemas.md#logical-types)。<br><br>示例：`44.475833` |
| **DstGeoLongitude** | 可选 | 经度 | 与目标 IP 地址关联的地理坐标的经度。 有关详细信息，请参阅[逻辑类型](normalization-about-schemas.md#logical-types)。<br><br>示例：`73.211944` |
| <a name="srcipaddr"></a>SrcIpAddr | 建议 | IP 地址 | 从中发起了连接或会话的 IP 地址。 如果指定了 SrcHostname，则此值是必需的。<br><br>示例：`77.138.103.108` |
| **IpAddr** | Alias | | [SrcIpAddr](#srcipaddr) 的别名 |
| **SrcPortNumber** | 可选 | 整数 | 从中发起了连接的 IP 端口。 可能与包含多个连接的会话无关。<br><br>示例：`2335` |
| SrcHostname | 建议 | 字符串 | 源设备主机名，不包括域信息。 如果没有可用的设备名称，请在此字段中存储相关的 IP 地址。如果指定了 [SrcIpAddr](#srcipaddr)，则此值是必需的。<br><br>示例：`DESKTOP-1282V4D` |
|<a name="srcdomain"></a>SrcDomain | 建议 | 字符串 | 源设备的域。<br><br>示例：`Contoso` |
| <a name="srcdomaintype"></a>SrcDomainType | 建议 | Enumerated | [SrcDomain](#srcdomain) 的类型（如果已知）。 可能的值包括：<br>- `Windows`（例如：`contoso`）<br>- `FQDN`（例如：`microsoft.com`）<br><br>如果使用了 [SrcDomain](#srcdomain)，则该字段是必填的。 |
| SrcFQDN | 可选 | 字符串 | 源设备主机名，包括域信息（如果可用）。 <br><br>注意：此字段支持传统的 FQDN 格式和 Windows 域\主机名格式。 [SrcDomainType](#srcdomaintype) 字段反映使用的格式。 <br><br>示例：`Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>SrcDvcId | 可选 | 字符串 | 记录中报告的源设备的 ID。<br><br>例如：`ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| SrcDvcIdType | 可选 | Enumerated | [SrcDvcId](#srcdvcid) 的类型（如果已知）。 可能的值包括：<br> - `AzureResourceId`<br>- `MDEid`<br><br>如果有多个可用 ID，请使用上述列表中的第一个 ID，并分别在 SrcDvcAzureResourceId 或 SrcDvcMDEid 字段中存储其他 ID 。<br><br>注意：如果使用了 [SrcDvcId](#srcdvcid)，则此字段是必填的。 |
| SrcDeviceType | 可选 | Enumerated | 源设备的类型。 可能的值包括：<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>SrcUserId | 可选 | String | 源用户的计算机可读的唯一字母数字表示形式。 格式和支持的类型包括：<br>-  SID (Windows)：`S-1-5-21-1377283216-344919071-3415362939-500`<br>-  UID (Linux)：`4578`<br>-  AADID (Azure Active Directory)：`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  OktaId：`00urjk4znu3BcncfY0h7`<br>-  AWSId：`72643944673`<br><br>在 [SrcUserIdType](#srcuseridtype) 字段中存储 ID 类型。 如果其他 ID 可用，我们建议将字段名称分别规范化为 SrcUserSid、SrcUserUid、SrcUserAadId、SrcUserOktaId 和 UserAwsId。有关详细信息，请参阅“用户实体”。<br><br>示例：S-1-12 |
| <a name="srcuseridtype"></a>SrcUserIdType | 可选 | Enumerated | [SrcUserId](#srcuserid) 字段中存储的 ID 的类型。 支持的值包括：`SID`、`UIS`、`AADID`、`OktaId` 和 `AWSId`。 |
| <a name="srcusername"></a>SrcUsername | 可选 | String | 源用户名，包括域信息（如果可用）。 使用以下格式之一并遵循以下优先顺序：<br>- UPN/电子邮件：`johndow@contoso.com`<br>- Windows：`Contoso\johndow`<br>- DN：`CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- 简单：`johndow`。 仅当未提供域信息时才使用简单格式。<br><br>在 [SrcUsernameType](#srcusernametype) 字段中存储用户名类型。 如果其他 ID 可用，我们建议将字段名称规范化为 SrcUserUpn、SrcUserWindows 和 SrcUserDn  。<br><br>有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。<br><br>示例： `AlbertE` |
| <a name="srcusernametype"></a>SrcUsernameType | 可选 | Enumerated | 指定 [SrcUsername](#srcusername) 字段中存储的用户名的类型。 支持的值为 `UPN`、`Windows`、`DN` 和 `Simple`。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。<br><br>示例： `Windows` |
| SrcUserType | 可选 | Enumerated | 操作者的类型。 允许值包括：<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 将原始值存储在 [EventOriginalSeverity](#eventoriginalseverity) 字段中。 |
| SrcOriginalUserType | | | 原始源用户类型（如果源已提供）。 |
| **SrcUserDomain** | 可选 | 字符串 | 此字段只是为了实现后向兼容而保留的。 ASIM 要求提供域信息（如果可用）作为 [SrcUsername](#srcusername) 字段的一部分。 |
| <a name="srcappname"></a>SrcAppName | 可选 | String | 源应用程序的名称。 <br><br>示例：`filezilla.exe` |
| <a name="srcappid"></a>SrcAppId | 可选 | 字符串 | 目标应用程序的 ID，由报告设备报告。<br><br>示例：`124` |
| SrcAppType | 可选 | 字符串 | 源应用程序的类型。 支持的值包括：<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `Other`<br><br>如果使用了 [SrcAppName](#srcappname) 或 [SrcAppId](#srcappid)，则此字段是必填的。 |
| **SrcZone** | 可选 | 字符串 | 源的网络区域，由报告设备定义。<br><br>示例：`Internet` |
| **SrcIntefaceName** | 可选 | String | 由源设备用来建立连接或会话的网络接口。 <br><br>示例：`eth01` |
| **SrcInterfaceGuid** | 可选 | String | 在源设备上使用的网络接口的 GUID。<br><br>示例：<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **SrcMacAddr** | 可选 | 字符串 | 从中发起了连接或会话的网络接口的 MAC 地址。<br><br>示例： `06:10:9f:eb:8f:14` |
| **SrcGeoCountry** | 可选 | 国家/地区 | 与源 IP 地址关联的国家/地区。<br><br>示例：`USA` |
| **SrcGeoRegion** | 可选 | 区域 | 与源 IP 地址关联的国家/地区中的区域。<br><br>示例：`Vermont` |
| **SrcGeoCity** | 可选 | 城市 | 与源 IP 地址关联的城市。<br><br>示例：`Burlington` |
| **SrcGeoLatitude** | 可选 | 纬度 | 与源 IP 地址关联的地理坐标的纬度。<br><br>示例：`44.475833` |
| **SrcGeoLongitude** | 可选 | 经度 | 与源 IP 地址关联的地理坐标的经度。<br><br>示例：`73.211944` |
| **NetworkApplicationProtocol** | 可选 | String | 连接或会话使用的应用程序层协议。 如果提供了 [DstPortNumber](#dstportnumber) 值，建议也包含 NetworkApplicationProtocol。 如果无法从源获取该值，请从 [DstPortNumber](#dstportnumber) 值派生该值。<br><br>示例：`HTTP` |
| **NetworkProtocol** | 可选 | Enumerated | 连接或会话使用的 IP 协议，在 [IANA 协议分配](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)中列出。 通常为 `TCP`、`UDP` 或 `ICMP`。<br><br>示例：`TCP` |
| **NetworkDirection** | 可选 | Enumerated | 连接或会话的方向：入站到组织或者从组织出站。 支持的值包括：`Inbound`、`Outbound`、`Listen`。 `Listen` 指示设备已开始接受网络连接，但实际上不一定已连接。|
| <a name="networkduration"></a>NetworkDuration | 可选 | 整数 | 完成网络会话或连接所花费的时间，以毫秒为单位。<br><br>示例： `1500` |
| **持续时间** | Alias | | [NetworkDuration](#networkduration) 的别名 |
| **NetworkIcmpCode** | 可选 | 整数 | 对于 ICMP 消息，该字段表示 [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780)（适用于 IPv4 网络连接）或 [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443)（适用于 IPv6 网络连接）中所述的 ICMP 消息类型数值。 如果提供了 [NetworkIcmpType](#networkicmptype) 值，则此字段是必填的。 如果无法从源获取该值，请改为从 [NetworkIcmpType](#networkicmptype) 字段派生该值。<br><br>示例：`34` |
|<a name="networkicmptype"></a> **NetworkIcmpType** | 可选 | String | 对于 ICMP 消息，该字段表示 [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780)（适用于 IPv4 网络连接）或 [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443)（适用于 IPv6 网络连接）中所述的 ICMP 消息类型文本表示形式。<br><br>示例：`Destination Unreachable` |
| **DstBytes** | 建议 | Integer | 从连接或会话的目标发送到源的字节数。 如果聚合了事件，则 DstBytes 应为所有聚合会话的总和。<br><br>示例：`32455` |
| **SrcBytes** | 建议 | Integer | 从连接或会话的源发送到目标的字节数。 如果聚合了事件，则 SrcBytes 应为所有聚合会话的总和。<br><br>示例：`46536` |
| **NetworkBytes** | 可选 | 整数 | 双向发送的字节数。 如果 BytesReceived 和 BytesSent 都存在，则 BytesTotal 应等于它们的总和  。 如果聚合了事件，则 NetworkBytes 应为所有聚合会话的总和。<br><br>示例：`78991` |
| **DstPackets** | 可选 | 整数 | 从连接或会话的目标发送到源的数据包数。 数据包的含义由报告设备定义。如果聚合了事件，则 DstPackets 应为所有聚合会话的总和。<br><br>示例：`446` |
| **SrcPackets** | 可选 | 整数 | 从连接或会话的源发送到目标的数据包数。 数据包的含义由报告设备定义。 如果聚合了事件，则 SrcPackets 应为所有聚合会话的总和。<br><br>示例：`6478` |
| **NetworkPackets** | 可选 | 整数 | 双向发送的数据包数。 如果 PacketsReceived 和 PacketsSent 都存在，则 BytesTotal 应等于它们的总和  。 数据包的含义由报告设备定义。 如果聚合了事件，则 NetworkPackets 应为所有聚合会话的总和。<br><br>示例：`6924` |
|<a name="networksessionid"></a>NetworkSessionId | 可选 | string | 报告设备报告的会话标识符。 <br><br>示例：`172\_12\_53\_32\_4322\_\_123\_64\_207\_1\_80` |
| **SessionId** | Alias | 字符串 | [NetworkSessionId](#networksessionid) 的别名 |
| | | | |

### <a name="intermediary-device-fields"></a><a name="Intermediary"></a>中间设备字段

如果记录包含有关用于中继网络会话的中间设备（例如防火墙或代理）的信息，则以下字段非常有用。

| 字段 | 类 | 类型 | 说明 |
| --- | --- | --- | --- |
| **DstNatIpAddr** | 可选 | IP 地址 | 如果中间 NAT 设备报告了该值，则该值是由 NAT 设备用来与源通信的 IP 地址。<br><br>示例：`2::1` |
| **DstNatPortNumber** | 可选 | 整数 | 如果中间 NAT 设备报告了该值，则该值是由 NAT 设备用来与源通信的端口。<br><br>示例：`443` |
| **SrcNatIpAddr** | 可选 | IP 地址 | 如果中间 NAT 设备报告了该值，则该值是由 NAT 设备用来与目标通信的 IP 地址。<br><br>示例：`4.3.2.1` |
| **SrcNatPortNumber** | 可选 | 整数 | 如果中间 NAT 设备报告了该值，则该值是由 NAT 设备用来与目标通信的端口。<br><br>示例：`345` |
| **DvcInboundInterface** | 可选 | 字符串 | 如果中间设备报告了该值，则该值是由 NAT 设备用来连接到源设备的网络接口。<br><br>示例：`eth0` |
| **DvcOutboundInterface** | 可选 | String | 如果中间设备报告了该值，则该值是由 NAT 设备用来连接到目标设备的网络接口。<br><br>示例：`Ethernet adapter Ethernet 4e` |
| | | | |

### <a name="http-session-fields"></a><a name="http-session-fields"></a>HTTP 会话字段

HTTP 会话是使用 HTTP 协议的网络会话。 此类会话通常由 Web 服务器、Web 代理和 Web 安全网关报告。 下面是特定于 HTTP 会话的其他字段：

| 字段 | 类 | 类型 | 说明 |
| --- | --- | --- | --- |
| **Url** | 建议 | 字符串 | 对于 HTTP/HTTPS 网络会话，该字段表示完整的 HTTP 请求 URL（包括参数）。 当事件表示 HTTP 会话时，此字段是必填的。<br><br>示例：`https://contoso.com/fo/?k=v&amp;q=u#f` |
| **UrlCategory** | 可选 | String | URL 的已定义分组，或 URL 的域部分。 类别通常由 Web 安全网关提供，基于 URL 指向的站点的内容。<br><br>示例：搜索引擎、成人、新闻、广告和停靠域。 |
| **UrlOriginal** | 可选 | String | URL 的原始值（如果报告设备修改了 URL，并且已提供这两个值）。 |
| **HttpVersion** | 可选 | String | HTTP/HTTPS 网络连接的 HTTP 请求版本。<br><br>示例：`2.0` |
| **HttpRequestMethod** | 建议 | Enumerated | HTTP/HTTPS 网络会话的 HTTP 方法。 这些值已在 [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4) 和 [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2) 中定义，包括 `GET`、`HEAD`、`POST`、`PUT`、`DELETE`、`CONNECT`、`OPTIONS`、`TRACE` 和 `PATCH`。<br><br>示例：`GET` |
| **HttpStatusCode** | Alias | | HTTP/HTTPS 网络会话的 HTTP 状态代码。 [EventResultDetails](#eventresultdetails) 的别名。 |
| <a name="httpcontenttype"></a>HttpContentType | 可选 | 字符串 | HTTP/HTTPS 网络会话的 HTTP 响应内容类型头。 <br><br>注意：HttpContentType 字段可以包含内容格式和其他参数，例如用于获取实际格式的编码 。<br><br> 示例：`text/html; charset=ISO-8859-4` |
| HttpContentFormat | 可选 | String | [HttpContentType](#httpcontenttype) 的内容格式部分 <br><br> 示例：`text/html` |
| HttpReferrer | 可选 | String | HTTP/HTTPS 网络会话的 HTTP referrer 头。<br><br>注意：与 OSSEM 同步的 ASIM 为引用网站使用正确的拼写方式，而不使用原始 HTTP 头拼写方式。<br><br>示例：`https://developer.mozilla.org/docs` |
| <a name="httpuseragent"></a>HttpUserAgent | 可选 | 字符串 | Http/HTTPS 网络会话的 HTTP 用户代理头。<br><br>示例：<br> `Mozilla/5.0`（Windows NT 10.0；WOW64）<br>`AppleWebKit/537.36`（KHTML，例如 Gecko）<br>`Chrome/83.0.4103.97 Safari/537.36` |
| **UserAgent** | Alias | | [HttpUserAgent](#httpuseragent) 的别名 |
| **HttpRequestXff** | 可选 | IP 地址 | HTTP/HTTPS 网络会话的 HTTP X-Forwarded-For 头。<br><br>示例：`120.12.41.1` |
| **HttpRequestTime** | 可选 | 整数 | 将请求发送到服务器所花费的时间，以毫秒为单位（如果适用）。<br><br>示例：`700` |
| **HttpResponseTime** | 可选 | 整数 | 在服务器中接收响应所花费的时间，以毫秒为单位（如果适用）。<br><br>示例：`800` |
| **FileName** | 可选 | 字符串 | 对于 HTTP 上传，该字段表示上传的文件的名称。 |
| FileMD5 | 可选 | MD5 | 对于 HTTP 上传，该字段表示上传文件的 MD5 哈希。<br><br>示例：`75a599802f1fa166cdadb360960b1dd0` |
| FileSHA1 | 可选 | SHA1 | 对于 HTTP 上传，该字段表示上传文件的 SHA1 哈希。<br><br>示例：<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
| FileSHA256 | 可选 | SHA256 | 对于 HTTP 上传，该字段表示上传文件的 SHA256 哈希。<br><br>示例：<br>`e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| FileSHA512 | 可选 | SHA512 | 对于 HTTP 上传，该字段表示上传文件的 SHA512 哈希。 |
| **FileSize** | 可选 | 整数 | 对于 HTTP 上传，该字段表示上传文件的大小（以字节为单位）。 |
| FileContentType | 可选 | String | 对于 HTTP 上传，该字段表示上传文件的内容类型。 |
| | | | |

此外，将以下标准网络架构字段用于 HTTP 会话时，这些字段还需要符合其他准则：

- EventType 应为 &quot;HTTP 会话&quot;
- EventResultDetails 应设置为 HTTP 状态代码。
- 对于低于 400 的 HTTP 状态代码，EventResult 应为 &quot;Success&quot;，否则为 &quot;Failure&quot;。

### <a name="inspection-fields"></a><a name="inspection-fields"></a>检查字段

以下字段用于表示安全设备（例如防火墙、IPS 或 Web 安全网关）执行的检查：

| 字段 | 类 | 类型 | 说明 |
| --- | --- | --- | --- |
| **NetworkRuleName** | 可选 | 字符串 | 确定 [DvcAction](#dvcaction) 时所依据的规则的名称或 ID。<br><br> 示例：`AnyAnyDrop` |
| **NetworkRuleNumber** | 可选 | 整数 | 确定 [DvcAction](#dvcaction) 时所依据的规则的编号。<br><br>示例：`23` |
| **规则** | 必需 | 字符串 | `NetworkRuleName` 或 `NetworkRuleNumber` |
| <a name="dvcaction"></a>DvcAction | 可选 | Enumerated | 对网络会话执行的操作。 支持的值是：<br>- `Allow`<br>- `Deny`<br>- `Drop`<br>- `Drop ICMP`<br>- `Reset`<br>- `Reset Source`<br>- `Reset Destination`<br>- `Encrypt`<br>- `Decrypt`<br>- `VPNroute`<br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 原始值应存储在 [DvcOriginalAction](#dvcoriginalaction) 字段中。<br><br>示例：`drop` |
| <a name="dvcoriginalaction"></a>DvcOriginalAction | 可选 | 字符串 | 报告设备提供的原始 [DvcAction](#dvcaction)。 |
| **ThreatId** | 可选 | String | 在网络会话中识别到的威胁或恶意软件的 ID。<br><br>示例：`Tr.124` |
| **ThreatName** | 可选 | String | 在网络会话中识别到的威胁或恶意软件的名称。<br><br>示例：`EICAR Test File` |
| **ThreatCategory** | 可选 | 字符串 | 在网络会话中识别到的威胁或恶意软件的类别。<br><br>示例：`Trojan` |
| ThreatRiskLevel | 可选 | 整数 | 与会话关联的风险级别。 级别应是介于 0 和 100 之间的数字 。<br><br>注意：可以在源记录中使用不同的标度提供值，而使用的标度应规范化为此标度。 原始值应存储在 [ThreatRiskLevelOriginal](#threatriskleveloriginal) 中。 |
| <a name="threatriskleveloriginal"></a>ThreatRiskLevelOriginal | 可选 | String | 报告设备报告的风险级别。 |
| | | | |

### <a name="other-fields"></a>其他字段

如果事件由网络会话的某个终结点报告，则它可以包含有关发起或终止了该会话的进程的信息。 在这种情况下，可以使用 [ASIM 进程事件架构](process-events-normalization-schema.md)来规范化此信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Sentinel 中的规范化](normalization.md)
- [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)
- [Azure Sentinel 文件事件规范化架构参考（公共预览版）](file-event-normalization-schema.md)
- [Azure Sentinel DNS 规范化架构参考](dns-normalization-schema.md)
- [Azure Sentinel 进程事件规范化架构参考](process-events-normalization-schema.md)
- [Azure Sentinel 注册表事件规范化架构参考（公共预览版）](registry-event-normalization-schema.md)

