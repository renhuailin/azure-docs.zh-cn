---
title: Azure 防火墙功能
description: 了解 Azure 防火墙功能
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: victorh
ms.openlocfilehash: 364072c44791cb9b74d385dbc3f2688a3725d76c
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112033503"
---
# <a name="azure-firewall-features"></a>Azure 防火墙功能

[Azure 防火墙](overview.md)是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。

![防火墙概述](media/overview/firewall-threat.png)

Azure 防火墙包括以下功能：

- 内置的高可用性
- 可用性区域
- 不受限制的云可伸缩性
- 应用程序 FQDN 筛选规则
- 网络流量筛选规则
- FQDN 标记
- 服务标记
- 威胁情报
- 出站 SNAT 支持
- 入站 DNAT 支持
- 多个公共 IP 地址
- Azure Monitor 日志记录
- 强制隧道
- Web 类别（预览版）
- 认证

## <a name="built-in-high-availability"></a>内置的高可用性

内置高可用性，因此不需要部署额外的负载均衡器，也不需要进行任何配置。

## <a name="availability-zones"></a>可用性区域

在部署期间，可将 Azure 防火墙配置为跨多个可用性区域，以提高可用性。 使用可用性区域可将可用性提高到 99.99% 运行时间。 有关详细信息，请参阅 Azure 防火墙的[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)。 如果选择了两个或更多个可用性区域，则可以提供 99.99% 的运行时间 SLA。

还可以仅仅出于相互更靠近的原因，将 Azure 防火墙关联到特定的区域，并享用服务标准 99.95% SLA。

在可用性区域中部署的防火墙不会产生额外的费用。 但是，与可用性区域关联的入站和出站数据传输会产生额外的费用。 有关详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

在支持可用性区域的区域中可以使用 Azure 防火墙可用性区域。 有关详细信息，请参阅[在 Azure 中支持可用性区域的区域](../availability-zones/az-region.md)。

> [!NOTE]
> 只能在部署期间配置可用性区域。 无法将现有的防火墙配置为包含可用性区域。

有关可用性区域的详细信息，请参阅 [Azure 中的区域和可用性区域](../availability-zones/az-overview.md)。

## <a name="unrestricted-cloud-scalability"></a>不受限制的云可伸缩性

为了适应不断变化的网络流量，Azure 防火墙可最大程度地进行横向扩展，因此不需要你为峰值流量做出预算。

## <a name="application-fqdn-filtering-rules"></a>应用程序 FQDN 筛选规则

可将出站 HTTP/S 流量或 Azure SQL 流量限制到指定的一组完全限定的域名 (FQDN)（包括通配符）。 此功能不需要 TLS 终止。

## <a name="network-traffic-filtering-rules"></a>网络流量筛选规则

可以根据源和目标 IP 地址、端口和协议，集中创建“允许”或“拒绝”网络筛选规则。  Azure 防火墙是完全有状态的，因此它能区分不同类型的连接的合法数据包。 将跨多个订阅和虚拟网络实施与记录规则。

## <a name="fqdn-tags"></a>FQDN 标记

[FQDN 标记](fqdn-tags.md)使你可以轻松地允许已知的 Azure 服务网络流量通过防火墙。 例如，假设你想要允许 Windows 更新网络流量通过防火墙。 创建应用程序规则，并在其中包括 Windows 更新标记。 现在，来自 Windows 更新的网络流量将可以流经防火墙。

## <a name="service-tags"></a>服务标记

[服务标记](service-tags.md)表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。 无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

## <a name="threat-intelligence"></a>威胁情报

可以为防火墙启用基于[威胁智能](threat-intel.md)的筛选，以提醒和拒绝来自/到达已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

## <a name="outbound-snat-support"></a>出站 SNAT 支持

所有出站虚拟网络流量 IP 地址将转换为 Azure 防火墙公共 IP（源网络地址转换）。 可以识别源自你的虚拟网络的流量，并允许将其发往远程 Internet 目标。 如果目标 IP 是符合 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) 的专用 IP 范围，Azure 防火墙不会执行 SNAT。 

如果组织对专用网络使用公共 IP 地址范围，Azure 防火墙会通过 SNAT 将流量发送到 AzureFirewallSubnet 中的某个防火墙专用 IP 地址。 可以将 Azure 防火墙配置为 **不** SNAT 公共 IP 地址范围。 有关详细信息，请参阅 [Azure 防火墙 SNAT 专用 IP 地址范围](snat-private-range.md)。

可以在 Azure 防火墙指标中监视 SNAT 端口利用率。 在[防火墙日志和指标文档](logs-and-metrics.md#metrics)中了解详细信息并查看我们对 SNAT 端口利用率的建议。

## <a name="inbound-dnat-support"></a>入站 DNAT 支持

转换到防火墙公共 IP 地址的入站 Internet 网络流量（目标网络地址转换）并将其筛选到虚拟网络上的专用 IP 地址。

## <a name="multiple-public-ip-addresses"></a>多个公共 IP 地址

可将[多个公共 IP 地址](deploy-multi-public-ip-powershell.md)（最多 250 个）关联到防火墙。

这样可以实现以下方案：

- **DNAT** - 可将多个标准端口实例转换为后端服务器。 例如，如果你有两个公共 IP 地址，可以转换这两个 IP 地址的 TCP 端口 3389 (RDP)。
- SNAT - 更多端口可用于出站 SNAT 连接，以减少 SNAT 端口耗尽的可能性。 目前，Azure 防火墙会随机选择用于建立连接的源公共 IP 地址。 如果你在网络中进行任何下游筛选，则需要允许与防火墙关联的所有公共 IP 地址。 请考虑使用[公共 IP 地址前缀](../virtual-network/public-ip-address-prefix.md)来简化此配置。

## <a name="azure-monitor-logging"></a>Azure Monitor 日志记录

所有事件与 Azure Monitor 集成，使你能够在存储帐户中存档日志、将事件流式传输到事件中心，或者将其发送到 Azure Monitor 日志。 有关 Azure Monitor 日志示例，请参阅 [Azure 防火墙的 Azure Monitor 日志](./firewall-workbook.md)。

有关详细信息，请参阅[教程：监视 Azure 防火墙日志和指标](./firewall-diagnostics.md)。 

Azure 防火墙工作簿为 Azure 防火墙数据分析提供了一个灵活的画布。 该画布可用于在 Azure 门户中创建丰富的视觉对象报表。 有关详细信息，请参阅[使用 Azure 防火墙工作簿监视日志](firewall-workbook.md)。

## <a name="forced-tunneling"></a>强制隧道

你可以对 Azure 防火墙进行配置，使其将所有 Internet 绑定的流量路由到指定的下一跃点，而不是直接前往 Internet。 例如，你可能有一个本地边缘防火墙或其他网络虚拟设备 (NVA)，用于对网络流量进行处理，然后再将其传递到 Internet。 有关详细信息，请参阅 [Azure 防火墙强制隧道](forced-tunneling.md)。

## <a name="web-categories-preview"></a>Web 类别（预览版）

通过 Web 类别，管理员可以允许或拒绝用户访问某些类别的网站，例如赌博网站、社交媒体网站等。 Web 类别还包含在 Azure 防火墙标准版中，但在 Azure 防火墙高级预览版中，它会进行更多微调。 标准版 SKU 中的 Web 类别功能根据 FQDN 来匹配类别，而高级版 SKU 则根据 HTTP 和 HTTPS 流量的整个 URL 来匹配类别。 若要详细了解 Azure 防火墙高级预览版，请参阅 [Azure 防火墙高级预览版功能](premium-features.md)。

例如，如果 Azure 防火墙截获了 `www.google.com/news` 的 HTTPS 请求，则需要进行以下分类： 

- 防火墙标准版 – 只检查 FQDN 部分，因此 `www.google.com` 将被分类为搜索引擎。 

- 防火墙高级版 – 检查完整的 URL，因此 `www.google.com/news` 将被分类为新闻。

这些类别是根据“责任”、“高带宽”、“商业用途”、“生产力损失”、“常规上网”和“未分类”下的严重性来组织的。     

### <a name="categorization-change"></a>分类更改

你可以在下面两种情况下请求更改类别：

 - 认为 FQDN 或 URL 应采用不同的类别 
 
或 

- 对于未分类的 FQDN 或 URL，有建议的类别

欢迎通过 [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) 提交请求。

### <a name="category-exceptions"></a>类别例外

可以为 Web 类别规则创建例外。 在规则收集组中创建具有更高优先级的单独的允许或拒绝规则集合。 例如，你可以配置一个允许 `www.linkedin.com` 的优先级为 100 的规则集合和一个拒绝社交网络的优先级为 200 的规则集合。 这将为预定义的社交网络 Web 类别创建例外。



## <a name="certifications"></a>认证

Azure 防火墙符合支付卡行业 (PCI)、服务组织控制 (SOC)、国际标准化组织 (ISO) 和 ICSA 实验室标准。 有关详细信息，请参阅 [Azure 防火墙符合性认证](compliance-certifications.md)。

## <a name="next-steps"></a>后续步骤

- [Azure 防火墙高级预览版功能](premium-features.md)