---
title: Azure Sentinel 信息模型 (ASIM) 架构 | Microsoft Docs
description: 本文介绍了 Azure Sentinel 信息模型 (ASIM) 架构，及其如何帮助 ASIM 规范来自众多不同源的数据，以便呈现统一的内容
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
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: 5474dbce356ab8bb4b07ffcc5bd9facadd7134e0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430029"
---
# <a name="azure-sentinel-information-model-asim-schemas-public-preview"></a>Azure Sentinel 信息模型 (ASIM) 架构（公共预览版）

[ASIM](normalization.md) 架构是表示活动的一组字段。 在查询中使用来自规范化架构的字段可确保查询适用于每个规范化源。

架构参考概述了构成每个架构的字段。 ASIM 当前定义了以下架构：

 - [网络会话](normalization-schema.md)
 - [DNS 活动](dns-normalization-schema.md)
 - [进程事件](process-events-normalization-schema.md)
 - [身份验证事件](authentication-normalization-schema.md)
 - [注册表事件](registry-event-normalization-schema.md)
 - [文件活动](file-event-normalization-schema.md)

> [!IMPORTANT]
> ASIM 当前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="schema-concepts"></a>架构概念

以下概念有助于理解架构参考文档，并在数据包含架构未涵盖的信息时以规范化方式扩展架构。


|概念  |说明  |
|---------|---------|
|**字段名**     |   每个架构的核心部分是其字段名称。 字段名称属于以下组： <br><br>- 所有架构通用的字段 <br>- 特定于架构的字段 <br>- 表示参与架构的实体（例如用户）的字段。 表示实体的字段在[不同的架构中是类似的](#entities)。 <br><br>如果源中的字段未在记录的架构中提供，这些字段将被规范化以保持一致。 如果这些额外的字段表示实体，则会根据实体字段准则将这些字段规范化。 否则，架构将会尽量在所有架构中保持一致。<br><br> 例如，虽然 DNS 服务器活动日志不提供用户信息，但终结点中的 DNS 活动日志可以包含可根据用户实体准则规范化的用户信息。      |
|**字段类型**     |  每个架构字段有一个类型。 Log Analytics 工作区具有有限的一组数据类型。 因此，Azure Sentinel 对许多架构字段使用某种逻辑类型，Log Analytics 不强制要求使用该类型，但该类型是为了实现架构兼容性而必须使用的。 逻辑字段类型可确保值和字段名称在不同的源中保持一致。  <br><br>有关详细信息，请参阅[逻辑类型](#logical-types)。     |
|字段类     |字段可以有多个类，这些类定义了字段何时应由分析器实现： <br><br>-    必需的字段必须出现在每个分析器中。 如果源未提供此值的信息，或者无法添加数据，则源不支持引用规范化架构的大多数内容项。<br>-  建议的字段（如果可用）应规范化。 但是，它们可能不会在每个源中提供，并且引用该规范化架构的任何内容项都应考虑到可用性。 <br>-  可选字段（如果可用）可以规范化或保持其原始形式。 通常，出于性能方面的原因，精简的分析器不会将其规范化。    |
|**实体**     | 事件围绕实体（例如用户、主机、进程或文件）进行演变，每个实体可能需要通过多个字段来描述自身。 例如，主机可以有名称和 IP 地址。 <br><br>单个记录可以包括相同类型的多个实体，例如源主机和目标主机。 <br><br>Azure Sentinel 信息模型定义了如何一致地描述实体，而实体允许扩展架构。 <br><br>例如，虽然网络会话架构不包括进程信息，但某些事件源确实提供可添加的进程信息。 有关详细信息，请参阅[实体](#entities)。 |
|**别名**     |  在某些情况下，不同的用户要求某个字段具有不同的名称。 例如，在 DNS 术语中，用户需要一个名为 `query` 的字段，但更常见的情况是它有一个域名。 别名允许为指定的值使用多个名称，从而解决了此问题。 别名类与其别名化的字段相同。       |
| | |

## <a name="logical-types"></a>逻辑类型

每个架构字段有一个类型。 某些字段具有内置的 Azure Log Analytics 类型，例如 `string`、`int`、`datetime` 或 `dynamic`。 其他字段具有逻辑类型，该类型表示字段值的规范化方式。

|数据类型  |物理类型  |格式和值  |
|---------|---------|---------|
|**布尔值**     |   Bool      |    使用原生 KQL 布尔数据类型，而不要使用布尔值的数字或字符串表示形式。     |
|枚举     |  字符串       |   为字段显式定义的值列表。 架构定义列出了接受的值。      |
|**日期/时间**     |  根据引入方法功能，按优先级的降序使用以下任一物理表示形式： <br><br>- Log Analytics 内置的日期时间类型 <br>- 使用 Log Analytics 日期时间数字表示形式的整数字段。 <br>- 使用 Log Analytics 日期时间数字表示形式的字符串字段 <br>- 用于存储受支持 [Log Analytics 日期/时间格式](/azure/data-explorer/kusto/query/scalar-data-types/datetime)的字符串字段。       |  [Log Analytics 日期和时间表示形式](/azure/kusto/query/scalar-data-types/datetime)与 Unix 时间的表示形式类似，但两者实际上是不同的。 有关详细信息，请参阅[转换指南](/azure/kusto/query/datetime-timespan-arithmetic)。 <br><br>注意：在适用的情况下，应调整时间的时区。 |
|**MAC 地址**     |  String       | 冒分十六进制表示法        |
|**IP 地址**     |字符串         |    Azure Sentinel 架构没有单独的 IPv4 和 IPv6 地址。 任一 IP 地址字段都可以包含 IPv4 地址或 IPv6 地址，如下所述： <br><br>- 采用点分十进制表示法的 IPv4  <br>- 采用 8 个 16 位段表示法的 IPv6，允许短格式<br><br>例如：<br>`192.168.10.10` (IPv4)<br>`FEDC:BA98:7654:3210:FEDC:BA98:7654:3210` (IPv6)<br>`1080::8:800:200C:417A`（IPv6 短格式）     |
|**FQDN**        |   string      |    使用点分表示法的完全限定域名，例如 `docs.microsoft.com` |
|**国家/地区**     |   字符串      |    根据以下优先级使用 [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html) 的字符串： <br><br> - Alpha-2 代码，例如 `US` 表示美国 <br> - Alpha-3 代码，例如 `USA` 表示美国 <br>- 短名称<br><br>在[国际标准化组织 (ISO) 网站](https://www.iso.org/obp/ui/#search)上可以找到代码列表|
|**区域**     | 字符串        |   使用 ISO 3166-2 的国家/地区辖区名称<br><br>在[国际标准化组织 (ISO) 网站](https://www.iso.org/obp/ui/#search)上可以找到代码列表|
|**城市**     |  字符串       |         |
|**经度**     | Double        |  ISO 6709 坐标表示形式（带符号的十进制数）       |
|**纬度**     | Double        |    ISO 6709 坐标表示形式（带符号的十进制数）     |
|**MD5**     |    字符串     |  32 个十六进制字符       |
|**SHA1**     |   字符串      | 40 个十六进制字符        |
|**SHA256**     | 字符串        |  64 个十六进制字符       |
|**SHA512**     |   字符串      |  128 个十六进制字符       |
| | |

## <a name="common-fields"></a>通用字段

下面是所有 ASIM 架构通用的字段。 此处既列出了通用字段，也列出了每个架构支持的字段值的详细信息。 例如，EventType 字段的值可能因每个架构而异，EventSchemaVersion 字段的值同样如此 。 

| 字段               | 类       | 类型       |  说明        |
|---------------------|-------------|------------|--------------------|
| <a name="timegenerated"></a>TimeGenerated | 内置 | datetime | 报告设备生成事件的时间。|
| _ResourceId   | 内置 |  GUID     | 报告设备或服务的 Azure 资源 ID，或使用 Syslog、CEF 或 WEF 转发的事件的日志转发器资源 ID。 |
| **Type** | 内置 | String | 从中提取记录的原始表。 当同一事件可以通过两个通道传入不同的表，但具有相同的 `EventVendor` 和 `EventProduct` 时，此字段很有用。 例如，Sysmon 事件可以传入 Event 表或 SecurityEvent 表。 |
| **EventMessage**        | 可选    | 字符串     |     一般消息或说明，包含在记录中或者从记录生成。   |
| “EventCount”          | 必需   | 整数    |     记录描述的事件数。 <br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。 <br><br>对于其他源，设置为 `1`。   |
| **EventStartTime**      | 必需   | 日期/时间  |      如果源支持聚合且记录表示多个事件，则此字段将指定生成第一个事件的时间。 <br><br>否则，此字段将别名化 [TimeGenerated](#timegenerated) 字段。 |
| **EventEndTime**        | 必需   | Alias      |      [TimeGenerated](#timegenerated) 字段的别名。    |
|  <a name=eventtype></a>EventType           | 必需   | Enumerated |    描述记录报告的操作。 每个架构将记录此字段的有效值列表。 |
| **EventSubType** | 可选 | Enumerated | 描述 [EventType](#eventtype) 字段中报告的操作的细分。 每个架构将记录此字段的有效值列表。 |
| <a name="eventresult"></a>EventResult | 必需 | Enumerated | 以下值之一：Success、Partial、Failure、NA（不适用）   。<br> <br>可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 或者，源可以只提供 [EventResultDetails](#eventresultdetails) 字段，应分析该字段以派生 EventResult 值。<br><br>示例： `Success`|
| <a name=eventresultdetails></a>EventResultDetails | 必需 | Alias | [EventResult](#eventresult) 字段中报告的结果的原因或详细信息。 每个架构将记录此字段的有效值列表。<br><br>示例： `NXDOMAIN`|
| **EventOriginalUid**    | 可选    | 字符串     |   原始记录的唯一 ID（如果已由源提供）。<br><br>示例： `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| “EventOriginalType”   | 可选    | 字符串     |   原始事件类型或 ID（如果已由源提供）。 例如，此字段用于存储原始 Windows 事件 ID。<br><br>示例： `4624`|
| <a name ="eventproduct"></a>“EventProduct”        | 必需   | 字符串     |             生成事件的产品。 <br><br>示例： `Sysmon`<br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。           |
| **EventProductVersion** | 可选    | 字符串     | 生成事件的产品的版本。 <br><br>示例： `12.1`      |
| **EventVendor**         | 必需   | 字符串     |           生成事件的产品的供应商。 <br><br>示例： `Microsoft`  <br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。  |
| **EventSchemaVersion**  | 必需   | 字符串     |    架构的版本。 每个架构将记录其当前版本。         |
| **EventReportUrl**      | 可选    | 字符串     | 在资源的事件中提供的 URL，提供有关该事件的其他信息。|
| “Dvc” | 必需       | 字符串     |               发生该事件的设备的唯一标识符。 <br><br>此字段可以别名化 [DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确设备的云源，请使用与 [Event Product](#eventproduct) 字段相同的值。           |
| <a name ="dvcipaddr"></a>“DvcIpAddr”           | 建议 | IP 地址 |         发生该事件的设备的 IP 地址。  <br><br>示例： `45.21.42.12`    |
| <a name ="dvchostname"></a>“DvcHostname”         | 建议 | 主机名   |               发生该事件的设备的主机名。 <br><br>示例： `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>“DvcId”               | 可选    | 字符串     |  发生该事件的设备的唯一 ID。 <br><br>示例： `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 可选    | MAC        |   发生该事件的设备的 MAC 地址。  <br><br>示例： `00:1B:44:11:3A:B7`       |
| “DvcOs”               | 可选    | 字符串     |         发生该事件的设备上运行的操作系统。    <br><br>示例： `Windows`    |
| “DvcOsVersion”        | 可选    | 字符串     |   发生该事件的设备上的操作系统版本。 <br><br>示例： `10` |
| **AdditionalFields**    | 可选    | 动态    | 如果源提供了值得保留的附加信息，请使用原始字段名称保留这些信息，或者创建动态 AdditionalFields 字段，并在其中以键/值对的形式添加这些附加信息。    |
| | | | |

> [!NOTE]
> Log Analytics 还会添加与安全用例不太相关的其他字段。 有关详细信息，请参阅 [Azure Monitor 日志中的标准列](/azure/azure-monitor/logs/log-standard-columns)。
>


## <a name="entities"></a>实体

事件围绕用户、主机、进程或文件等实体进行演变。 实体表示形式允许将相同类型的多个实体包含在单个记录中，并支持相同实体的多个属性。

为了启用实体功能，实体表示形式遵守以下准则：

|准则  |说明  |
|---------|---------|
|描述符和别名     | 由于单个事件通常包括相同类型的多个实体（例如源主机和目标主机），因此描述符用作前缀来标识与特定实体关联的所有字段。 <br><br>为了保持规范化，Azure Sentinel 信息模型使用少量的标准描述符，并为实体的特定角色选择最合适的描述符。  <br><br>如果某种类型的单个实体与某个事件相关，则无需使用描述符。 此外，一组不带描述符的字段将别名化每种类型的最常用实体。  |
|标识符和类型     | 规范化架构允许为每个实体使用多个标识符，我们预期这些标识符将在事件中共存。 如果源事件具有无法映射到规范化架构的其他实体标识符，请将这些标识符保留为源的形式，或使用 `AdditionalFields` 动态字段。 <br><br>若要维护标识符的类型信息，在适用的情况下，请将类型存储在具有相同名称且后缀为 `Type` 的字段中。 例如 `UserIdType`。         |
|**特性**     |   实体通常具有其他不用作标识符的属性，并且也可以使用描述符对其进行限定。 例如，如果源用户具有域信息，则规范化字段为 `SrcUserDomain`。      |
| | |

每个架构显式定义了中心实体和实体字段。 以下指南可帮助用户了解中心架构字段，以及如何使用架构中未显式定义的其他实体或实体字段，以规范化方式扩展架构。

### <a name="the-user-entity"></a>用户实体

用于用户的描述符是“参与者”、“目标用户”和“已更新用户”，如以下方案中所述  ：

|活动  |完整方案  |用于别名化的单个实体方案  |
|---------|---------|---------|
|**创建用户**     |  参与者已创建或已修改目标用户        |  已创建（目标）用户。       |
|**修改用户**     |   参与者已将“目标用户”重命名为“已更新用户”  。 已更新用户的所有信息通常不与某个用户关联，并且与目标用户之间存在一定的重叠 。      |         |
|**网络连接**     |    在源主机上作为参与者运行的进程，与目标主机上作为目标用户运行的进程通信      |         |
|DNS 请求     | 参与者启动了 DNS 查询        |         |
|**登录**     |    参与者以目标用户的身份登录到了系统 。     |（目标）用户已登录         |
|进程创建     |   参与者（与启动进程关联的用户）启动了进程创建。 创建的进程通过目标用户（与目标进程相关的用户）的凭据运行。      |  创建的进程通过（目标）用户的凭据运行。       |
|**Email**     |     参与者将电子邮件发送到目标用户     |         |
| | | |

下表描述了支持的用户标识符：

|规范化字段  |类型  |格式和支持的类型  |
|---------|---------|---------|
|**UserId**     |    字符串     |   系统中用户的计算机可读的唯一字母数字表示形式。 <br><br>格式和支持的类型包括：<br>    - SID (Windows)：`S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  UID (Linux)：`4578`<br>    -    AADID (Azure Active Directory)：`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - OktaId：`00urjk4znu3BcncfY0h7`<br>    - AWSId：`72643944673`<br><br>    将 ID 类型存储在 `UserIdType` 字段中。 如果其他 ID 可用，我们建议将字段名称分别规范化为 `UserSid`、`UserUid`、`UserAADID`、`UserOktaId` 和 `UserAwsId`。       |
|**用户名**     |  字符串       |   采用以下格式之一并遵循以下优先顺序的用户名（适用的情况下包括域信息）： <br> -   UPN/电子邮件：`johndow@contoso.com` <br>  -    Windows：`Contoso\johndow` <br> -   DN：`CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - 简单：`johndow`。 仅当未提供域信息时才使用此格式。 <br><br> 将 Username 类型存储在 `UsernameType` 字段中。    |
| | | |


### <a name="the-process-entity"></a>进程实体

用于用户的描述符是 `Acting Process`、`Target Process` 和 `Parent Process`，如以下方案中所述：

- 网络连接。 操作进程启动了网络连接，以便与远程系统上的目标进程通信 。
- DNS 请求。  操作进程启动了 DNS 查询
- 登录。  操作进程启动了远程系统登录，该系统代表该进程运行了目标进程 。
- 进程创建。 操作进程启动了目标进程创建 。 父进程是操作进程的父级。

下表描述了支持的进程标识符：

|规范化字段  |类型  |格式和支持的类型  |
|---------|---------|---------|
|**Id**     |    字符串     |   OS 分配的进程 ID。      |
|**Guid**     |  字符串       |   OS 分配的进程 GUID。 GUID 在每次重启系统后通常会保持唯一，而 ID 经常会重复使用。   |
|**Path**     |    字符串     |   进程的完整路径名，包括目录和文件名。       |
|**名称**     |  Alias       |  进程名称是路径的别名。   |
| | | |


有关详细信息，请参阅 [Azure Sentinel 进程事件规范化架构参考（公共预览版）](process-events-normalization-schema.md)。

### <a name="the-device-entity"></a>设备实体

规范化架构尽可能地与用户直觉保持一致，因此它会根据方案以各种方式处理设备：

- 当事件上下文表示源设备和目标设备时，将使用 `Src` 和 `Target` 描述符。 在这种情况下，`Dvc` 描述符用于报告设备。

- 对于单设备事件（例如本地 OS 事件），将使用 `Dvc` 描述符。

- 如果事件中引用了另一个网关设备，并且值与报告设备不同，则使用 `Gateway` 描述符。

下面对设备处理准则做了进一步的澄清：

- 网络连接。 已建立从源设备 (`Src`) 到目标设备 (`Target`) 的连接 。 （报告）设备 (`Dvc`) 已报告连接。
- 代理网络连接。 已通过网关设备 (`Gateway`) 建立从源设备 (`Src`) 到目标设备 (`Target`) 的连接  。 （报告）设备已报告连接。
- DNS 请求。  已从源设备 (`Src`) 启动 DNS 查询。
- 登录：已启动从源设备 (`Src`) 到目标设备 (`Target`) 上的远程系统的登录  。
- 进程：已在设备 (`Dvc`) 上启动进程 。

下表描述了支持的设备标识符：

|规范化字段  |类型  |格式和支持的类型  |
|---------|---------|---------|
|**主机名**     |    字符串     |        |
|**FQDN**     |  字符串       |   完全限定的域名   |
|IpAddr     |    IP 地址     |   虽然设备可以有多个 IP 地址，但事件通常只有一个标识 IP 地址。 例外的情况是网关设备，它可以有两个相关的 IP 地址。 对于网关设备，请使用 `UpstreamIpAddr` 和 `DownstreamIpAddr`。      |
|HostId     |  字符串       |     |
| | | |


> [!NOTE]
> `Domain` 是设备的典型属性，但不是完整标识符。
>

有关详细信息，请参阅 [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)。

### <a name="sample-entity-mapping"></a>示例实体映射

本部分使用 [Windows 事件 4624](/windows/security/threat-protection/auditing/event-4624) 作为示例来介绍如何规范化 Azure Sentinel 的事件数据。

此事件具有以下实体：

|Microsoft 术语  |原始事件字段前缀 |ASIM 字段前缀  |说明  |
|---------|---------|---------|---------|
|**主题**     | `Subject`        |   `Actor`      |  报告有关成功登录的信息的用户。      |
|新登录     |    `Target`     |  `TargetUser`       |  执行登录的用户。       |
|**处理**     |    -     |     `ActingProcess`    |   尝试登录的进程。      |
|网络信息     |   -      |   `Src`      |     从中尝试执行登录的计算机。    |
| | | | |


[Windows 事件 4624](/windows/security/threat-protection/auditing/event-4624) 基于这些实体规范化为以下字段（某些字段是可选的）：

|规范化字段  |原始字段  |示例中的值  |说明  |
|---------|---------|---------|---------|
|ActorUserId     |  SubjectUserSid       |  S-1-5-18        |        |
|ActorUserIdType     |  -       | SID        |         |
|ActorUserName     |   SubjectDomainName\ SubjectUserName      |  WORKGROUP\WIN-GG82ULGC9GO$       |  通过串联两个字段来生成       |
|ActorUserNameType     |   -      |   Windows      |         |
|ActorSessionId     | SubjectLogonId        |  0x3e7       |         |
|TargetUserId     |   TargetUserSid      |    S-1-5-21-1377283216-344919071-3415362939-500     |         |
|**UserId**     |    TargetUserSid     |   alias      |         |
|TargetUserIdType     |   -      |    SID     |         |
|**TargetUserName**     | TargetDomainName\ TargerUserName        |   Administrator\WIN-GG82ULGC9GO$      |   通过串联两个字段来生成      |
|**用户名**     |  TargetDomainName\ TargerUserName       |  alias       |         |
|TargetUserNameType     | -        |  Windows       |         |
|TargetSessionId     |   TargetLogonId      |  0x8dcdc       |         |
|ActingProcessName     |  ProcessName       |  C:\\Windows\\System32\\svchost.exe       |         |
|ActingProcessId     |    ProcessId     |     0x44c    |         |
|SrcHostname     |    WorkstationName     | Windows        |         |
|**SrcIpAddr**     |  IpAddress       |     127.0.0.1    |         |
|**SrcPortNumber**     |  IpPort       |  0       |         |
|TargetHostname     |   Computer      |  WIN-GG82ULGC9GO           |         |
|**主机名**     |     Computer    |     Alias    |         |
| | | | |


## <a name="next-steps"></a>后续步骤

本文概述了 Azure Sentinel 和 Azure Sentinel 信息模型中所涉及的规范化。

有关详细信息，请参阅：

- [Azure Sentinel 信息模型概述](normalization.md)
- [Azure Sentinel 信息模型分析器](normalization-about-parsers.md)
- [Azure Sentinel 信息模型内容](normalization-content.md)
