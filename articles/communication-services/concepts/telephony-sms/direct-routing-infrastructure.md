---
title: Azure 直接路由基础结构要求 - Azure 通信服务
description: 熟悉 Azure 通信服务直接路由配置的基础结构要求
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: a840aa9ac67ff751ef6889c5375c5b79880e79b9
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251202"
---
# <a name="azure-direct-routing-infrastructure-requirements"></a>Azure 直接路由基础结构要求 

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

 
本文介绍了基础结构、许可和会话边界控制器 (SBC) 连接详细信息，在计划 Azure 直接路由部署时需要牢记这些详细信息。


## <a name="infrastructure-requirements"></a>基础结构要求
对于部署 Azure 直接路由，针对支持的 SBC、域的基础结构要求以及其他网络连接性要求如下表所示：  

|基础结构要求|需要满足以下条件|
|:--- |:--- |
|会话边界控制器 (SBC)|受支持的 SBC。 有关详细信息，请参阅[受支持的 SBC](#supported-session-border-controllers-sbcs)。|
|连接到 SBC 的电话服务中继|连接到 SBC 的一个或多个电话服务中继。 SBC 一端通过直接路由连接 Azure 通信服务。 SBC 还可以连接到第三方电话实体，如 PBX、模拟电话适配器等。 连接到 SBC 的任何 PSTN 连接选项都将起作用。 （对于 SBC 的 PSTN 中继配置，请咨询 SBC 供应商或中继提供商。）|
|Azure 订阅|用于创建通信服务资源的 Azure 订阅，以及针对 SBC 的配置和连接。|
|通信服务访问令牌|若要进行调用，需要具有 `voip` 作用域的有效访问令牌。 请参阅[访问令牌](../identity-model.md#access-tokens)|
|用于 SBC 的公共 IP 地址|可用于连接到 SBC 的公共 IP 地址。 基于 SBC 的类型，SBC 可以使用 NAT。|
|用于 SBC 的完全限定的域名 (FQDN)|用于 SBC 的 FQDN，其中 FQDN 的域部分与 Microsoft 365 或 Office 365 组织中的注册域不匹配。 有关详细信息，请参阅 [SBC 域名](#sbc-domain-names)。|
|SBC 的公共 DNS 条目 |将 SBC FQDN 映射到公共 IP 地址的公共 DNS 条目。 |
|用于 SBC 的受信任公共证书 |用于 SBC 的证书，旨在用于与 Azure 直接路由进行所有通信。 有关详细信息，请参阅[用于 SBC 的受信任公共证书](#public-trusted-certificate-for-the-sbc)。|
|用于 SIP 信号和媒体的防火墙 IP 地址和端口 |SBC 与云中的以下服务进行通信：<br/><br/>SIP 代理，用于处理信号<br/>媒体处理器，用于处理媒体<br/><br/>这两个服务在 Microsoft 云中有单独的 IP 地址，本文档稍后将对此进行介绍。


## <a name="sbc-domain-names"></a>SBC 域名

不使用 Office 365 的客户可以使用任何可获取公共证书的域名。

下表显示了为租户注册的 DNS 名称的示例、该名称是否可用作 SBC 的完全限定的域名 (FQDN) 以及有效 FQDN 名称的示例：

|DNS 名称|可用于 SBC FQDN|FQDN 名称示例|
|:--- |:--- |:--- |
contoso.com|是|有效名称：<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|否|SBC 名称不支持使用 *.onmicrosoft.com 域

如果你是 Office 365 的客户，则 SBC 域名不得与在 Office 365 租户域中注册的域名匹配。 以下是 Office 365 和 Azure 通信服务共存的示例：

|在 Office 365 中注册的域|Teams 中的 SBC FQDN 示例|Azure 通信服务中的 SBC FQDN 名称示例|
|:--- |:--- |:--- |
contoso.com（二级域）|sbc.contoso.com（二级域中的名称）|sbc.acs.contoso.com（三级域中的名称）<br/>sbc.fabrikam.com（不同域中的任何名称）|
|o365.contoso.com（三级域）|sbc.o365.contoso.com（三级域中的名称）|sbc.contoso.com（二级域中的名称）<br/>sbc.acs.o365.contoso.com（四级域中的名称）<br/>sbc.fabrikam.com（不同域中的任何名称）

SBC 配对适用于通信服务资源级别，这意味着可以将多个 SBC 与一个通信服务资源配对，但不能将一个 SBC 与多个通信服务资源配对。 与不同资源进行配对需要具有唯一的 SBC FQDN。

## <a name="public-trusted-certificate-for-the-sbc"></a>用于 SBC 的受信任公共证书

Microsoft 建议通过生成证书签名请求 (CSR) 来请求 SBC 的证书。 有关为 SBC 生成 CSR 的具体说明，请参阅由 SBC 供应商提供的互连说明或文档。 

  > [!NOTE]
  > 大多数证书颁发机构 (CA) 要求私钥大小至少为 2048。 请在生成 CSR 时记住这一点。

证书必须将 SBC FQDN 作为公用名 (CN) 或使用者可选名称 (SAN) 字段。 应直接从证书颁发机构（而不是从中间提供程序）颁发证书。

此外，通信服务直接路由支持 CN 和/或 SAN 中的通配符，并且通配符需要符合标准的 [RFC HTTP Over TLS](https://tools.ietf.org/html/rfc2818#section-3.1)。 

一个示例是使用 `\*.contoso.com`，它与 SBC FQDN `sbc.contoso.com` 匹配，但与 `sbc.test.contoso.com` 不匹配。

该证书需要通过以下根证书颁发机构之一生成：

- AffirmTrust
- AddTrust 外部 CA 根
- Baltimore CyberTrust 根*
- Buypass
- CyberTrust
- 类 3 公共主要证书颁发机构
- Comodo Secure Root CA
- Deutsche Telekom 
- DigiCert 全局根 CA
- DigiCert 高保障 EV 根 CA
- Entrust
- GlobalSign
- Go Daddy
- GeoTrust
- Verisign, Inc. 
- SSL.com
- Starfield
- 适用于 Microsoft 的 Symantec 企业移动根 
- SwissSign
- Thawte 时间戳 CA
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis
- USERTrust RSA 证书颁发机构
- Hongkong Post 根 CA 1、2、3
- Sectigo 根 CA

Microsoft 将根据客户请求添加更多证书颁发机构。 

## <a name="sip-signaling-fqdns"></a>SIP 信号：FQDN 

通信服务直接路由的连接点为以下三个 FQDN：

- **sip.pstnhub.microsoft.com** - 全局 FQDN - 必须首先尝试。 当 SBC 发送请求解析此名称时，Microsoft Azure DNS 服务器将返回一个 IP 地址，该 IP 地址指向分配给 SBC 的主要 Azure 数据中心。 该分配基于数据中心的性能指标以及与 SBC 的地理邻近度。 返回的 IP 地址对应于主要 FQDN。
- **sip2.pstnhub.microsoft.com** - 次要 FQDN - 映射到第二优先区域（按地理位置）。
- **sip3.pstnhub.microsoft.com** - 第三位 FQDN - 映射到第三优先区域（按地理位置）。

按顺序放置这三个 FQDN 旨在：

- 提供最佳体验（通过查询第一个 FQDN 来减少负载并且最接近分配的 SBC 数据中心）。
- 当从 SBC 建立到遇到临时问题的数据中心的连接时，提供故障转移。 有关详细信息，请参阅下面的[故障转移机制](#failover-mechanism-for-sip-signaling)。  

FQDN（sip.pstnhub.microsoft.com、sip2.pstnhub.microsoft.com 和 sip3.pstnhub.microsoft.com）将解析为以下 IP 地址之一：

- `52.112.0.0/14 (IP addresses from 52.112.0.1 to 52.115.255.254)`
- `52.120.0.0/14 (IP addresses from 52.120.0.1 to 52.123.255.254)`

为所有这些 IP 地址范围打开防火墙端口，以允许流量传入和传出用于发送信号的地址。 如果防火墙支持 DNS 名称，则 FQDN `sip-all.pstnhub.microsoft.com` 将解析为所有这些 IP 地址。 

## <a name="sip-signaling-ports"></a>SIP 信号：端口

将以下端口用于通信服务 Azure 直接路由：

|交通|从|功能|源端口|目标端口|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|SIP 代理|SBC|1024 - 65535|在 SBC 上定义（对于 Office 365 GCC High/DoD，仅必须使用端口 5061）|
SIP/TLS|SBC|SIP 代理|在 SBC 上定义|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>用于 SIP 信号的故障转移机制

SBC 发出 DNS 查询来解析 sip.pstnhub.microsoft.com。 系统将根据 SBC 位置和数据中心性能指标选择主数据中心。 如果主数据中心遇到问题，SBC 将尝试 sip2.pstnhub.microsoft.com，它将解析为分配的第二个数据中心，在两个区域的数据中心均不可用的罕见情况下，SBC 将重试最后一个 FQDN (sip3.pstnhub.microsoft.com)，该 FQDN 提供第三个数据中心 IP。

## <a name="media-traffic-ip-and-port-ranges"></a>媒体流量：IP 和端口范围

媒体流量进出于称为“媒体处理器”的单独服务。 在发布时，通信服务的媒体处理器可以使用任何 Azure IP 地址。 下载[完整地址列表](https://www.microsoft.com/download/details.aspx?id=56519)。

### <a name="port-range"></a>端口范围
下表显示了媒体处理器的端口范围： 

|交通|从|功能|源端口|目标端口|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|媒体处理器|SBC|3478-3481 和 49152-53247|在 SBC 上定义|
|UDP/SRTP|SBC|媒体处理器|在 SBC 上定义|3478-3481 和 49152-53247|

  > [!NOTE]
  > Microsoft 建议 SBC 上的每次并发调用至少有两个端口。


## <a name="media-traffic-media-processors-geography"></a>媒体流量：媒体处理器地域

媒体流量通过称为媒体处理器的组件实现流动。 媒体处理器与 SIP 代理位于相同的数据中心。 还有其他用于优化媒体流的媒体处理器。 例如，我们现在在澳大利亚没有 SIP 代理组件（SIP 流量通过新加坡或香港 SAR 流动），但是我们在澳大利亚本地有媒体处理器。 本地对媒体处理器的需求由我们远程发送流量（例如从澳大利亚到新加坡或香港 SAR）所经历的延迟来决定。 在流量从澳大利亚流向香港 SAR 或新加坡的示例中，虽然延迟对于 SIP 流量来说是可接受的（可保持良好呼叫质量），但对于实时媒体流量来说并非如此。

部署 SIP 代理和媒体处理器组件的位置：
- 美国（两个在美国西部和美国东部数据中心）
- 欧洲（阿姆斯特丹和都柏林数据中心）
- 亚洲（新加坡和香港 SAR 数据中心）
- 澳大利亚（澳大利亚东部和东南部数据中心）

仅部署媒体处理器的位置（SIP 通过上面列出的最近数据中心流动）：
- 日本（日本东部和西部数据中心）


## <a name="media-traffic-codecs"></a>媒体流量：编解码器

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>SBC 与云媒体处理器或 Microsoft Teams 客户端之间的分支。

会话边界控制器和云媒体处理器之间的分支上的 Azure 直接路由接口可以使用以下编解码器：

- SILK、G.711、G.722、G.729

通过从产品/服务中排除不需要的编解码器，可以强制在会话边框控制器上使用特定编解码器。

### <a name="leg-between-communication-services-calling-sdk-app-and-cloud-media-processor"></a>通信服务调用 SDK 应用与云媒体处理器之间的分支

在云媒体处理器与通信服务调用 SDK 应用之间的分支上，使用 G.722。 Microsoft 将在此分支上添加更多的编解码器。 

## <a name="supported-session-border-controllers-sbcs"></a>支持的会话边界控制器 (SBC)

- [为 Azure 通信服务直接路由认证的会话边界控制器](./certified-session-border-controllers.md)

## <a name="next-steps"></a>后续步骤

### <a name="conceptual-documentation"></a>概念文档

- [电话服务概念](./telephony-concept.md)
- [Azure 通信服务中的电话号码类型](./plan-solution.md)
- [配对会话边界控制器并配置语音路由](./direct-routing-provisioning.md)
- [定价](../pricing.md)

### <a name="quickstarts"></a>快速入门

- [拨打电话](../../quickstarts/voice-video-calling/pstn-call.md)