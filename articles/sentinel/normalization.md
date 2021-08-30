---
title: 规范化和 Azure Sentinel 信息模型 (ASIM) | Microsoft Docs
description: 本文介绍 Azure Sentinel 如何使用 Azure Sentinel 信息模型 (ASIM) 规范化来自多种不同源的数据
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
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: c8bf2fd28a1b5adee4d028c3dc11b771d48ef295
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179329"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim"></a>规范化和 Azure Sentinel 信息模型 (ASIM)

Azure Sentinel 从多种源引入数据。 将各种数据类型和表一起处理时，需要了解每种数据类型和表，并为每种类型或架构编写/使用唯一的分析规则、工作簿和搜寻查询集。 有时，即使数据类型共享通用元素（例如防火墙设备），也需要单独的规则、工作簿和查询。 在调查和搜寻期间，在不同类型的数据之间进行关联也可能有难度。

Azure Sentinel 信息模型 (ASIM) 通过以下方式，提供一种无缝的体验以用于在统一的规范化视图中处理各种源：

- 允许与源无关的内容和解决方案。
- 简化 Azure Sentinel 工作区中数据的分析和使用。
- 使用查询时分析，同时最大程度地降低对性能的影响。


本文将介绍 Azure Sentinel 信息模型，以及如何创建规范化分析器以将非规范化数据转换为信息模型规范化架构。 你还可以开发内容以使用规范化架构，以及转换现有内容以使用规范化架构。

> [!NOTE]
> Azure Sentinel 信息模型与[开源安全事件元数据 (OSSEM)](https://ossemproject.com/intro.html) 通用信息模型相一致，可以在规范化表之间实现可预测的实体关联。 OSSEM 是社区导向型项目，主要注重于记录和标准化来自不同数据源与操作系统的安全事件日志。 该项目还提供一个通用信息模型 (CIM)，可供数据工程师在数据建模过程中使用，从而使安全分析师可以查询和分析不同数据源中的数据。
>
> 有关详细信息，请参阅 [OSSEM 参考文档](https://ossemproject.com/cdm/guidelines/entity_structure.html)。
>
## <a name="azure-sentinel-information-model-components"></a>Azure Sentinel 信息模型组件

Azure Sentinel 信息模型包括以下组件：

|组件  |说明  |
|---------|---------|
|规范化架构     |   涵盖构建统一功能时可用的标准可预测事件类型集。 <br><br>每个架构定义了表示事件的字段、规范化列命名约定，以及字段值的标准格式。 <br><br> ASIM 当前定义了以下架构：<br> - [网络会话](normalization-schema.md)<br> - [DNS 活动](dns-normalization-schema.md)<br> - [进程事件](process-events-normalization-schema.md)<br> - [身份验证事件](authentication-normalization-schema.md)<br> - [注册表事件](registry-event-normalization-schema.md)<br> - [文件活动](file-event-normalization-schema.md) |
|**分析器**     |  使用 [KQL 函数](/azure/data-explorer/kusto/query/functions/user-defined-functions)将现有数据映射到规范化架构。 <br><br>从 [Azure Sentinel GitHub 分析器文件夹](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers)部署 Microsoft 开发的规范化分析器。 规范化分析器位于以 ASim 开头的子文件夹中。       |
|每个规范化架构的内容     |    包括分析规则、工作簿、搜寻查询等。 每个规范化架构的内容适用于任何规范化数据，无需创建特定于源的内容。     |

<br>

下图显示了如何将非规范化数据转换为规范化内容以及如何在 Azure Sentinel 中使用这些数据。 例如，可以从一个自定义的、特定于产品的非规范化表开始，使用分析器和规范化架构将该表转换为规范化数据。 将你的规范化数据用于 Microsoft 和自定义分析、规则、工作簿、查询等等。

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Azure Sentinel 中非规范化数据到规范化数据的转换流和用法":::

### <a name="azure-sentinel-information-model-terminology"></a>Azure Sentinel 信息模型术语

Azure Sentinel 信息模型使用以下术语：

|术语  |说明  |
|---------|---------|
|**报告设备**     |   向 Azure Sentinel 发送记录的系统。 此系统可能不是要为其发送记录的主题系统。      |
|**记录**     |从报告设备发送的数据单位。 记录通常称为 `log`、`event` 或 `alert`，但也可以是其他类型的数据。         |
|内容或内容项      |可以在 Azure Sentinel 中使用的不同的、可自定义的或用户创建的项目。 例如，这些项目包括分析规则、搜寻查询和工作簿。 内容项就是这样的一个项目。|

<br>

## <a name="normalized-schemas"></a>规范化架构

### <a name="schema-concepts"></a>架构概念

架构是表示活动的一组字段。 在查询中使用来自规范化架构的字段可确保查询适用于每个规范化源。

架构参考概述了构成每个架构的字段。 以下概念有助于理解架构参考文档，并在源包括了架构未涵盖的信息时以规范化方式扩展架构。


|概念  |说明  |
|---------|---------|
|**字段名**     |   每个架构的核心部分是其字段名称。 字段名称属于以下组： <br><br>- 所有架构通用的字段 <br>- 特定于架构的字段 <br>- 表示参与架构的实体（例如用户）的字段。 表示实体的字段在[不同的架构中是类似的](#entities)。 <br><br>如果源中的字段未在记录的架构中提供，这些字段将被规范化以保持一致。 如果这些额外的字段表示实体，则会根据实体字段准则将这些字段规范化。 否则，架构将会尽量在所有架构中保持一致。<br><br> 例如，虽然 DNS 服务器活动日志不提供用户信息，但终结点中的 DNS 活动日志可以包含可根据用户实体准则规范化的用户信息。      |
|**字段类型**     |  每个架构字段有一个类型。 Log Analytics 工作区具有有限的一组数据类型。 因此，Azure Sentinel 对许多架构字段使用某种逻辑类型，Log Analytics 不强制要求使用该类型，但该类型是为了实现架构兼容性而必须使用的。 逻辑字段类型可确保值和字段名称在不同的源中保持一致。  <br><br>有关详细信息，请参阅[逻辑类型](#logical-types)。     |
|字段类     |字段可以有多个类，这些类定义了字段何时应由分析器实现： <br><br>-    必需的字段必须出现在每个分析器中。 如果源未提供此值的信息，或者无法添加数据，则源不支持引用规范化架构的大多数内容项。<br>-  建议的字段（如果可用）应规范化。 但是，它们可能不会在每个源中提供，并且引用该规范化架构的任何内容项都应考虑到可用性。 <br>-  可选字段（如果可用）可以规范化或保持其原始形式。 通常，出于性能方面的原因，精简的分析器不会将其规范化。    |
|**实体**     | 事件围绕实体（例如用户、主机、进程或文件）进行演变，每个实体可能需要通过多个字段来描述自身。 例如，主机可以有名称和 IP 地址。 <br><br>单个记录可以包括相同类型的多个实体，例如源主机和目标主机。 <br><br>Azure Sentinel 信息模型定义了如何一致地描述实体，而实体允许扩展架构。 <br><br>例如，虽然网络会话架构不包括进程信息，但某些事件源确实提供可添加的进程信息。 有关详细信息，请参阅[实体](#entities)。 |
|**别名**     |  在某些情况下，不同的用户要求某个字段具有不同的名称。 例如，在 DNS 术语中，用户需要一个名为 `query` 的字段，但更常见的情况是它有一个域名。 别名允许为指定的值使用多个名称，从而解决了此问题。 别名类与其别名化的字段相同。       |

<br>

### <a name="logical-types"></a>逻辑类型

每个架构字段有一个类型。 某些字段具有内置的 Azure Log Analytics 类型，例如 `string`、`int`、`datetime` 或 `dynamic`。 其他字段具有逻辑类型，该类型表示字段值的规范化方式。

|数据类型  |物理类型  |格式和值  |
|---------|---------|---------|
|**布尔值**     |   Bool      |    使用原生 KQL 布尔数据类型，而不要使用布尔值的数字或字符串表示形式。     |
|枚举     |  字符串       |   为字段显式定义的值列表。 架构定义列出了接受的值。      |
|**日期/时间**     |  根据引入方法功能，按优先级的降序使用以下任一物理表示形式： <br><br>- Log Analytics 内置的日期时间类型 <br>- 使用 Log Analytics 日期时间数字表示形式的整数字段。 <br>- 使用 Log Analytics 日期时间数字表示形式的字符串字段 <br>- 用于存储受支持 [Log Analytics 日期/时间格式](/azure/data-explorer/kusto/query/scalar-data-types/datetime)的字符串字段。       |  [Log Analytics 日期和时间表示形式](/azure/kusto/query/scalar-data-types/datetime)与 Unix 时间的表示形式类似，但两者实际上是不同的。 有关详细信息，请参阅[转换指南](/azure/kusto/query/datetime-timespan-arithmetic)。 <br><br>注意：在适用的情况下，应调整时间的时区。 |
|**MAC 地址**     |  字符串       | 冒分十六进制表示法        |
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

<br>

### <a name="common-fields"></a>通用字段

下面是所有 ASIM 架构通用的字段。 此处既列出了通用字段，也列出了每个架构支持的字段值的详细信息。 例如，EventType 字段的值可能因每个架构而异，EventSchemaVersion 字段的值同样如此 。 

| 字段               | 类       | 类型       |  说明        |
|---------------------|-------------|------------|--------------------|
| <a name="timegenerated"></a>TimeGenerated | 内置 | datetime | 报告设备生成事件的时间。|
| _ResourceId   | 内置 |  GUID     | 报告设备或服务的 Azure 资源 ID，或使用 Syslog、CEF 或 WEF 转发的事件的日志转发器资源 ID。 |
| **EventMessage**        | 可选    | 字符串     |     一般消息或说明，包含在记录中或者从记录生成。   |
| EventCount          | 必需   | 整数    |     记录描述的事件数。 <br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。 <br><br>对于其他源，设置为 `1`。   |
| **EventStartTime**      | 必需   | 日期/时间  |      如果源支持聚合且记录表示多个事件，则此字段将指定生成第一个事件的时间。 <br><br>否则，此字段将别名化 [TimeGenerated](#timegenerated) 字段。 |
| **EventEndTime**        | 必需   | Alias      |      [TimeGenerated](#timegenerated) 字段的别名。    |
|  <a name=eventtype></a>EventType           | 必需   | Enumerated |    描述记录报告的操作。 每个架构将记录此字段的有效值列表。 |
| **EventSubType** | 可选 | Enumerated | 描述 [EventType](#eventtype) 字段中报告的操作的细分。 每个架构将记录此字段的有效值列表。 |
| <a name="eventresult"></a>EventResult | 必需 | Enumerated | 以下值之一：Success、Partial、Failure、NA（不适用）   。<br> <br>可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 或者，源可以只提供 [EventResultDetails](#eventresultdetails) 字段，应分析该字段以派生 EventResult 值。<br><br>示例： `Success`|
| <a name=eventresultdetails></a>EventResultDetails | 必需 | Alias | [EventResult](#eventresult) 字段中报告的结果的原因或详细信息。 每个架构将记录此字段的有效值列表。<br><br>示例： `NXDOMAIN`|
| **EventOriginalUid**    | 可选    | 字符串     |   原始记录的唯一 ID（如果已由源提供）。<br><br>示例： `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| EventOriginalType   | 可选    | 字符串     |   原始事件类型或 ID（如果已由源提供）。 例如，此字段用于存储原始 Windows 事件 ID。<br><br>示例： `4624`|
| <a name ="eventproduct"></a>EventProduct        | 必需   | 字符串     |             生成事件的产品。 <br><br>示例： `Sysmon`<br><br>注意：此字段可能未在源记录中提供。 在这种情况下，此字段必须由分析器设置。           |
| **EventProductVersion** | 可选    | 字符串     | 生成事件的产品的版本。 <br><br>示例： `12.1`      |
| **EventVendor**         | 必需   | 字符串     |           生成事件的产品的供应商。 <br><br>示例： `Microsoft`  <br><br>注意：此字段可能未在源记录中提供。 在这种情况下，此字段必须由分析器设置。  |
| **EventSchemaVersion**  | 必需   | 字符串     |    架构的版本。 每个架构将记录其当前版本。         |
| **EventReportUrl**      | 可选    | 字符串     | 在资源的事件中提供的 URL，提供有关该事件的其他信息。|
| Dvc | 必需       | 字符串     |               发生该事件的设备的唯一标识符。 <br><br>此字段可以别名化 [DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确的设备的云源，请使用与 [EventProduct](#eventproduct) 字段相同的值。           |
| <a name ="dvcipaddr"></a>DvcIpAddr           | 建议 | IP 地址 |         发生该事件的设备的 IP 地址。  <br><br>示例： `45.21.42.12`    |
| <a name ="dvchostname"></a>DvcHostname         | 建议 | 主机名   |               发生该事件的设备的主机名。 <br><br>示例： `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>DvcId               | 可选    | 字符串     |  发生该事件的设备的唯一 ID。 <br><br>示例： `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 可选    | MAC        |   发生该事件的设备的 MAC 地址。  <br><br>示例： `00:1B:44:11:3A:B7`       |
| DvcOs               | 可选    | 字符串     |         发生该事件的设备上运行的操作系统。    <br><br>示例： `Windows`    |
| DvcOsVersion        | 可选    | 字符串     |   发生该事件的设备上的操作系统版本。 <br><br>示例： `10` |
| **AdditionalFields**    | 可选    | 动态    | 如果源提供了值得保留的附加信息，请使用原始字段名称保留这些信息，或者创建动态 AdditionalFields 字段，并在其中以键/值对的形式添加这些附加信息。    |

> [!NOTE]
> Log Analytics 还会添加与安全用例不太相关的其他字段。 有关详细信息，请参阅 [Azure Monitor 日志中的标准列](../azure-monitor/logs/log-standard-columns.md)。
>


### <a name="entities"></a>实体

事件围绕用户、主机、进程或文件等实体进行演变。 实体表示形式允许将相同类型的多个实体包含在单个记录中，并支持相同实体的多个属性。 

为了启用实体功能，实体表示形式遵守以下准则：

|准则  |说明  |
|---------|---------|
|描述符和别名     | 由于单个事件通常包括相同类型的多个实体（例如源主机和目标主机），因此描述符用作前缀来标识与特定实体关联的所有字段。 <br><br>为了保持规范化，Azure Sentinel 信息模型使用少量的标准描述符，并为实体的特定角色选择最合适的描述符。  <br><br>如果某种类型的单个实体与某个事件相关，则无需使用描述符。 此外，一组不带描述符的字段将别名化每种类型的最常用实体。  |
|标识符和类型     | 规范化架构允许为每个实体使用多个标识符，我们预期这些标识符将在事件中共存。 如果源事件具有无法映射到规范化架构的其他实体标识符，请将这些标识符保留为源的形式，或使用 `AdditionalFields` 动态字段。 <br><br>若要维护标识符的类型信息，在适用的情况下，请将类型存储在具有相同名称且后缀为 `Type` 的字段中。 例如 `UserIdType`。         |
|**特性**     |   实体通常具有其他不用作标识符的属性，并且也可以使用描述符对其进行限定。 例如，如果源用户具有域信息，则规范化字段为 `SrcUserDomain`。      |

<br>

每个架构显式定义了中心实体和实体字段。 遵循以下准则可以：

- 了解中心架构字段
- 了解如何使用架构中未显式定义的其他实体或实体字段以规范化方式扩展架构

#### <a name="the-user-entity"></a>用户实体

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

<br>

下表描述了支持的用户标识符：

|规范化字段  |类型  |格式和支持的类型  |
|---------|---------|---------|
|**UserId**     |    字符串     |   系统中用户的计算机可读的唯一字母数字表示形式。 <br><br>格式和支持的类型包括：<br>    - SID (Windows)：`S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  UID (Linux)：`4578`<br>    -    AADID (Azure Active Directory)：`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - OktaId：`00urjk4znu3BcncfY0h7`<br>    - AWSId：`72643944673`<br><br>    将 ID 类型存储在 `UserIdType` 字段中。 如果其他 ID 可用，我们建议将字段名称分别规范化为 `UserSid`、`UserUid`、`UserAADID`、`UserOktaId` 和 `UserAwsId`。       |
|**用户名**     |  字符串       |   采用以下格式之一并遵循以下优先顺序的用户名（适用的情况下包括域信息）： <br> -   UPN/电子邮件：`johndow@contoso.com` <br>  -    Windows：`Contoso\johndow` <br> -   DN：`CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - 简单：`johndow`。 仅当未提供域信息时才使用此格式。 <br><br> 将 Username 类型存储在 `UsernameType` 字段中。    |

<br>

#### <a name="the-process-entity"></a>进程实体

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

<br>

有关详细信息，请参阅 [Azure Sentinel 进程事件规范化架构参考（公共预览版）](process-events-normalization-schema.md)。

#### <a name="the-device-entity"></a>设备实体

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

<br>

> [!NOTE]
> `Domain` 是设备的典型属性，但不是完整标识符。
>

有关详细信息，请参阅 [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)。

#### <a name="entity-mapping-example"></a>实体映射示例

本部分使用 [Windows 事件 4624](/windows/security/threat-protection/auditing/event-4624) 作为示例来介绍如何规范化 Azure Sentinel 的事件数据。

此事件具有以下实体：

|Microsoft 术语  |原始事件字段前缀 |ASIM 字段前缀  |说明  |
|---------|---------|---------|---------|
|**主题**     | `Subject`        |   `Actor`      |  报告有关成功登录的信息的用户。      |
|新登录     |    `Target`     |  `TargetUser`       |  执行登录的用户。       |
|**处理**     |    -     |     `ActingProcess`    |   尝试登录的进程。      |
|网络信息     |   -      |   `Src`      |     从中尝试执行登录的计算机。    |

<br>

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

<br>

## <a name="parsers"></a>分析器

在 Azure Sentinel 中，分析是在查询时进行的。 分析器生成为 [KQL 用户定义的函数](/azure/data-explorer/kusto/query/functions/user-defined-functions)，这些函数将现有表（例如 CommonSecurityLog）、自定义日志表或 Syslog 中的数据转换为规范化架构。 将分析器保存为工作区函数后，可以像使用任何其他 Azure Sentinel 表一样使用该函数。

有两种级别的分析器：与源无关的分析器和特定于源的分析器，如上面的 [Azure Sentinel 信息模型组件](#azure-sentinel-information-model-components)插图中所示 。

- 与源无关的分析器将所有已规范化的源组合到同一架构，可用于通过规范化字段查询所有这些源。 与源无关的分析器名称为 `im<schema>`，其中的 `<schema>` 代表它处理的特定架构。

    例如，以下查询使用与源无关的 DNS 分析器通过 `ResponseCodeName`、`SrcIpAddr` 和 `TimeGenerated` 规范化字段来查询 DNS 事件：

    ```kusto
    imDns
      | where isnotempty(ResponseCodeName)
      | where ResponseCodeName =~ "NXDOMAIN"
      | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
    ```

    与源无关的分析器可以使用 `union` KQL 运算符来组合多个特定于源的规范化分析器。 特定于源的规范化分析器名称为 `vim<schema><vendor><product>`。 因此，`imDns` 分析器如下所示：

    ```kusto
    union isfuzzy=true
    vimDnsEmpty,
    vimDnsCiscoUmbrella,
    vimDnsInfobloxNIOS,
    vimDnsMicrosoftOMS
    ```

- 将特定于源的规范化分析器添加到与源无关的分析器，可以在使用与源无关的分析器的内置查询中包含自定义源。

    使用特定于源的分析器可以从内置内容（例如分析、工作簿和自定义数据的见解）中立即获取值。

    也可以单独使用特定于源的分析器。 例如，在特定于 Infoblox 的工作簿中使用 `vimDnsInfobloxNIOS` 分析器。
### <a name="writing-parsers"></a>编写分析器

分析器是保存为工作区函数的 KQL 查询。 保存后，可以像使用内置表一样使用该函数。 分析器查询包括以下部分：

筛选器 > 分析 > 准备字段  

#### <a name="filtering"></a>筛选

在许多情况下，表包含多种类型的事件。 例如：
* Syslog 表包含来自多个源的数据。
* 自定义表可以包含来自单个源的信息，该源提供多种事件类型并可适应各种架构。

因此，解析器首先应该仅筛选与目标架构相关的记录。

KQL 中的筛选是使用 `where` 运算符进行的。 例如，Sysmon 事件 1 报告进程创建，应规范化为 ProcessEvent 架构 。 Sysmon 事件 1 事件是 `Event` 表的一部分，应使用以下筛选器：

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

为了确保分析器保持良好的性能，请注意以下筛选建议：

-   始终根据内置字段而不是分析的字段进行筛选。 虽然使用分析的字段进行筛选有时更方便，但这会对性能造成很大的影响。
-   使用有利于优化性能的运算符。 具体而言，请使用 `==`、`has` 和 `startswith`。 使用 `contains` 或 `matches regex` 之类的运算符也会对性能造成很大的影响。

为保持性能而提供的筛选建议不一定总是很容易遵循。 例如，使用 `has` 不如使用 `contains` 那么准确。 在其他情况下，匹配内置字段（例如 `SyslogMessage`）不如比较提取的字段（例如 `DvcAction`）那么准确。 在这种情况下，建议仍旧使用性能优化的运算符而不是内置字段进行预筛选，并在分析后使用更准确的条件重复筛选。

有关示例，请参阅以下 [Infoblox DNS](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM/Infoblox) 分析器代码片段。 该分析器首先检查 SyslogMessage 字段是否 `has`（包含）单词 `client`。 但是，该词语可能使用在消息中的不同位置。 因此，在分析 `Log_Type` 字段后，该分析器会再次检查单词 `client` 是否确实为字段值。

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> 分析器不应按时间进行筛选，因为使用分析器的查询会筛选时间。
> 

#### <a name="parsing"></a>分析

查询选择相关的记录后，可能需要分析这些记录。 通常，如果单个文本字段中传达了大量事件信息，则需要进行分析。

下面按性能优化顺序列出了执行分析的 KQL 运算符。 第一个运算符提供优化程度最高的性能，最后一个运算符提供优化程度最低的性能。

|运算符  |说明  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    分析由分隔符分隔的值的字符串     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     分析 CSV（逗号分隔值）行格式的值的字符串。    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    使用某种模式（可以是性能较好的简化模式，也可以是正则表达式）分析任意字符串中的多个值。     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | 使用正则表达式分析任意字符串中的单一值。 `extract_all` 的性能类似于 `parse`（如果后者使用正则表达式）。        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    使用正则表达式提取任意字符串中的单个值。 <br><br>如果需要单个值，则使用 `extract` 的性能比使用 `parse` 或 `extract_all` 更好。 但是，对同一源字符串使用 `extract` 的多次激活的效率明显低于单个 `parse` 或 `extract_all`，应避免这样用法。      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | 分析 JSON 格式的字符串中的值。 如果只需要 JSON 中的少量几个值，则使用 `parse`、`extract` 或 `extract_all` 可提供更好的性能。        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    分析 XML 格式的字符串中的值。 如果只需要 XML 中的少量几个值，则使用 `parse`、`extract` 或 `extract_all` 可提供更好的性能。     |

<br>

除了分析字符串之外，分析阶段可能还需要对原始值进行其他处理，包括：

- 格式设置和类型转换。 提取源字段后，可能需要设置其格式以适应目标架构字段。 例如，可能需要将表示日期和时间的字符串转换为日期时间字段。     在这种情况下，`todatetime` 和 `tohex` 等函数非常有用。

- 值查找。 提取源字段的值后，可能需要将其映射到为目标架构字段指定的值集。 例如，某些源报告数字 DNS 响应代码，而架构强制要求报告更常见的文本响应代码。 要映射少量值，可以使用 `iff` 和 `case` 函数。

    例如，Microsoft DNS 分析器使用 `iff` 语句基于事件 ID 和响应代码分配 `EventResult` 字段，如下所示：

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    对于多个值，请使用 `datatable` 和 `lookup`，如同一个 DNS 分析器中所示：

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName, 
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use', 
         'Unassigned')
    ```

> [!NOTE]
> 转换不允许只使用 `lookup`，因为多个值将映射到 `Reserved for Private Use`、`Unassigned`，因此查询将同时使用 lookup 和 case。 尽管如此，查询仍比对所有值使用 `case` 要高效得多。
>

#### <a name="prepare-fields-in-the-result-set"></a>在结果集中准备字段

分析器必须在结果集中准备字段，以确保使用规范化字段。 作为一项准则，不应从结果集中删除未规范化的原始字段，除非有令人信服的理由（例如，这些字段会造成混淆）。

以下 KQL 运算符用于准备字段：

|运算符  | 说明  | 何时在分析器中使用  |
|---------|---------|---------|
|**extend**     | 创建计算字段并将其添加到记录中        |  如果规范化字段是从原始数据分析或转换的，则使用 `Extend`。 有关详细信息，请参阅前面[分析](#parsing)部分中的示例。     |
|**project-rename**     | 重命名字段        |     如果某个字段存在于原始事件中且只需要重命名，请使用 `project-rename`。 <br><br>已重命名的字段的行为仍类似于内置字段，针对该字段的操作的性能要好得多。   |
|**project-away**     |      删除字段。   |对于要从结果集中删除的特定字段，请使用 `project-away`。         |
|**project**     |  选择之前已存在的字段，或者作为语句的一部分创建的字段。 删除所有其他字段。       | 不建议在分析器中使用，因为分析器不应删除未规范化的任何其他字段。 <br><br>如果需要删除特定的字段（例如，在分析过程中使用的临时值），请使用 `project-away` 从结果中将其删除。      |

<br>

### <a name="handle-parsing-variants"></a>处理分析变体

在许多情况下，事件流中的事件包括需要不同分析逻辑的变体。 

从不同的子分析器生成一个分析器，并让每个子分析器处理需要不同分析逻辑的另一个事件变体通常会更方便。 然后，可以使用 `union` 运算符来统一这些子分析器（每个子分析器本身是一个查询）。 这种方法虽然可以带来方便，但不建议采用，因为它会显著影响分析器的性能。

处理变体时，请遵循以下准则：

|场景  |处理  |
|---------|---------|
|不同的变体表示不同的事件类型，通常映射到不同的架构     |  使用单独的分析器       |
|不同的变体表示相同的事件类型，但以不同的方式构建。     |   如果变体是已知的（例如，在分析之前有区分事件的方法时），请使用 `case` 运算符选择要运行的正确 `extract_all` 和字段映射，如 [Infoblox DNS 分析器](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM/Infoblox)中所示。      |
|如果 `union` 不可避免     |  使用不可避免地需要使用 `union`，请确保遵循以下准则：<br><br>- 在每个子查询中使用内置字段进行预筛选。 <br>- 确保筛选器互斥。 <br>- 考虑不分析较不重要的信息，以减少子查询数量。       |

<br>

### <a name="deploy-parsers"></a>部署分析器

通过将分析器复制到 Azure Monitor“日志”页并保存更改来手动部署分析器。 此方法可用于测试。 有关详细信息，请参阅[创建函数](../azure-monitor/logs/functions.md)。

但是，若要部署大量分析器，我们建议使用 ARM 模板。 例如，在部署包含与源无关的分析器以及多个特定于源的分析器的完整规范化解决方案时，或者为源的不同架构部署多个分析器时，可以使用 ARM 模板。

有关详细信息，请参阅[泛型分析器 ARM 模板](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery)。 使用此模板作为起点，在模板部署过程中将分析器粘贴到相关位置来部署分析器。 有关示例，请参阅 [DNS 分析器 ARM 模板](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM)。

> [!TIP]
> ARM 模板可以包含不同的资源，因此可将分析器与连接器、分析规则或监视列表以及几个有用选项一起部署。 例如，分析器可以引用与其一起部署的监视列表。
>

### <a name="use-parsers-in-normalized-content"></a>在规范化内容中使用分析器

Azure Sentinel 内容包括使用与源无关的规范化分析器的分析规则、搜寻查询和工作簿。

- 在 Azure Sentinel 库和[解决方案](sentinel-solutions-catalog.md)中查找规范化的内置内容。

- 自行创建规范化内容，或修改现有内容以使用规范化数据。

####  <a name="identify-built-in-normalized-content"></a>识别内置规范化内容

每个架构的文档包含了使用每个规范化架构的内容项的列表。 架构内容会定期更新并遵循以下准则：

-   注重于规范化架构的内容项包含该架构作为名称的一部分。 例如，注重于[规范化 DNS 架构](dns-normalization-schema.md)的分析规则的名称带有 `(Normalized DNS)` 后缀。

-   考虑其他数据类型的规范化架构的内容项不会以任何后缀进行标记。 对于这种情况，在 GitHub 上搜索规范化架构分析器名称即可识别所有这些内容项。

#### <a name="modifying-your-content-to-use-normalized-data"></a>修改内容以使用规范化数据

要使自定义内容能够使用规范化，请执行以下操作：

- 修改查询，以使用与查询相关的与源无关的分析器。
- 修改查询中的字段名称，以使用规范化架构字段名称。
- 在适用的情况下，更改条件以在查询中使用字段的规范化值。

例如，考虑使用“观测到反向 DNS 查找计数较高的罕见客户端”DNS 分析规则来处理 Infoblox DNS 服务器发送的 DNS 事件：

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

以下版本是与源无关的版本，它使用规范化来为提供 DNS 查询事件的任何源提供相同的检测：

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

与源无关的规范化版本具有以下差别：

- 使用 `imDns` 规范化分析器而不是 Infoblox 分析器。

- `imDns` 只提取 DNS 查询事件，因此无需检查事件类型，而 Infoblox 版本中的 `where ProcessName =~ "named" and Log_Type =~ "client"` 会执行这种检查。

- 使用 `ResponseCodeName` 和 `SrcIpAddr` 字段而不是相应的 `ResponseCode` 和 `Client_IP`。

#### <a name="enable-normalized-content-to-use-your-custom-data"></a>使规范化内容能够使用自定义数据

规范化允许将你自己的内容和内置内容与自定义数据一起使用。

例如，如果你的自定义连接器接收 DNS 查询活动日志，则你可以通过以下方式来确保 DNS 查询活动日志利用任何规范化的 DNS 内容：

-   为自定义连接器[创建规范化分析器](#parsers)。 如果分析器用于供应商 `Yyy` 的产品 `Xxx`，则分析器应命名为 `vimDnsYyyXxx`。

-   修改与源无关的分析器 `imDns`，使之也包含你的分析器，方法是将你的分析器添加到 `union` 语句中的分析器列表。 例如：

    ```kusto
    union isfuzzy=true 
    vimDnsEmpty, 
    vimDnsCiscoUmbrella, 
    vimDnsInfobloxNIOS, 
    vimDnsMicrosoftOMS,
    vimDnsYyyXxx
    ```

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Sentinel 和 Azure Sentinel 信息模型中的规范化。

有关详细信息，请参阅：

- [Azure Sentinel 网络规范化架构参考](normalization-schema.md)
- [Azure Sentinel DNS 规范化架构参考](dns-normalization-schema.md)
- [Azure Sentinel 文件事件规范化架构参考（公共预览版）](file-event-normalization-schema.md)
- [Azure Sentinel 进程事件规范化架构参考](process-events-normalization-schema.md)
- [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)