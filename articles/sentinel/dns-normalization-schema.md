---
title: Azure Sentinel DNS 规范化架构参考 | Microsoft Docs
description: 本文介绍 Azure Sentinel DNS 规范化架构。
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
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: d1196f634143b2526240fe36775d7c0b272a7e3a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726069"
---
# <a name="azure-sentinel-dns-normalization-schema-reference-public-preview"></a>Azure Sentinel DNS 规范化架构参考（公共预览版）

DNS 信息模型用于描述 DNS 服务器或 DNS 安全系统报告的事件，Azure Sentinel 用它来启用与源无关的分析。

有关详细信息，请参阅[规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)。

> [!IMPORTANT]
> DNS 规范化架构目前处于公共预览阶段。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="guidelines-for-collecting-dns-events"></a>DNS 事件收集准则

DNS 是一种独特的协议，因为它可以跨越大量计算机。 此外，由于 DNS 使用 UDP，因此请求和响应是分离的，彼此之间没有直接关系。

下图显示了一个简化的 DNS 请求流，其中包括四个段。 现实中的请求可能更复杂，涉及的段也更多。

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="简化的 DNS 请求流。":::

由于请求和响应段在 DNS 请求流中没有直接相互连接，因此，完整日志记录可能会导致大量重复。

要记录的最有价值的段是对客户端的响应，它提供域名查询、查找结果和客户端的 IP 地址。 虽然许多 DNS 系统只记录此段，但记录其他部分也是有价值的。 例如，DNS 缓存中毒攻击通常利用来自上游服务器的虚假响应。

如果数据源支持完整 DNS 日志记录，并且你已经选择记录多个段，则需要调整查询，以防止 Azure Sentinel 中的数据重复。

例如，你可以使用以下规范化规则修改查询：

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>分析器

实现 DNS 信息模型的 KQL 函数具有以下名称：

| 名称 | 说明 | 用法说明 |
| --- | --- | --- |
| **imDNS** | 一种聚合分析程序，它使用 union 包含来自所有 DNS 源的规范化事件。 |- 如果你想在与源无关的分析中添加或删除源，请更新此分析程序。 <br><br>- 在与源无关的查询中使用此函数。|
| **imDNS\<vendor\>\<product\>** | 特定于源的分析程序为特定源实现规范化，例如 imDNSWindowsOMS。 |- 当没有内置规范化分析程序时，为源添加特定于源的分析程序。 更新聚合分析程序，以包含对新分析程序的引用。 <br><br>- 更新特定于源的分析程序，以解决分析和规范化问题。<br><br>- 使用特定于源的分析程序进行特定于源的分析。|
| | | |

可以从 [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM)部署分析程序。

## <a name="normalized-content"></a>规范化内容

以下内置分析规则现在适用于规范化的 DNS 分析程序：
- 已添加： 
  - NXDOMAIN DNS 查询过多（规范化 DNS）
  - 与挖掘池相关的 DNS 事件（规范化 DNS）
  - 与 ToR 代理相关的 DNS 事件（规范化 DNS）
- 已更新为包含规范化 DNS： 
  - 已知的 Barium 域
  - 已知的 Barium IP 地址  
  - 2021 年 3 月 IoC 匹配中披露的 Exchange Server 漏洞
  - 已知 GALLIUM 域和哈希
  - 已知 IRIDIUM IP
  - NOBELIUM - 域和 IP IOC - 2021 年 3 月
  - 已知 Phosphorus 组域/IP
  - 已知的 STRONTIUM 组域 - 2019 年 7 月
  - Solorigate 网络信标
  - 包含在 DCU 撤销中的 THALLIUM 域
  - 已知的 ZINC Comebacker 和 Klackring 恶意软件哈希


## <a name="schema-details"></a>架构详细信息

DNS 信息模型与 [OSSEM DNS 实体架构](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md)保持一致。

有关详细信息，请参阅 [Internet 编号分配机构 (IANA) 域名系统参数参考](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。

### <a name="log-analytics-fields"></a>Log Analytics 字段

Log Analytics 会为每条记录生成以下字段，你可以在[创建自定义连接器](create-custom-connector.md)时替代这些字段。

| **字段** | **类型** | **说明** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | 日期/时间 | 报告设备生成事件的时间。 |
| **\_ResourceId** | GUID | 报告设备或服务的 Azure 资源 ID，或使用 Syslog、CEF 或 WEF 转发的事件的日志转发器资源 ID。 |
| | | |

> [!NOTE]
> [Azure Monitor](../azure-monitor/logs/log-standard-columns.md) 记录了更多 Log Analytics 字段，但与安全性的相关性较低。
> 

### <a name="event-fields"></a>事件字段

事件字段通用于所有架构，描述活动本身和报告设备。

| 字段 | **类** | 类型 | **示例** | **讨论 (Discussion)** |
| --- | --- | --- | --- | --- |
| **EventMessage** | 可选 | 字符串 | | 一般消息或说明，包含在记录中或者根据记录生成。 |
| **EventCount** | 必需 | 整数 | `1` | 记录描述的事件数。 <br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。 <br><br>对于其他源，应将其设置为 1。 |
| **EventStartTime** | 必需 | 日期/时间 | | 如果源支持聚合且记录表示多个事件，则使用此字段指定生成第一个事件的时间。 <br><br>在其他情况下，它是 [TimeGenerated](#timegenerated) 字段的别名。 |
| **EventEndTime** | | Alias || [TimeGenerated](#timegenerated) 字段的别名。 |
| **EventType** | 必需 | Enumerated | `lookup` | 指示记录报告的操作。 <br><Br> 对于 DNS 记录，此值将为 [DNS 操作代码](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。 |
| **EventSubType** | 可选 | Enumerated || request 或 response。 对于大多数源，[仅记录响应](#guidelines-for-collecting-dns-events)，因此该值通常为“response”。  |
| **EventResult** | 必需 | Enumerated | `Success` | 以下值之一：Success、Partial、Failure、NA（不适用）   。<br> <br>在源记录中，该值可能使用不同的字词提供，这些字词应规范化为上述值。 或者，源记录可能只提供 [EventResultDetails](#eventresultdetails) 字段，应对该字段进行分析以得出 EventResult 值。<br> <br>如果此记录表示请求而不是响应，则设置为 NA。 |
| <a name=eventresultdetails></a>**EventResultDetails** | 必需 | Alias | `NXDOMAIN` | EventResult 字段中报告的结果的原因或详细信息。 它是 [ResponseCodeName](#responsecodename) 字段的别名。|
| **EventOriginalUid** | 可选 | 字符串 | | 原始记录的唯一 ID（如果已由源提供）。 |
| **EventOriginalType**   | 可选    | 字符串  | `lookup` |   原始事件类型或 ID（如果已由源提供）。 |
| <a name ="eventproduct"></a>**EventProduct** | 必需 | 字符串 | `DNS Server` | 生成事件的产品。 该字段在源记录中可能不可用，在这种情况下，它应由分析程序设置。 |
| **EventProductVersion** | 可选 | 字符串 | `12.1` | 生成事件的产品的版本。 该字段在源记录中可能不可用，在这种情况下，它应由分析程序设置。 |
| **EventVendor** | 必需 | 字符串 | `Microsoft` | 生成事件的产品的供应商。 该字段在源记录中可能不可用，在这种情况下，它应由分析程序设置。 |
| **EventSchemaVersion** | 必需 | 字符串 | `0.1.1` | 此处所述的架构版本为 0.1.1。 |
| **EventReportUrl** | 可选 | 字符串 | | 在资源的事件中提供的 URL，提供有关该事件的更多信息。 |
| <a name="dvc"></a>**Dvc** | 必需       | 字符串     |    `ContosoDc.Contoso.Azure` |           发生该事件的设备的唯一标识符。 <br><br>此字段可能又称为 [DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确的设备的云源，请使用与 [EventProduct](#eventproduct) 字段相同的值。         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 建议 | IP 地址 |  `45.21.42.12` |       发生进程事件的设备的 IP 地址。  |
| <a name ="dvchostname"></a>**DvcHostname**         | 建议 | 主机名   | `ContosoDc.Contoso.Azure` |              发生进程事件的设备的主机名。                |
| <a name ="dvcid"></a>**DvcId**               | 可选    | 字符串     || 发生进程事件的设备的唯一 ID。 <br><br>示例： `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| <a name=additionalfields></a>**AdditionalFields** | 可选 | 动态 | | 如果源提供了值得保留的附加信息，请使用原始字段名称保留这些信息，或者创建 AdditionalFields 动态字段，并在其中以键/值对的形式添加这些附加信息。 |
| | | | | |

### <a name="dns-specific-fields"></a>特定于 DNS 的字段

以下字段特定于 DNS 事件。 也就是说，它们中的许多在其他架构中确实有相似之处，因此遵循相同的命名约定。

| 字段 | **类** | 类型 | **示例** | **备注** |
| --- | --- | --- | --- | --- |
| **SrcIpAddr** | 必需 | IP 地址 |  `192.168.12.1 `| 发送 DNS 请求的客户端的 IP 地址。 对于递归 DNS 请求，此值通常为报告设备，并且在大多数情况下设置为 127.0.0.1。 |
| **SrcPortNumber** | 可选 | 整数 |  `54312` | DNS 查询的源端口。 |
| **DstIpAddr** | 可选 | IP 地址 |  `127.0.0.1` | 接收 DNS 请求的服务器的 IP 地址。 对于常规 DNS 请求，此值通常为报告设备，并且在大多数情况下设置为 127.0.0.1。 |
| **DstPortNumber** | 可选 | 整数 |  `53` | 目标端口号 |
| **IpAddr** | | Alias | | SrcIpAddr 的别名 |
| <a name=query></a>**DnsQuery** | 必需 | FQDN | `www.malicious.com` | 需要解析的域。 <br><br>请注意，有些源以不同的格式发送查询。 最值得注意的是，在 DNS 协议本身中，查询在末尾包含一个点。 应删除这个点。<br><br>虽然 DNS 协议允许单个请求中存在多个查询，但这种情况很少见（即便有）。 如果请求有多个查询，则将第一个查询存储在此字段中，然后根据需要将其余查询保留在 [AdditionalFields](#additionalfields) 字段中。 |
| **Domain** | | Alias || [Query](#query) 的别名。 |
| **DnsQueryType** | 可选 | 整数 | `28` | 此字段可能包含 [DNS 资源记录类型代码](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。 |
| **DnsQueryTypeName** | 必需 | Enumerated | `AAAA` | 此字段可能包含 [DNS 资源记录类型](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)名称。 <br><br>注意：IANA 未定义值的大小写，因此，分析功能必须根据需要规范化大小写。 如果源仅提供数值查询类型代码，而不提供查询类型名称，则分析程序必须包含要使用此值来扩充的查找表。 |
| <a name=responsename></a>**DnsResponseName** | 可选 | 字符串 | | 响应的内容，包含在记录中。<br> <br> DNS 响应数据在报告设备之间不一致，分析起来很复杂，并且对于与源无关的分析的价值较小。 因此，信息模型不需要分析和规范化，Azure Sentinel 会使用辅助函数来提供响应信息。 有关详细信息，请参阅[处理 DNS 响应](#handling-dns-response)。|
| <a name=responsecodename></a>**DnsResponseCodeName** |  必需 | Enumerated | `NXDOMAIN` | [DNS 响应代码](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。 <br><br>注意：IANA 未定义值的大小写，因此，分析功能必须规范化大小写。 如果源仅提供数值响应代码，而不提供响应代码名称，则分析程序必须包含要使用此值来扩充的查找表。 <br><br> 如果此记录表示请求而不是响应，则设置为 NA。 |
| **DnsResponseCode** | 可选 | 整数 | `3` | [DNS 数值响应代码](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。|
| **TransactionIdHex** | 建议 | 字符串 | | DNS 唯一十六进制事务 ID。 |
| **NetworkProtocol** | 可选 | Enumerated | `UDP` | 网络解析事件使用的传输协议。 其值可以是 UDP 或 TCP；对于 DNS，最常设置为 UDP。 |
| **DnsQueryClass** | 可选 | 整数 | | [DNS 类 ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。<br> <br>在实践中，仅使用 IN 类 (ID 1)，因此该字段并不重要。|
| **DnsQueryClassName** | 可选 | 字符串 | `"IN"` | [DNS 类名](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。<br> <br>在实践中，仅使用 IN 类 (ID 1)，因此该字段并不重要。 |
| <a name=flags></a>**DnsFlags** | 可选 | 字符串列表 | `["DR"]` | 标志字段，由报告设备提供。 如果在多个字段中提供标志信息，请使用逗号作为分隔符将它们连接起来。 <br><br>由于 DNS 标志分析起来很复杂，分析功能很少使用它，因此无需分析和规范化，Azure Sentinel 会使用辅助函数来提供标志信息。 有关详细信息，请参阅[处理 DNS 响应](#handling-dns-response)。|
| <a name=UrlCategory></a>**UrlCategory** |   | 字符串 | `Educational \\ Phishing` | DNS 事件源还可以查找所请求域的类别。 为了与 Azure Sentinel 网络架构保持一致，此字段称为 UrlCategory。 <br><br>我们还添加了 DomainCategory，作为适合 DNS 的别名。 |
| **DomainCategory** | | Alias | | [UrlCategory](#UrlCategory) 的别名。 |
| **ThreatCategory** |   | 字符串 |   | 如果 DNS 事件源还提供 DNS 安全性，它还可以评估 DNS 事件。 例如，它可以在威胁情报数据库中搜索 IP 地址或域，并为域或 IP 地址分配威胁类别。 |
| **EventSeverity** | 可选 | 字符串 | `"Informational"` | 如果 DNS 事件源还提供 DNS 安全性，则它可以评估 DNS 事件。 例如，它可以在威胁情报数据库中搜索 IP 地址或域，并根据评估结果分配严重性。 |
| **DvcAction** | 可选 | 字符串 | `"Blocked"` | 如果 DNS 事件源还提供 DNS 安全性，它可能会对请求执行操作，例如阻止它。 |
| | | | | |

### <a name="additional-aliases-deprecated"></a>其他别名（已弃用）

以下字段是为了向后兼容而保留的别名：
- Query（DnsQuery 的别名）
- QueryType（DnsQueryType 的别名）
- QueryTypeName（DnsQueryTypeName 的别名）
- ResponseName（DnsReasponseName 的别名）
- ResponseCodeName（DnsResponseCodeName 的别名）
- ResponseCode（DnsResponseCode 的别名）
- QueryClass（DnsQueryClass 的别名）
- QueryClassName（DnsQueryClassName 的别名）
- Flags（DnsFlags 的别名）

### <a name="additional-entities"></a>其他实体

事件围绕用户、主机、进程或文件等实体进行演变。 每个实体可能需要多个字段来描述。 例如，主机可能具有名称和 IP 地址。 此外，单个记录可能包括相同类型的多个实体，例如源主机和目标主机。 上面介绍的 DNS 架构包括描述实体的字段。 如果源包含描述这些实体的其他信息，请根据以下实体添加更多字段，以捕获此信息。 有关实体的详细信息，请参阅 [Azure Sentinel 中的规范化](normalization.md)。

| **实体** | **Fields** | 类型 | **必填字段** | **备注** |
| --- | --- | --- | --- | --- |
| 演员 | Actor\* | 用户 |  | 大多数 DNS 事件源不提供用户信息，这些信息通常不是 DNS 协议的一部分。 <br><br>在某些情况下，报告设备通常通过以下方式来提供用户信息：将源 IP 地址解析为用户信息。 在这种情况下，请按照架构实体文档中的描述来表示用户。 使用 Actor 作为描述符，因为信息基于源 IP 地址。 <br><br>注意：使用 Actor 作为实体描述符时，无需追加 User 字段。 |
| **源设备** | Src\* | 设备 | `SrcIpAddr` | DNS 事件源通常报告请求源的 IP 地址，因此 SrcIpAddr 是必填字段。 <br><br>如果报告设备提供有关请求源的详细信息，或者你要扩充数据，请按照设备实体准则，通过将 Src 用作字段前缀来进行规范化。 |
| **目标设备** | Dst\* | 设备 |  | DNS 事件源通常不报告有关请求目标的信息。 如果报告设备提供有关请求目标的信息，或者你要扩充数据，请按照设备实体准则，通过将 Dst 用作前缀来进行规范化。 |
| **报告设备** | Dvc\* | 设备 | `Dvc` | 大多数事件包括有关报告设备的信息。 对这些字段使用前缀 Dvc|
| **进程信息** | 过程\* | 过程 |  | 与在客户端设备上生成 DNS 查询的进程相关的信息。 |
| | | | | |

## <a name="handling-dns-response"></a>处理 DNS 响应

在大多数情况下，记录的 DNS 事件不包括响应信息，这些信息的量可能很大而且很详细。 如果记录中包含较多响应信息，请将显示在记录中的响应信息存储在 [ResponseName](#responsename) 字段中。

你还可以提供一个额外的名为 `_imDNS<vendor>Response_` 的 KQL 函数，它将未分析的响应作为输入并返回具有以下结构的动态值：

```kql
[
    {
        "part": "answer"
        "query": "yahoo.com."
        "TTL": 1782
        "Class": "IN"
        "Type": "A"
        "Response": "74.6.231.21"
    }
    {
        "part": "authority"
        "query": "yahoo.com."
        "TTL": 113066
        "Class": "IN"
        "Type": "NS"
        "Response": "ns5.yahoo.com"
    }
    ...
]
```

动态值中每个字典中的字段对应于每个 DNS 响应中的字段。 `part` 条目应包含 `answer`、`authority` 或 `additional`，以反映字典所属的响应部分。

> [!TIP]
> 若要确保最佳性能，请仅在需要时调用 `imDNS<vendor>Response` 函数；若要在此基础上获得更好的性能，应仅在初始筛选后调用。
>

## <a name="handling-dns-flags"></a>处理 DNS 标志

无需分析和规范化标志数据， 而应将报告设备提供的标志数据存储在 [Flags](#flags) 字段中。

你还可以提供一个额外的名为 `_imDNS<vendor>Flags_` 的 KQL 函数，它将未分析的响应作为输入并返回一个动态列表，其中包含按以下顺序表示每个标志的布尔值：

- 已通过身份验证 (AD)
- 权威 (AA)
- 已禁用检查 (CD)
- 递归可用 (RA)
- 需要递归 (RD)
- 已截断 (TC)
- Z

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Sentinel 中的规范化](normalization.md)
- [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)
- [Azure Sentinel 数据规范化架构参考](normalization-schema.md)
- [Azure Sentinel 文件事件规范化架构参考（公共预览版）](file-event-normalization-schema.md)
- [Azure Sentinel 进程事件规范化架构参考](process-events-normalization-schema.md)
- [Azure Sentinel 注册表事件规范化架构参考（公共预览版）](registry-event-normalization-schema.md)
