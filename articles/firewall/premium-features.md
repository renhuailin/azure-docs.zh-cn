---
title: Azure 防火墙高级预览版功能
description: Azure 防火墙高级版是一种托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: e823e1efc66592e9f48b7ff5e53a176a4e8cb514
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549580"
---
# <a name="azure-firewall-premium-preview-features"></a>Azure 防火墙高级预览版功能

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA 认证徽标" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI 认证徽标" border="false":::


> [!IMPORTANT]
> Azure 防火墙高级版目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

 Azure 防火墙高级预览版是下一代防火墙，其中包含高度敏感和管控环境所需的功能。

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Azure 防火墙高级概述示意图":::

Azure 防火墙高级预览版使用防火墙策略，这是一个全局资源，可用于使用 Azure 防火墙管理器集中管理防火墙。 从此版本开始，所有新功能仅可通过防火墙策略进行配置。 防火墙规则 (经典) 继续受支持，并可用于配置现有标准防火墙功能。  防火墙策略可以独立管理，也可以通过 Azure 防火墙管理器进行管理。 与单一防火墙关联的防火墙策略不收取任何费用。

> [!IMPORTANT]
> 目前，在安全中心部署和强制隧道配置中不支持防火墙高级 SKU。 

Azure 防火墙高级预览版包括以下功能：

- **TLS 检查** -解密出站流量，处理数据，然后加密数据并将数据发送到目标。
- **Idp** -网络入侵检测和防护系统 (idp) 使你可以监视恶意活动的网络活动，记录有关此活动的信息，报告它，还可以选择尝试阻止它。
- **URL 筛选** -扩展 Azure 防火墙的 FQDN 筛选功能，以考虑整个 URL。 例如， `www.contoso.com/a/c` 而不是 `www.contoso.com` 。
- **Web 类别** -管理员可以允许或拒绝用户访问网站类别，如赌博网站、社交媒体网站等。

## <a name="features"></a>功能

### <a name="tls-inspection"></a>TLS 检查

Azure 防火墙 Premium 终止出站和东-西 TLS 连接。 支持端到端加密的 [Azure 应用程序网关](../web-application-firewall/ag/ag-overview.md) 支持入站 TLS 检查。 Azure 防火墙将执行所需的增值安全功能，并重新加密发送到原始目标的流量。

> [!TIP]
> TLS 1.0 和1.1 将被弃用并且不受支持。 Tls 1.0 和1.1 版本的 TLS/安全套接字层 (SSL) 已发现易受攻击，尽管它们目前仍在允许向后兼容性，但不建议这样做。 尽快迁移到 TLS 1.2。

若要了解有关 Azure 防火墙高级预览版中间 CA 证书要求的详细信息，请参阅 [Azure 防火墙高级预览版证书](premium-certificates.md)。

### <a name="idps"></a>IDP

网络入侵检测和防护系统 (IDP) 使你可以监视网络中的恶意活动，记录有关此活动的信息，报告它，还可以选择尝试阻止它。 

通过查找特定模式（如网络流量中的字节序列），或恶意软件所使用的已知恶意指令序列，Azure 防火墙高级预览版提供基于签名的 IDP，以允许快速检测到攻击。 IDP 签名是完全托管的，并不断更新。

IDP 允许检测所有端口和协议中未加密流量的攻击。 但是，当需要检查 HTTPS 流量时，Azure 防火墙可以使用其 TLS 检查功能来解密流量并更好地检测恶意活动。  

通过 IDP 绕过列表，你可以不将流量筛选为绕过列表中指定的任何 IP 地址、范围和子网。  

### <a name="url-filtering"></a>URL 筛选

URL 筛选扩展了 Azure 防火墙的 FQDN 筛选功能，以考虑整个 URL。 例如， `www.contoso.com/a/c` 而不是 `www.contoso.com` 。  

URL 筛选可以应用于 HTTP 和 HTTPS 流量。 检查 HTTPS 通信时，Azure 防火墙高级预览版可以使用其 TLS 检查功能来解密流量，并提取目标 URL 以验证是否允许访问。 TLS 检查要求在应用程序规则级别选择加入。 启用后，可以使用 Url 通过 HTTPS 进行筛选。 

### <a name="web-categories"></a>Web 类别

通过 web 类别，管理员可以允许或拒绝用户访问网站类别，如赌博网站、社交媒体网站等。 Web 类别还将包含在 Azure 防火墙标准中，但在 Azure 防火墙高级预览版中，它将更清晰地进行优化。 与基于 FQDN 的类别匹配的标准 SKU 中的 Web 类别功能不同，高级 SKU 根据 HTTP 和 HTTPS 流量的整个 URL 来匹配类别。 

例如，如果 Azure 防火墙截获 HTTPS 请求 `www.google.com/news` ，则需要以下分类： 

- 防火墙标准–只会检查 FQDN 部分，因此会 `www.google.com` 分类为 *搜索引擎*。 

- 防火墙高级–将检查完整的 URL， `www.google.com/news` 并将其分类为 *新闻*。

根据 **责任** 严重性、 **高带宽**、 **业务使用** 情况、 **生产率损失**、 **常规冲浪** 和未 **分类** 来组织类别。

#### <a name="category-exceptions"></a>类别异常

您可以为 web 类别规则创建例外。 在规则集合组中创建一个具有较高优先级的单独的允许或拒绝规则集合。 例如，你可以配置允许 `www.linkedin.com` 优先级为100的规则集合，并使用拒绝优先级为200的 **社交网络** 的规则集合。 这会为预定义的 **社交网络** web 类别创建例外。 

## <a name="known-issues"></a>已知问题

Azure 防火墙高级预览版具有以下已知问题：

|问题  |说明  |缓解措施  |
|---------|---------|---------|
|仅 HTTPS 标准端口支持 TLS 检测|TLS 检查仅支持 HTTPS/443。 |无。 其他端口将在 GA 中受支持。|
|ESNI 支持 HTTPS 中的 FQDN 解析|HTTPS 握手不支持加密的 SNI。|目前只有 Firefox 通过自定义配置支持 ESNI。 建议的解决方法是禁用此功能。|
| (TLS 的客户端证书) |客户端证书用于在客户端与服务器之间建立相互的身份信任。 TLS 协商期间使用客户端证书。 Azure 防火墙会重新协商与服务器的连接，并且无权访问客户端证书的私钥。|无|
|QUIC/HTTP3|QUIC 是新的 HTTP 主版本。 它是基于 UDP 的协议，超过 80 (计划) 和 443 (SSL) 。 不支持 FQDN/URL/TLS 检查。|配置作为网络规则传递 UDP 80/443。|
|高级版中不支持安全中心和强制隧道|目前，在安全中心部署和强制隧道配置中不支持防火墙高级 SKU。|修补计划为 GA。|
不受信任的客户签名证书|从基于 intranet 的 web 服务器接收到的客户签名证书不受防火墙信任。|修补计划为 GA。
|带有 TLS 检查的 IDP 警报中的错误源和目标 IP 地址。|启用 TLS 检查并 IDP 发出新警报时，显示的源/目标 IP 地址错误 (显示的是内部 IP 地址，而不是) 的原始 IP 地址。|修补计划为 GA。|
|IDP for HTTP (的警报中的源 IP 地址错误，无 TLS 检查) 。|当使用纯文本 HTTP 流量时，IDP 会发出新警报，并且目标为公共 IP 地址，显示的源 IP 地址错误 (显示的是内部 IP 地址，而不是) 的原始 IP 地址。|修补计划为 GA。|
|证书传播|在防火墙上应用 CA 证书后，证书可能需要5-10 分钟才能生效。|修补计划为 GA。|
|绕过 IDP|IDP 旁路不适用于 TLS 终止的流量，并且不支持源 IP 地址和源 IP 组。|修补计划为 GA。|




## <a name="next-steps"></a>后续步骤

- [了解 Azure 防火墙高级证书](premium-certificates.md)
- [部署和配置 Azure 防火墙 Premium 预览版](premium-deploy.md)
- [迁移到 Azure 防火墙高级预览版](premium-migrate.md)
