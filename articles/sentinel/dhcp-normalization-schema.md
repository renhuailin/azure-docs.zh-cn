---
title: Azure Sentinel DHCP 规范化架构参考 | Microsoft Docs
description: 本文介绍 Azure Sentinel DHCP 规范化架构。
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
ms.date: 10/04/2021
ms.author: bagol
ms.openlocfilehash: f8ad30e833e7b400c7d308b1f99dd457af438fa0
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620860"
---
# <a name="azure-sentinel-dhcp-normalization-schema-reference-public-preview"></a>Azure Sentinel DHCP 规范化架构参考（公共预览版）

DHCP 信息模型用于描述 DHCP 服务器报告的事件，Azure Sentinel 用它来启用与源无关的分析。

有关详细信息，请参阅[规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)。

> [!IMPORTANT]
> DHCP 规范化架构目前为预览版。 此功能不附带服务级别协议，不建议将其用于生产工作负荷。
>
> [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="schema-overview"></a>架构概述

ASIM DHCP 架构表示 DHCP 服务器活动，包括为 DHCP IP 地址（从客户端系统租用）请求提供服务，以及使用授予的租用更新 DNS 服务器。

DHCP 事件中最重要的字段是 [SrcIpAddr](#srcipaddr) 和 [SrcHostname](#srchostname)，DHCP 服务器通过授予租用来绑定它们，其别名分别为 [IpAddr](#ipaddr) 和 [Hostname](#hostname) 字段。 [SrcMacAddr](#srcmacaddr) 字段也很重要，因为它表示未租用 IP 地址时使用的客户端计算机。  

DHCP 服务器可能会因安全问题或网络饱和而拒绝客户端。 它也有可能通过租给客户端一个将其连接到有限网络的 IP 地址来隔离客户端。 [EventResult](#eventresult)、[EventResultDetails](#eventresultdetails) 和 [DvcAction](#dvcaction) 字段提供有关 DHCP 服务器响应和操作的信息。

租用持续时间存储在 [DhcpLeaseDuration](#dhcpleaseduration) 字段中。

## <a name="schema-details"></a>架构详细信息

ASIM 与[开源安全事件元数据 (OSSEM)](https://github.com/OTRF/OSSEM) 项目保持一致。

OSSEM 没有与 ASIM DHCP 架构类似的 DHCP 架构。

### <a name="log-analytics-fields"></a>Log Analytics 字段

Log Analytics 会为每条记录生成以下字段，你可以在[创建自定义连接器](create-custom-connector.md)时替代这些字段。

| **字段** | **类型** | **说明** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | 日期/时间 | 报告设备生成事件的时间。 |
| **\_ResourceId** | GUID | 报告设备或服务的 Azure 资源 ID，或使用 Syslog、CEF 或 WEF 转发的事件的日志转发器资源 ID。 |
| **类型** | String | 从中提取记录的原始表。 当同一事件可通过多个通道传入不同的表，并且具有相同的 [EventVendor](#eventvendor) 和 [EventProduct](#eventproduct) 值时，此字段很有用。<br><br>例如，Sysmon 事件可以传入 Event 表或 WindowsEvent 表。 |
| | | |

> [!NOTE]
> [Azure Monitor](../azure-monitor/logs/log-standard-columns.md) 记录了更多 Log Analytics 字段，但与安全性的相关性较低。
> 

### <a name="event-fields"></a>事件字段

事件字段通用于所有架构，描述活动本身和报告设备。

| 字段 | **类** | 类型  | **讨论 (Discussion)** |
| --- | --- | --- | --- |
| **EventMessage** | 可选 | 字符串 | 一般消息或说明，包含在记录中或者根据记录生成。 |
| “EventCount” | 必需 | Integer | 记录描述的事件数。<br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。<br><br>对于其他源，应将其设置为 `1`。<br><br>示例： `1`|
| **EventStartTime** | 必需 | 日期/时间 | 如果源支持聚合且记录表示多个事件，则使用此字段指定生成第一个事件的时间。 <br><br>在其他情况下，它是 [TimeGenerated](#timegenerated) 字段的别名。 |
| **EventEndTime** | Alias || [TimeGenerated](#timegenerated) 字段的别名。 |
| **EventType** | 必需 | Enumerated | 指示记录报告的操作。 <br><Br> 可能的值为 `Assign`、`Renew`、`Release` 和 `DNS Update`。 <br><br>示例： `Assign`| 
| <a name="eventresult"></a>EventResult | 必需 | Enumerated | 以下值之一：`Success`、`Partial`、`Failure`、`NA`（不适用）。<br> <br>可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 或者，源记录可能只提供 [EventResultDetails](#eventresultdetails) 字段，应对该字段进行分析以得出 EventResult 值。 当客户端被 DHCP 服务器隔离时，此字段应设置为 `Partial`。<br><br>示例： `Success`|
| <a name="eventresultdetails"></a>EventResultDetails | Alias | | EventResult](#eventresult) 字段中报告的结果的原因或详细信息。 <br><Br> 可能值为 `Exhausted`、`Quarantined` 和 `Denied`。 <br><br>示例： `Exhausted` |
| EventOriginalResultDetails    | 可选    | 字符串     |  [EventResultDetails](#eventresultdetails) 的原始记录中提供的值（如果已由源提供）。 对于 Windows DHCP 服务器日志，请在此处存储 QResult 字段值。 |
| **EventOriginalUid** | 可选 | 字符串 | 原始记录的唯一 ID（如果已由源提供）。 |
| **EventOriginalType**   | 可选    | 字符串  |  原始事件类型或 ID（如果已由源提供）。<br><br>示例： `DNS Assign Failed` |
| <a name ="eventproduct"></a>“EventProduct” | 必需 | 字符串 | 生成事件的产品。 该字段在源记录中可能不可用，在这种情况下，它应由分析程序设置。 <br><br>示例： `DHCP Server` |
| **EventProductVersion** | 可选 | 字符串 | 生成事件的产品的版本。 该字段在源记录中可能不可用，在这种情况下，它应由分析程序设置。 <br><br>示例： `12.1` |
| <a name="eventvendor"></a>**EventVendor** | 必需 | 字符串 | 生成事件的产品的供应商。 该字段在源记录中可能不可用，在这种情况下，它应由分析程序设置。<br><br>示例： `Microsoft`|
| **EventSchemaVersion** | 必需 | 字符串 | 此处所述的架构版本为 0.1.0。 |
| EventSchema | 必需 | 字符串 | 此处所述的架构名称为 Dhcp。 |
| **EventReportUrl** | 可选 | 字符串 | 在资源的事件中提供的 URL，提供有关该事件的更多信息。 |
| <a name="dvc"></a>**Dvc** | Alias | 字符串 | DHCP 服务器的唯一标识符。<br><br>示例： `ContosoDc.Contoso.Azure`<br><br>此字段可能又称为 [DvcFQDN](#dvcfqdn)、[DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确的设备的云源，请使用与 [EventProduct](#eventproduct) 字段相同的值。 |
| <a name="dvcipaddr"></a>DvcIpAddr | 建议 | IP 地址 | DHCP 服务器的 IP 地址。<br><br>示例： `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>“DvcHostname” | 必需 | 字符串 | DHCP 服务器的主机名，不包括域信息。 如果没有可用的设备名称，请在此字段中存储相关的 IP 地址。<br><br>示例： `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | 建议 | 字符串 | DHCP 服务器的域。<br><br>示例： `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | 建议 | Enumerated | [DvcDomain](#dvcdomain) 的类型（如果已知）。 可能的值包括：<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>注意：如果使用了 [DvcDomain](#dvcdomain) 字段，则此字段是必填的。 |
| <a name="dvcfqdn"></a>**DvcFQDN** | 可选 | String | DHCP 服务器的主机名，包括域信息（如果可用）。 <br><br> 示例： `Contoso\DESKTOP-1282V4D`<br><br>注意：此字段支持传统的 FQDN 格式和 Windows 域\主机名格式。 [DvcDomainType](#dvcdomaintype) 字段反映使用的格式。  |
| <a name="dvcid"></a>“DvcId” | 可选 | String | 记录中报告的 DHCP 服务器的 ID。<br><br>示例：`ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | 可选 | Enumerated | [DvcId](#dvcid) 的类型（如果已知）。 可能的值包括：<br> - `AzureResourceId`<br>- `MDEid`<br><br>如果有多个可用 ID，请使用列表中的第一个 ID，并分别使用字段名称 DvcAzureResourceId 和 DvcMDEid 存储其他 ID 。<br><br>注意：如果使用了 [DvcId](#dvcid) 字段，则此字段是必填的。 |
| **EventSeverity** | 可选 | Enumerated | 如果 DHCP 服务器隔离了客户端，则设置为 `Low`；如果服务器阻止了客户端，则设置为 `Medium`。 否则设置为 `Informational`。 <br><br>示例： `Informational`|
| <a name="dvcaction"></a>**DvcAction** | 可选 | Enumerated | DHCP 服务器采取的操作。 可能值为 `Allow`、`Deny` 和 `Quarantine`。<br><br>示例： `Deny` |
| <a name="additionalfields"></a>**AdditionalFields** | 可选 | 动态 | 如果源提供了值得保留的附加信息，请使用原始字段名称保留这些信息，或者创建 AdditionalFields 动态字段，并在其中以键/值对的形式添加这些附加信息。 |
| | | | |

### <a name="dhcp-specific-fields"></a>特定于 DHCP 的字段

以下字段特定于 DHCP 事件，但有许多类似于其他架构中的字段，并遵循相同的命名约定。

| 字段 | **类** | 类型 | **说明** |
| --- | --- | --- | --- |
| <a name="srcipaddr"></a>**SrcIpAddr** | 必需 | IP 地址 | DHCP 服务器分配给客户端的 IP 地址。<br><br>示例： `192.168.12.1` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | [SrcIpAddr](#srcipaddr) 的别名 |
| <a name="requestedipaddr"></a>**RequestedIpAddr** | 可选 | IP 地址 | DHCP 客户端请求的 IP 地址（如果可用）。<br><br>示例： `192.168.12.3` |
| <a name="srchostname"></a>**SrcHostname** | 必需 | 字符串 | 请求 DHCP 租用的设备的主机名。 如果没有可用的设备名称，请在此字段中存储相关的 IP 地址。<br><br>示例： `DESKTOP-1282V4D` |
| <a name="hostname"></a>**Hostname** | Alias | | [SrcHostname](#srchostname) 的别名 |
| <a name="srcdomain"></a> **SrcDomain** | 建议 | 字符串 | 源设备的域。<br><br>示例： `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | 建议 | Enumerated | [SrcDomain](#srcdomain) 的类型（如果已知）。 可能的值包括：<br>- `Windows`（例如：`contoso`）<br>- `FQDN`（例如：`microsoft.com`）<br><br>如果使用了 [SrcDomain](#srcdomain)，则该字段是必填的。 |
| **SrcFQDN** | 可选 | String | 源设备主机名，包括域信息（如果可用）。 <br><br>注意：此字段支持传统的 FQDN 格式和 Windows 域\主机名格式。 [SrcDomainType](#srcdomaintype) 字段反映使用的格式。 <br><br>示例： `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | 可选 | 字符串 | 记录中报告的源设备的 ID。<br><br>例如：`ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | 可选 | Enumerated | [SrcDvcId](#srcdvcid) 的类型（如果已知）。 可能的值包括：<br> - `AzureResourceId`<br>- `MDEid`<br><br>如果有多个可用 ID，请使用上述列表中的第一个 ID，并分别在 SrcDvcAzureResourceId 和 SrcDvcMDEid 中存储其他 ID 。<br><br>注意：如果使用了 [SrcDvcId](#srcdvcid)，则此字段是必填的。 |
| **SrcDeviceType** | 可选 | Enumerated | 源设备的类型。 可能的值包括：<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | 可选 | String | 源用户的计算机可读的唯一字母数字表示形式。 格式和支持的类型包括：<br>- SID (Windows)：`S-1-5-21-1377283216-344919071-3415362939-500`<br>- UID (Linux)：`4578`<br>- AADID (Azure Active Directory)：`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>- OktaId：`00urjk4znu3BcncfY0h7`<br>- AWSId：`72643944673`<br><br>在 [SrcUserIdType](#srcuseridtype) 字段中存储 ID 类型。 如果其他 ID 可用，建议将字段名称分别规范化为 SrcUserSid、SrcUserUid、SrcUserAadId、SrcUserOktaId 和 UserAwsId。<br><br>示例： `S-1-12` |
| <a name="srcuseridtype"></a>**SrcUserIdType** | 可选 | Enumerated | [SrcUserId](#srcuserid) 字段中存储的 ID 的类型。 支持的值包括：`SID`、`UIS`、`AADID`、`OktaId` 和 `AWSId`。 |
| <a name="srcusername"></a>**SrcUsername** | 可选 | String | 源用户名，包括域信息（如果可用）。 使用以下格式之一并遵循以下优先顺序：<br>- UPN/电子邮件：`johndow@contoso.com`<br>- Windows：`Contoso\johndow`<br>- DN：`CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- 简单：`johndow`。 仅当未提供域信息时才使用简单格式。<br><br>在 [SrcUsernameType](#srcusernametype) 字段中存储用户名类型。 如果其他 ID 可用，建议将字段名称规范化为 SrcUserUpn、SrcUserWindows 和 SrcUserDn  。<br><br>有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。<br><br>示例： `AlbertE` |
| **用户名** | Alias | | [SrcUsername](#srcusername) 的别名 |
| <a name="srcusernametype"></a>**SrcUsernameType** | 可选 | Enumerated | 指定 [SrcUsername](#srcusername) 字段中存储的用户名的类型。 支持的值为 `UPN`、`Windows`、`DN` 和 `Simple`。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。<br><br>示例： `Windows` |
| **SrcUserType** | 可选 | Enumerated | 操作者的类型。 允许值包括：<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 在 [EventOriginalUserType](#srcoriginalusertype) 字段中存储原始值。 |
| <a name="srcoriginalusertype"></a>**SrcOriginalUserType** | | | 原始源用户类型（如果源已提供）。 |
| <a name="srcmacaddr"></a>**SrcMacAddr** | 必需 | MAC 地址 | 请求 DHCP 租用的客户端的 MAC 地址。 <br><br>注意：Windows DHCP 服务器以非标准方式记录 MAC 地址，省略应该由分析程序插入的冒号。<br><br>示例： `06:10:9f:eb:8f:14` |
| <a name="dhcpleaseduration"></a>**DhcpLeaseDuration** | 可选 | 整数 | 授予客户端的租用时长，以秒为单位。 |  
|<a name="dhcpsessionid"></a>**DhcpSessionId** | 可选 | string | 报告设备报告的会话标识符。 对于 Windows DHCP 服务器，请将此字段设置为 TransactionID 字段。 <br><br>示例： `2099570186` |
| **SessionId** | Alias | 字符串 | [DhcpkSessionId](#dhcpsessionid) 的别名 |
| <a name="dhcpsessionduration"></a>**DhcpSessionDuration** | 可选 | 整数 | 完成 DHCP 会话所花费的时间，以毫秒为单位。<br><br>示例： `1500` |
| **持续时间** | Alias | | [DhcpSessionDuration](#dhcpsessionduration) 的别名 |
| **DhcpSrcDHCId** | 可选 | String | [RFC4701](https://datatracker.ietf.org/doc/html/rfc4701) 定义的 DHCP 客户端 ID |
| **DhcpCircuitId** | 可选 | String | [RFC3046](https://datatracker.ietf.org/doc/html/rfc3046) 定义的 DHCP 线路 ID |
| **DhcpSubscriberId** | 可选 | String | [RFC3993](https://datatracker.ietf.org/doc/html/rfc3993) 定义的 DHCP 订阅者 ID |
| **DhcpVendorClassId**  | 可选 | String | [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925) 定义的 DHCP 供应商类 ID。 |
| **DhcpVendorClass**  | 可选 | String | [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925) 定义的 DHCP 供应商类。|
| **DhcpUserClassId**  | 可选 | String | [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004) 定义的 DHCP 用户类 ID。|
| **DhcpUserClass** | 可选 | String | [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004) 定义的 DHCP 用户类。|
| | | | |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- 观看 [ASIM 网络研讨会](https://www.youtube.com/watch?v=WoGD-JeC7ng)或查看[幻灯片](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Azure Sentinel 信息模型架构](normalization-about-schemas.md)
- [Azure Sentinel 信息模型分析程序](normalization-about-parsers.md)
- [Azure Sentinel 信息模型内容](normalization-content.md)