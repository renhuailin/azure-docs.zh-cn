---
title: Azure Sentinel 身份验证规范化架构参考 | Microsoft Docs
description: 本文介绍 Azure Sentinel 身份验证规范化架构。
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
ms.openlocfilehash: 3372dd6c76cbc4e1e232832966474c4b383f2d76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731068"
---
# <a name="azure-sentinel-authentication-normalization-schema-reference-public-preview"></a>Azure Sentinel 身份验证规范化架构参考（公共预览版）

身份验证信息模型用于描述与用户身份验证、登录和注销相关的事件。身份验证事件由许多报告设备发送，通常作为事件流的一部分与其他事件一起发送。

例如，Windows 将多个身份验证事件与其他 OS 活动事件一起发送。 因此，在大多数情况下，身份验证事件存储在不同的 Azure Sentinel 表中，并使用 KQL 函数进行规范化，该函数也仅会筛选相关的身份验证事件。

身份验证事件包括来自侧重于身份验证的系统（例如 VPN 网关或域控制器）的事件，以及直接向最终系统（例如计算机或防火墙）进行身份验证的事件。

有关 Azure Sentinel 中的规范化的详细信息，请参阅[规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)。

> [!IMPORTANT]
> 身份验证规范化架构目前以公共预览版提供。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="parsers"></a>分析器

Azure Sentinel 提供以下内置的特定于产品的身份验证事件分析器： 

- 使用 Log Analytics 代理或 Azure Monitor 代理收集的报告为安全事件（4624、4625、4634 和 4647）的 Windows 登录。
- 使用 Microsoft 365 Defender 连接器收集，由 Microsoft 365 Defender for Endpoint 报告的 Windows 登录。
- 使用 Azure Active Directory 连接器收集的 Azure Active Directory 登录。 针对常规、非交互式、托管标识和服务主体登录提供了单独的分析器。
- 使用 AWS CloudTrail 连接器收集的 AWS 登录。
- 使用 Okta 连接器收集的 Okta 身份验证。

若要使用与源无关的分析器（统一了所有列出的分析器），并确保针对所有配置的源进行分析，请在查询中使用“imAuthentication”作为表名称。

从 [Azure Sentinel GitHub 存储库](https://aka.ms/AzSentinelAuth)部署[与源无关的分析器和特定于源的分析器](normalization.md#parsers)。



## <a name="normalized-content"></a>规范化内容

对身份验证规范化架构的支持还包括对以下内置分析规则和规范化身份验证分析器的支持：

- 3 小时内来自不同国家/地区的用户登录（使用身份验证规范化）
- 潜在的密码喷涂攻击（使用身份验证规范化）
- 对用户凭据进行的暴力攻击（使用身份验证规范化）

规范化身份验证分析规则是唯一的，因为它们可以检测跨源的攻击。 例如，如果用户从不同国家/地区登录不同的不相关系统，Azure Sentinel 现在可以检测到这种威胁。

## <a name="schema-details"></a>架构详细信息

身份验证信息模型与 [OSSEM 登录实体架构](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/logon.md)保持一致。

在下面的表中，“类型”指的是逻辑类型。 有关详细信息，请参阅[逻辑类型](normalization.md#logical-types)。

### <a name="log-analytics-fields"></a>Log Analytics 字段

Log Analytics 会为每条记录生成以下字段，在创建自定义连接器时可以替代这些字段。

|字段  |类型  |说明  |
|---------|---------|---------|
|<a name ="timegenerated"></a>TimeGenerated     |  datetime       |报告设备生成事件的时间。         |
|_ResourceId     | GUID        |  报告设备或服务的 Azure 资源 ID，或使用 Syslog、CEF 或 WEF 转发的事件的日志转发器资源 ID。       |
|     |         |         |

> [!NOTE]
> Log Analytics 还会添加与安全用例不太相关的其他字段。 有关详细信息，请参阅 [Azure Monitor 日志中的标准列](../azure-monitor/logs/log-standard-columns.md)。
>

### <a name="event-fields"></a>事件字段

事件字段是所有架构的通用字段，用于描述活动本身和报告设备。

| 字段               | 类       | 类型       |  说明        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 可选    | 字符串     |     一般消息或说明，包含在记录中或者从记录生成。   |
| EventCount          | 必需   | 整数    |     记录描述的事件数。 <br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。 <br><br>对于其他源，设置为 `1`。 <br><br>注意：出于一致性目的，包括此字段，但它通常不用于身份验证事件。  |
| **EventStartTime**      | 必需   | 日期/时间  |      如果源支持聚合并且记录表示多个事件，则此字段指定生成第一个事件的时间。 否则，此字段将别名化 [TimeGenerated](#timegenerated) 字段。<br><br>注意：出于一致性目的，包括此字段，但它通常不用于身份验证事件。  |
| **EventEndTime**        | 必需   | Alias      |      [TimeGenerated](#timegenerated) 字段的别名。    |
| **EventType**           | 必需   | Enumerated |    描述记录报告的操作。 <br><br>对于身份验证记录，支持的值包括： <br>- `Logon` <br>- `Logoff`<br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 原始值应存储在 [EventOriginalType](#eventoriginaltype) 字段中。|
| <a name ="eventoriginaltype"></a>EventOriginalType           | 可选   | 字符串 |    源记录中提供的事件类型或 ID。 <br><br>示例： `4625`|
| **EventResult**         | 必需   | Enumerated |  描述事件的结果，规范化为以下支持的值之一： <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA`（不适用） <br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。 <br><br>源可以只提供 [EventResultDetails](#eventresultdetails) 字段的值，必须分析该字段才能获取 EventResult 值。 |
| <a name ="eventresultdetails"></a>EventResultDetails         | 可选   | 字符串 |  以下值之一： <br><br>-  `No such user or password`. 原始事件报告没有此类用户，并且没有引用密码时，还应使用此值。 <br>-   `Incorrect password`<br>-   `Account expired`<br>-  `Password expired`<br>- `User locked`<br>-  `User disabled`<br>-    `Logon violates policy`. 原始事件报告如下情况时应使用此值，例如：需要 MFA、在工作时间之外登录、条件性访问限制或尝试过于频繁。<br>-  `Session expired`<br>-  `Other`<br><br>注意：可以在源记录中使用不同字词提供该值，这些字词应规范化为这些值。|
| **EventSubType**    | 可选    | 字符串     |   登录类型，通常用于 Windows 登录类型。 <br><br>示例： `Interactive`|
| EventOriginalResultDetails    | 可选    | 字符串     |  [EventResultDetails](#eventresultdetails) 的原始记录中提供的值（如果已由源提供）。|
| **EventOriginalUid**    | 可选    | 字符串     |   原始记录的唯一 ID（如果已由源提供）。|
| EventOriginalType   | 可选    | 字符串     |   原始事件类型或 ID（如果已由源提供）。<br><br>示例： `4624`|
| <a name ="eventproduct"></a>EventProduct        | 必需   | 字符串     |             生成事件的产品。 <br><br>示例： `Windows`<br><br>注意：源记录中可能未提供此字段。 在这种情况下，此字段必须由分析器设置。           |
| **EventProductVersion** | 可选    | 字符串     | 生成事件的产品的版本。 <br><br>示例： `10` <br><br>注意：源记录中可能未提供此字段。 在这种情况下，此字段必须由分析器设置。     |
| **EventVendor**         | 必需   | 字符串     |           生成事件的产品的供应商。 <br><br>示例： `Microsoft`  <br><br>注意：源记录中可能未提供此字段。 在这种情况下，此字段必须由分析器设置。  |
| **EventSchemaVersion**  | 必需   | 字符串     |    架构的版本。 此处所述的架构版本为 `0.1`         |
| **EventReportUrl**      | 可选    | 字符串     | 在资源的事件中提供的 URL，提供有关该事件的其他信息。|
| <a name ="dvc"></a>Dvc | 必需       | 字符串     |              发生该事件的设备的唯一标识符。 <br><br>此字段可以别名化 [DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确的设备的云源，请使用与 [Event Product](#eventproduct) 字段相同的值。             |
| <a name ="dvcipaddr"></a>DvcIpAddr           | 建议 | IP 地址 |         发生进程事件的设备的 IP 地址。  <br><br>示例： `45.21.42.12`  <br><br>注意：如果标识符可用但类型未知，请不要使用此字段。 有关详细信息，请参阅 [Dvc](#dvc)。  |
| <a name ="dvchostname"></a>DvcHostname         | 建议 | 主机名   |               发生进程事件的设备的主机名。 <br><br>示例： `ContosoDc.Contoso.Azure`      <br><br>注意：如果标识符可用但类型未知，请不要使用此字段。 有关详细信息，请参阅 [Dvc](#dvc)。          |
| <a name ="dvcid"></a>DvcId               | 可选    | 字符串     |  发生进程事件的设备的唯一 ID。 <br><br>示例： `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **AdditionalFields**    | 可选    | 动态    | 如果源提供了值得保留的附加信息，请使用原始字段名称保留这些信息，或者创建动态“AdditionalFields”字段，并在其中以键/值对的形式添加这些附加信息。    |
| | | | |


## <a name="authentication-specific-fields"></a>特定于身份验证的字段

下表中列出的字段特定于身份验证事件，但它们类似于其他架构中的字段，并遵循类似的命名约定。

身份验证事件引用以下实体：

- 演员
- ActingApp
- SrcDvc
- TargetUser
- TargetApp
- TargetDvc

以下是对这些实体之间关系的最好展示：

“参与者”(Actor) 在“源设备”(SrcDvc) 上运行“操作应用程序”(ActingApp)，尝试向“目标设备”(TargetDvc) 上的“目标应用程序”(TargetApp) 对“目标用户”(TargetUser) 进行身份验证  。

| 字段          | 类        | 类型       | 说明   |
|---------------|--------------|------------|-----------------|
|**LogonMethod** |可选 |字符串 |用于执行身份验证的方法。 <br><br>示例： `Username & Password` |
|LogonProtocol |可选 |字符串 |用于执行身份验证的协议。 <br><br>示例： `NTLM` |
| <a name="actoruserid"></a>ActorUserId    | 可选  | UserId     |   参与者的计算机可读的唯一字母数字表示形式。 有关详细信息，请参阅[用户实体](normalization.md#the-user-entity)。  <br><br>示例： `S-1-12-1-4141952679-1282074057-627758481-2916039507`    |
| ActorUserIdType| 可选  | UserIdType     |  [ActorUserId](#actoruserid) 字段中存储的 ID 的类型。 有关详细信息，请参阅[用户实体](normalization.md#the-user-entity)。         |
| <a name="actorusername"></a>ActorUsername  | 可选    | 用户名     | 参与者的用户名，包括域信息（如果可用）。 有关详细信息，请参阅[用户实体](normalization.md#the-user-entity)。<br><br>示例： `AlbertE`     |
| ActorUsernameType              | 可选    | UsernameType |   指定 [ActorUsername](#actorusername) 字段中存储的用户名的类型。 有关详细信息，请参阅[用户实体](normalization.md#the-user-entity)。 <br><br>示例： `Windows`       |
| ActorUserType | 可选 | 字符串 | 参与者的类型。 <br><br>例如：`Guest` |
| ActorSessionId | 可选     | 字符串     |   参与者登录会话的唯一 ID。  <br><br>示例： `102pTUgC3p8RIqHvzxLCHnFlg`  |
| ActingAppId | 可选 | 字符串 | 代表参与者授权的应用程序的 ID，包括进程、浏览器或服务。 <br><br>例如：`0x12ae8` |
| ActiveAppName | 可选 | 字符串 | 代表参与者授权的应用程序的名称，包括进程、浏览器或服务。 <br><br>例如：`C:\Windows\System32\svchost.exe` |
| ActingAppType | 可选 | Enumerated | 操作应用程序的类型。 支持的值包括： <br> <br>- `Process` <br>- `Browser` <br>- `Resource` <br>- `Other` |
| HttpUserAgent |   可选    | 字符串 |  通过 HTTP 或 HTTPS 执行身份验证时，此字段的值是执行身份验证时操作应用程序提供的 user_agent HTTP 标头。<br><br>例如：`Mozilla/5.0 (iPhone; CPU iPhone OS 12_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1` |
|<a name="targetuserid"></a>TargetUserId   | 可选 | UserId     | 目标用户的计算机可读的唯一字母数字表示形式。 有关详细信息，请参阅[用户实体](normalization.md#the-user-entity)。            <br><br> 示例： `00urjk4znu3BcncfY0h7`    |
| TargetUserIdType               | 可选 | UserIdType     | [TargetUserId](#targetuserid) 字段中存储的用户 ID 的类型。 有关详细信息，请参阅[用户实体](normalization.md#the-user-entity)。            <br><br> 示例：`SID`  |
| <a name="targetusername"></a>TargetUsername | 可选 | 用户名     | 目标用户的用户名，包括域信息（如果可用）。 有关详细信息，请参阅[用户实体](normalization.md#the-user-entity)。  <br><br>示例：`MarieC`      |
| TargetUsernameType             |可选  | UsernameType | 指定 [TargetUsername](#targetusername) 字段中存储的用户名的类型。 有关详细信息，请参阅[用户实体](normalization.md#the-user-entity)。          |
| TargetUserType | 可选 | 字符串 | 目标用户的类型。 <br><br>例如：`Member` |
| TargetSessionId | 可选 | 字符串 | 源设备上 TargetUser 的登录会话标识符。 |
| **用户**           | Alias        |     字符串       | [TargetUsername](#targetusername) 或 [TargetUserId](#targetuserid) 的别名（如果未定义 [TargetUsername](#targetusername)）。 <br><br>示例： `CONTOSO\dadmin`     |
|SrcDvcId |可选 |字符串 |记录中报告的源设备的 ID。 <br><br>例如：`ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| <a name="srcdvchostname"></a>SrcDvcHostname |可选 | 主机名| 源设备主机名，包括域信息（如果可用）。 有关详细信息，请参阅[实体](normalization.md#the-device-entity)。 <br><br>示例： `Constoso\DESKTOP-1282V4D`|
| SrcDvcHostnameType|可选 |HostnameType |[SrcDvcHostname](#srcdvchostname) 的类型（如果已知）。 有关详细信息，请参阅[实体](normalization.md#the-device-entity)。 |
|**SrcDvcType** |可选 |Enumerated |源设备的类型。 可能的值包括： <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|**SrcDvcIpAddr**|建议 |IP 地址 |源设备的 IP 地址。 <br><br>示例： `185.175.35.214` |
| **SrcDvcOs**|可选 |字符串 |源设备的 OS。 <br><br>示例： `Windows 10` |
|SrcIsp | 可选|字符串 |源设备用于连接到 Internet 的 Internet 服务提供商 (ISP)。 <br><br>示例： `corpconnect` |
| **SrcGeoCountry**|可选 |国家/地区 |示例： `Canada` <br><br>有关详细信息，请参阅[逻辑类型](normalization.md#logical-types)。 |
| **SrcGeoCity**|可选 |城市 |示例： `Montreal` <br><br>有关详细信息，请参阅[逻辑类型](normalization.md#logical-types)。 |
|**SrcGeoRegion** | 可选|区域 | 示例： `Quebec` <br><br>有关详细信息，请参阅[逻辑类型](normalization.md#logical-types)。|
| SrcGeoLongtitude|可选 |经度  | 示例： `-73.614830` <br><br>有关详细信息，请参阅[逻辑类型](normalization.md#logical-types)。|
| **SrcGeoLatitude**|可选 |纬度 |示例： `45.505918` <br><br>有关详细信息，请参阅[逻辑类型](normalization.md#logical-types)。 |
|**TargetAppId** |可选 | 字符串| 需要授权的应用程序的 ID，该 ID 通常由报告设备分配。 <br><br>示例： `89162` |
|<a name="targetappname"></a>TargetAppName |可选 |字符串 |需要授权的应用程序的名称，包括服务、URL 或 SaaS 应用程序。 <br><br>示例： `Saleforce` |
| TargetAppType|可选 |字符串 |代表参与者授权的应用程序的类型。 支持的值包括：  <br><br>- `Process` <br>- `Service` <br>- `Resource` <br>- `URL` <br>- `SaaS application` <br>- `Other`|
|TargetUrl |可选 |字符串 |与目标应用程序相关联的 URL。 <br><br>示例： `https://console.aws.amazon.com/console/home?fromtb=true&hashArgs=%23&isauthcode=true&nc2=h_ct&src=header-signin&state=hashArgsFromTB_us-east-1_7596bc16c83d260b` |
|LogonTarget| Alias| |[TargetAppName](#targetappname)、URL 或 [TargetDvcHostname](#targetdvchostname) 的别名，以最能描述身份验证目标的字段为准。 |
|TargetDvcId |可选 | 字符串|记录中报告的目标设备的 ID。 <br><br> 示例： `2739` |
|<a name="targetdvchostname"></a>TargetDvcHostname | 建议| 字符串|目标设备主机名，包括域信息（如果可用）。 有关详细信息，请参阅[实体](normalization.md#the-device-entity)。 |
| TargetDvcHostnameType|建议 | 字符串|[TargetDvcHostname](#targetdvchostname) 的类型。 有关详细信息，请参阅[实体](normalization.md#the-device-entity)。 |
|TargetDvcType |可选 | Enumerated|目标设备的类型。 支持的值包括： <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|<a name="targetdvcipaddr"></a>TargetDvcIpAddr |可选 | IP 地址|目标设备的 IP 地址。 <br><br>示例： `2.2.2.2` |
|TargetDvc |Alias | |   目标设备的唯一标识符。 <br><br>选择此值可以为特定源的最合适值指定别名：[TargetDvcHostname](#targetdvchostname)[TargetDvcIpAddr](#targetdvcipaddr) 或不同的 ID（如果更合适）。 |
| TargetDvcOs| 可选| 字符串| 目标设备的 OS。 <br><br>示例： `Windows 10`|
| TargetPortNumber|可选 |整数 |目标设备的端口。|
| | | | |




## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Sentinel 中的规范化](normalization.md)
- [Azure Sentinel DNS 规范化架构参考](dns-normalization-schema.md)
- [Azure Sentinel 文件事件规范化架构参考（公共预览版）](file-event-normalization-schema.md)
- [Azure Sentinel 网络规范化架构参考](normalization-schema.md)
- [Azure Sentinel 进程事件规范化架构参考（公共预览版）](process-events-normalization-schema.md)
