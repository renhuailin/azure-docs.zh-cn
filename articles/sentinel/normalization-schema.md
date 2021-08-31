---
title: Azure Sentinel 网络规范化架构参考（公共预览版） | Microsoft Docs
description: 本文显示了 Azure Sentinel 数据规范化架构。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/15/2021
ms.author: yelevin
ms.openlocfilehash: f93e95936a268a2ac0490b5151167a2589d0070c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725146"
---
# <a name="azure-sentinel-network-normalization-schema-reference-public-preview"></a>Azure Sentinel 网络规范化架构参考（公共预览版）

网络规范化架构用于描述报告的网络事件，并由 Azure Sentinel 用于启用与源无关分析。

有关详细信息，请参阅[规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)。
## <a name="terminology"></a>术语

Azure Sentinel 架构中使用了以下术语：

| 术语 | 定义 |
| ---- | ---------- |
| **报告设备** | 向 Azure Sentinel 发送记录的系统。 它可能不是记录的主题系统。 |
| **记录** | 从报告设备发送的数据单位。 这通常称为“日志”、“事件”或“警报”，但不一定是这三种称法之一。 |
|

## <a name="data-types-and-formats"></a>数据类型和格式

应根据以下准则规范化值。 对于规范化的字段，必须遵循此准则；对于其他字段，建议遵循。 

| 数据类型 | 物理类型 | 格式和值 |
| --------- | ------------- | ---------------- |
| **日期/时间** | 根据引入方法功能，按降序优先级使用：<ul><li>Log Analytics 内置的日期时间类型</li><li>使用 Log Analytics 日期时间数字表示形式的整数字段</li><li>使用 Log Analytics 日期时间数字表示形式的字符串字段</li></ul> | Log Analytics 日期时间表示形式。 <br></br>本质上，日志分析的日期和时间表示形式与 Unix 时间的表示形式类似，但两者实际上是不同的。 请参阅这些转换准则。 <br></br>日期和时间应该经过时区调整。 |
| **MAC 地址** | 字符串 | 冒分十六进制表示法 |
| **IP 地址** | IP 地址 | 架构没有单独的 IPv4 和 IPv6 地址。 任一 IP 地址字段都可以包含 IPv4 地址或 IPv6 地址：<ul><li>采用点分十进制表示法的 IPv4</li><li>采用 8 个 16 位段表示法的 IPv6，允许本文所述的短格式。</li></ul> |
| **用户** | 字符串 | 可以使用以下 3 个用户字段：<ul><li>用户名</li><li>用户 UPN</li><li>用户域</li></ul> |
| **用户 ID** | 字符串 | 目前支持以下 2 个用户 ID：<ul><li>用户 SID</li><li>Azure Active Directory ID</li></ul> |
| **设备** | 字符串 | 支持以下 3 个设备/主机列：<ul><li>ID</li><li>名称</li><li>完全限定的域名 (FQDN)</li></ul> |
| **国家/地区** | 字符串 | 根据以下优先级使用 ISO 3166-1 的字符串：<ul><li>Alpha-2 代码（例如，US 表示美国）</li><li>Alpha-3 代码（例如，USA 表示美国）</li><li>短名称</li></ul> |
| **区域** | 字符串 | 使用 ISO 3166-2 的国家/地区辖区名称 |
| **城市** | 字符串 | |
| **经度** | Double | ISO 6709 坐标表示形式（带符号的十进制数） |
| **纬度** | Double | ISO 6709 坐标表示形式（带符号的十进制数） |
| **哈希算法** | 字符串 | 支持以下 4 个哈希列：<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **文件类型** | 字符串 | 文件类型的类型：<ul><li>扩展名</li><li>类</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>网络会话表架构

下面是网络会话表的架构，版本为 1.0.0

| 字段名称 | 值类型 | 示例 | 说明 | 关联的 OSSEM 实体 |
|-|-|-|-|-|
| **EventType** | 字符串 | 交通 | 要收集的事件的类型 | 事件 |
| **EventSubType** | 字符串 | 身份验证 | 类型的附加说明（如果适用） | 事件 |
| **EventCount** | Integer  | 10 | 聚合的事件数（如果适用）。 | 事件 |
| **EventEndTime** | 日期/时间 | 请参阅“数据类型” | 事件的结束时间 | 事件 |
| EventMessage | 字符串 |  访问被拒绝 | 一般消息或说明，包含在记录中或者从记录生成 | 事件 |
| **DvcIpAddr** | IP 地址 |  23.21.23.34 | 生成记录的设备的 IP 地址 | 设备，<br>IP |
| **DvcMacAddr** | 字符串 | 06:10:9f:eb:8f:14 | 从中发送了事件的报告设备的网络接口的 MAC 地址。 | 设备，<br>Mac |
| **DvcHostname** | 设备名称（字符串） | syslogserver1.contoso.com | 生成消息的设备的设备名称。 | 设备 |
| **EventProduct** | 字符串 | OfficeSharepoint | 生成事件的产品。 | 事件 |
| **EventProductVersion** | 字符串 | 9.0 |  生成事件的产品的版本。 | 事件 |
| **EventResourceId** | 设备 ID（字符串） | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | 生成消息的设备的资源 ID。 | 事件 |
| **EventReportUrl** | 字符串 | https://192.168.1.1/repoerts/ae3-56.htm | 报告设备创建的完整报告的链接 | 事件 |
| **EventVendor** | 字符串 | Microsoft | 生成事件的产品的供应商。 | 事件 |
| **EventResult** | 多值：Success、Partial、Failure、[空]（字符串） | 成功 | 针对活动报告的结果。 不适用时为空值。 | 事件 |
| **EventResultDetails** | 字符串 | 密码错误 | EventResult 中报告的结果的原因或详细信息 | 事件 |
| **EventSchemaVersion** | Real | 0.1 | Azure Sentinel 架构版本。 目前为 0.1。 | 事件 |
| **EventSeverity** | 字符串 | 低 | 如果报告的活动会造成安全影响，则指示影响的严重性。 | 事件 |
| **EventOriginalUid** | 字符串 | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | 报告设备中的记录 ID。 | 事件 |
| **EventStartTime** | 日期/时间 | 请参阅“数据类型” | 事件的开始时间 | 事件 |
| **TimeGenerated** | 日期/时间 | 请参阅“数据类型” | 事件的发生时间，由报告源报告。 | 自定义字段 |
| **EventTimeIngested** | 日期/时间 | 请参阅“数据类型” | 将事件引入 Azure Sentinel 的时间。 将由 Azure Sentinel 添加。 | 事件 |
| **EventUid** | Guid（字符串） | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | 由 Azure Sentinel 用来标记某行的唯一标识符。 | 事件 |
| **NetworkApplicationProtocol** | 字符串 | HTTPS | 连接或会话使用的应用程序层协议。 | 网络 |
| **DstBytes** | int | 32455 | 从连接或会话的目标发送到源的字节数。 | 目标 |
| **SrcBytes** | int | 46536 | 从连接或会话的源发送到目标的字节数。 | 源 |
| **NetworkBytes** | int | 78991 | 双向发送的字节数。 如果 BytesReceived 和 BytesSent 都存在，则 BytesTotal 应等于它们的总和。 | 网络 |
| **NetworkDirection** | 多值：Inbound、Outbound（字符串） | 入站 | 连接或会话的方向：入站到组织或者从组织出站。 | 网络 |
| **DstGeoCity** | 字符串 | Burlington | 与目标 IP 地址关联的城市 | 目标、<br>地域 |
| **DstGeoCountry** | 国家/地区（字符串） | 美国 | 与源 IP 地址关联的国家/地区 | 目标、<br>地域 |
| **DstDvcHostname** | 设备名称（字符串） |  victim_pc | 目标设备的设备名称 | 目标<br>设备 |
| **DstDvcFqdn** | 字符串 | victim_pc.contoso.local | 在其中创建了日志的主机的完全限定域名 | 目标、<br>设备 |
| **DstDomainHostname** | 字符串 | CONTOSO | 目标的域。目标主机的域（网站、域名等），例如，用于 DNS 查找或 NS 查找 | 目标 |
| **DstInterfaceName** | 字符串 | Microsoft Hyper-V 网络适配器 | 由目标设备用来建立连接或会话的网络接口。 | 目标 |
| **DstInterfaceGuid** | 字符串 | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | 用于身份验证请求的网络接口的 GUID  | 目标 |
| **DstIpAddr** | IP 地址 | 2001:db8::ff00:42:8329 | 连接或会话目标的 IP 地址，最常见的称法是网络数据包中的目标 IP | 目标、<br>IP |
| **DstDvcIpAddr** | IP 地址 | 75.22.12.2 | 不直接与网络数据包关联的设备的目标 IP 地址 | 目标、<br>设备，<br>IP
| **DstGeoLatitude** | 纬度（双精度） | 44.475833 | 与目标 IP 地址关联的地理坐标的纬度 | 目标、<br>地域 |
| **DstMacAddr** | 字符串 | 06:10:9f:eb:8f:14 | 连接或会话在其上终止的网络接口的 MAC 地址，最常见的称法是网络数据包中的目标 MAC | 目标、<br>MAC |
| **DstDvcMacAddr** | 字符串 | 06:10:9f:eb:8f:14 | 不直接与网络数据包关联的设备的目标 MAC 地址。 | 目标、<br>设备，<br>MAC |
| **DstDvcDomain** | 字符串 | CONTOSO | 目标设备的域。 | 目标、<br>设备 |
| **DstPortNumber** | 整数 | 443 | 目标 IP 端口。 | 目标、<br>端口 |
| **DstGeoRegion** | 区域（字符串） | 佛蒙特州 | 与目标 IP 地址关联的国家/地区中的区域 | 目标、<br>地域 |
| **DstResourceId** | 设备 ID（字符串） |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | 目标设备的资源 ID。 | 目标 |
| **DstNatIpAddr** | IP 地址 | 2::1 | 如果中介 NAT 设备（例如防火墙）报告了该值，则该值是由 NAT 设备用来与源通信的 IP 地址。 | 目标 NAT、<br>IP |
| **DstNatPortNumber** | int | 443 | 如果中介 NAT 设备（例如防火墙）报告了该值，则该值是由 NAT 设备用来与源通信的端口。 | 目标 NAT、<br>端口 |
| **DstUserSid** | 用户 SID |  S-12-1445 | 与会话目标关联的标识的用户 ID。 通常，它是用来对服务器进行身份验证的标识。 有关详细信息，请参阅“数据类型”。 | 目标、<br>用户 |
| **DstUserAadId** | 字符串 (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | 位于会话目标端的用户的 Azure AD 帐户对象 ID | 目标、<br>用户 |
| **DstUserName** | 用户名（字符串） | johnd | 与会话目标关联的标识的用户名。  | 目标、<br>用户 |
| **DstUserUpn** | 字符串 | johnd@anon.com | 与会话目标关联的标识的 UPN。 | 目标、<br>用户 |
| **DstUserDomain** | 字符串 | WORKGROUP | 位于会话目标中的帐户的域名或计算机名 | 目标、<br>用户 |
| **DstZone** | 字符串 | Dmz | 目标的网络区域，由报告设备定义。 | 目标 |
| **DstGeoLongitude** | 经度（双精度） | -73.211944 | 与目标 IP 地址关联的地理坐标的经度 | 目标、<br>地域 |
| **DvcAction** | 多值：Allow、Deny、Drop（字符串） | Allow | 如果中介设备（例如防火墙）报告了该值，则该值是该设备执行的操作。 | 设备 |
| **DvcInboundInterface** | 字符串 | eth0 | 如果中介设备（例如防火墙）报告了该值，则该值是该设备用来连接到源设备的网络接口。 | 设备 |
| **DvcOutboundInterface** | 字符串  | 以太网适配器以太网 4 | 如果中介设备（例如防火墙）报告了该值，则该值是该设备用来连接到目标设备的网络接口。 | 设备 |
| **NetworkDuration** | 整数 | 1500 | 完成网络会话或连接所花费的时间，以毫秒为单位 | 网络 |
| **NetworkIcmpCode** | 整数 | 34 | 对于 ICMP 消息，该值是 ICMP 消息类型的数字值（RFC 2780 或 RFC 4443）。 | 网络 |
| **NetworkIcmpType** | 字符串 | 目标不可访问 | 对于 ICMP 消息，该值是 ICMP 消息类型的文本表示形式（RFC 2780 或 RFC 4443）。 | 网络 |
| **DstPackets** | int  | 446 | 从连接或会话的目标发送到源的数据包数。 数据包的含义由报告设备定义。 | 目标 |
| **SrcPackets** | int  | 6478 | 从连接或会话的源发送到目标的数据包数。 数据包的含义由报告设备定义。 | 源 |
| **NetworkPackets** | int  | 0 | 双向发送的数据包数。 如果 PacketsReceived 和 PacketsSent 都存在，则 BytesTotal 应等于它们的总和。 | 网络 |
| **HttpRequestTime** | 整数 | 700 | 将请求发送到服务器所花费的时间（如果适用）。 | Http |
| **HttpResponseTime** | 整数 | 800 | 在服务器中接收响应所花费的时间（如果适用）。 | Http |
| **NetworkRuleName** | 字符串 | AnyAnyDrop | 确定 DeviceAction 时所依据的规则的名称或 ID | 网络 |
| **NetworkRuleNumber** | int |  23 | 匹配的规则编号  | 网络 |
| **NetworkSessionId** | 字符串 | 172_12_53_32_4322__123_64_207_1_80 | 报告设备报告的会话标识符。 例如，在身份验证后特定应用程序的会话标识符为 L7 | 网络 |
| **SrcGeoCity** | 字符串 | Burlington | 与源 IP 地址关联的城市 | 源、<br>地域 |
| **SrcGeoCountry** | 国家/地区（字符串） | 美国 | 与源 IP 地址关联的国家/地区 | 源、<br>地域 |
| **SrcDvcHostname** | 设备名称（字符串） |  villain | 源设备的设备名称 | 源、<br>设备 |
| **SrcDvcFqdn** | 字符串 | Villain.malicious.com | 在其中创建了日志的主机的完全限定域名 | 源、<br>设备 |
| **SrcDvcDomain** | 字符串 | EVILORG | 从中发起了会话的设备的域 | 源、<br>设备 |
| **SrcDvcOs** | 字符串 | iOS | 源设备的 OS | 源、<br>设备 |
| **SrcDvcModelName** | 字符串 | Samsung Galaxy Note | 源设备的型号名称 | 源、<br>设备 |
| **SrcDvcModelNumber** | 字符串 | 10.0 | 源设备的型号 | 源、<br>设备 |
| **SrcDvcType** | 字符串 | 移动型 | 源设备的类型 | 源、<br> 设备 |
| **SrcIntefaceName** | 字符串 | eth01 | 由源设备用来建立连接或会话的网络接口。 | 源 |
| **SrcInterfaceGuid** | 字符串 | 46ad544b-eaf0-47ef-827c-266030f545a6 | 所用网络接口的 GUID | 源 |
| **SrcIpAddr** | IP 地址 | 77.138.103.108 | 从中发起了连接或会话的 IP 地址。 | 源、<br>IP |
| **SrcDvcIpAddr** | IP 地址 | 77.138.103.108 | 不直接与网络数据包关联的设备的源 IP 地址（由提供程序收集，或显式计算）。 | 源、<br>设备，<br>IP |
| **SrcGeoLatitude** | 纬度（双精度） | 44.475833 | 与源 IP 地址关联的地理坐标的纬度 | 源、<br>地域 |
| **SrcGeoLongitude** | 经度（双精度） | -73.211944 | 与源 IP 地址关联的地理坐标的经度 | 源、<br>地域 |
| **SrcMacAddr** | 字符串 | 06:10:9f:eb:8f:14 | 从中发起了连接或会话的网络接口的 MAC 地址。 | 源、<br>Mac |
| **SrcDvcMacAddr** | 字符串 | 06:10:9f:eb:8f:14 | 不直接与网络数据包关联的设备的源 MAC 地址。 | 源、<br>设备，<br>Mac |
| **SrcPortNumber** | 整数 | 2335 | 从中发起了连接的 IP 端口。 可能与包含多个连接的会话无关。 | 源、<br>端口 |
| **SrcGeoRegion** | 区域（字符串） | 佛蒙特州 | 与源 IP 地址关联的国家/地区中的区域 | 源、<br>地域 |
| **SrcResourceId** | 字符串 | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | 生成消息的设备的资源 ID。 | 源 |
| **SrcNatIpAddr** | IP 地址 | 4.3.2.1 | 如果中介 NAT 设备（例如防火墙）报告了该值，则该值是由 NAT 设备用来与目标通信的 IP 地址。 | 源 NAT、<br>IP |
| **SrcNatPortNumber** | 整数 | 345 | 如果中介 NAT 设备（例如防火墙）报告了该值，则该值是由 NAT 设备用来与目标通信的端口。 | 源 NAT、<br>端口 |
| **SrcUserSid** | 用户 ID（字符串） | S-15-1445 | 与会话源关联的标识的用户 ID。 通常，这是在客户端上执行操作的用户。 有关详细信息，请参阅“数据类型”。 | 源、<br>用户 |
| **SrcUserAadId** | 字符串 (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | 位于会话源端的用户的 Azure AD 帐户对象 ID | 源、<br>用户 |
| **SrcUserName** | 用户名（字符串） | bob | 与会话源关联的标识的用户名。 通常，这是在客户端上执行操作的用户。 有关详细信息，请参阅“数据类型”。 | 源<br>用户 |
| **SrcUserUpn** | 字符串 | bob@alice.com | 发起会话的帐户的 UPN | 源、<br>用户 |
| **SrcUserDomain** | 字符串 | 桌面 | 发起会话的帐户的域 | 源、<br>用户 |
| **SrcZone** | 字符串 | 点击 | 源的网络区域，由报告设备定义。 | 源 |
| **NetworkProtocol** | 字符串 | TCP | 连接或会话使用的 IP 协议。 通常为 TCP、UDP 或 ICMP | 网络 |
| **CloudAppName** | 字符串 | Facebook | HTTP 应用程序的目标应用程序的名称，由代理标识。 | 云 |
| **CloudAppId** | 字符串 | 124 | HTTP 应用程序的目标应用程序的 ID，由代理标识。 此值通常特定于所用的代理。 | 云 |
| **CloudAppOperation** | 字符串 | DeleteFile | 用户在 HTTP 应用程序的目标应用程序上下文中执行的操作，由代理标识。 此值通常特定于所用的代理。 | 云 |
| **CloudAppRiskLevel** | String | 3 | 与 HTTP 应用程序关联的风险级别，由代理标识。 此值通常特定于所用的代理。 | 云 |
| **FileName** | 字符串 | ImNotMalicious.exe | 通过网络连接为提供文件名信息的协议（例如 FTP 和 HTTP）传输的文件名。 | 文件 |
| **文件路径** | 字符串 | C:\Malicious\ImNotMalicious.exe | 文件的完整路径，包括文件名 | 文件 |
| **FileHashMd5** | 字符串 | 51BC68715FC7C109DCEA406B42D9D78F | 通过网络连接为协议传输的文件的 MD5 哈希值。 | 文件 |
| **FileHashSha1** | 字符串 | 491AE3…C299821476F4 | 通过网络连接为协议传输的文件的 SHA1 哈希值。 | 文件 |
| **FileHashSha256** | 字符串 | 9B8F8EDB…C129976F03 | 通过网络连接为协议传输的文件的 SHA256 哈希值。 | 文件 |
| **FileHashSha512** | 字符串 | 5E127D…F69F73F01F361 | 通过网络连接为协议传输的文件的 SHA512 哈希值。 | 文件 |
| **FileExtension** |  字符串 | exe | 通过网络连接为协议（例如 FTP 和 HTTP）传输的文件的类型。 | 文件
| **FileMimeType** | 字符串 | application/msword | 通过网络连接为协议（例如 FTP 和 HTTP）传输的文件的 MIME 类型 | 文件 |
| **FileSize** | 整数 | 23500 | 通过网络连接为协议传输的文件的文件大小，以字节为单位。 | 文件 |
| **HttpVersion** | 字符串 | 2.0 | HTTP/HTTPS 网络连接的 HTTP 请求版本。 | Http |
| **HttpRequestMethod** | 字符串 | GET | HTTP/HTTPS 网络会话的 HTTP 方法。 | Http |
| **HttpStatusCode** | 字符串 | 404 | HTTP/HTTPS 网络会话的 HTTP 状态代码。 | Http |
| **HttpContentType** | 字符串 | multipart/form-data；boundary=something | HTTP/HTTPS 网络会话的 HTTP 响应内容类型头。 | Http |
| **HttpReferrerOriginal** | 字符串 | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS 网络会话的 HTTP referrer 头。 | Http |
| **HttpUserAgentOriginal** | 字符串 | Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36 | Http/HTTPS 网络会话的 HTTP 用户代理头。 | Http |
| **HttpRequestXff** | 字符串 | 120.12.41.1 | HTTP/HTTPS 网络会话的 HTTP X-Forwarded-For 头。 | Http |
| **UrlCategory** | 字符串 | 搜索引擎 | 定义的 URL 分组（也可能只是基于 URL 中的域），与该 URL 的内容相关（例如：成人、新闻、广告、暂停域等） | url |
| **UrlOriginal** | 字符串 | https:// contoso.com/fo/?k=v&q=u#f | HTTP/HTTPS 网络会话的 HTTP 请求 URL。 | URL |
| **UrlHostname** | 字符串 | contoso.com | HTTP/HTTPS 网络会话的 HTTP 请求 URL 的域部分。 | URL |
| **ThreatCategory** | 字符串 | Trojan | 安全系统（例如 IPS 的 Web 安全网关）识别到的威胁的类别，与此网络会话相关联。 | 威胁 |
| **ThreatId** | 字符串 | Tr.124 | 安全系统（例如 IPS 的 Web 安全网关）识别到的威胁的 ID，与此网络会话相关联。 | 威胁 |
| **ThreatName** | 字符串 | EICAR 测试文件 | 识别到的威胁或恶意软件的名称 | 威胁 |
| **AdditionalFields** | 动态（JSON 包） | {<br>Property1: “val1”，<br>Property2: “val2”<br>} | 如果架构中没有相应的列匹配，则可将附加字段存储在 JSON 包中。<br>若要执行查询时分析，我们建议不要使用此方法，因为将数据打包到 JSON 中会降低查询性能。 建议提升附加列。<br>对于将来的引入时分析方案，附加数据肯定会收集到此 JSON 包列中。 | 自定义字段 |
|

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Sentinel 中的规范化](normalization.md)
- [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)
- [Azure Sentinel 文件事件规范化架构参考（公共预览版）](file-event-normalization-schema.md)
- [Azure Sentinel DNS 规范化架构参考](dns-normalization-schema.md)
- [Azure Sentinel 进程事件规范化架构参考](process-events-normalization-schema.md)
- [Azure Sentinel 注册表事件规范化架构参考（公共预览版）](registry-event-normalization-schema.md)

