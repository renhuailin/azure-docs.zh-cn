---
title: Azure 防火墙高级版功能
description: Azure 防火墙高级版是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 08/10/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: d1a46cd010f0a629e787a7ebabcfe002837b7ad3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811165"
---
# <a name="azure-firewall-premium-features"></a>Azure 防火墙高级版功能

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA 认证徽标" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI 认证徽标" border="false":::


 Azure 防火墙高级版是新一代防火墙，具备了高度敏感环境和受管制环境所需的功能。

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Azure 防火墙高级版概览图":::

Azure 防火墙高级版使用防火墙策略，这是一项可用来通过 Azure 防火墙管理器集中管理防火墙的全局资源。 从此版本开始，所有新功能都只能通过防火墙策略进行配置。 防火墙规则（经典）继续受支持，并可用于配置现有标准防火墙功能。  防火墙策略可以独立管理，也可以通过 Azure 防火墙管理器进行管理。 与单个防火墙关联的防火墙策略不会产生任何费用。

Azure 防火墙高级版包括以下功能：

- TLS 检查 - 解密出站流量、处理数据，然后加密数据并将数据发送到目的地。
- IDPS - 网络入侵检测和防护系统 (IDPS) 使你可以监视网络活动是否出现恶意活动，记录有关此活动的信息，予以报告，选择性地尝试阻止。
- URL 筛选 - 将 Azure 防火墙的 FQDN 筛选功能扩展到考虑整个 URL。 例如，`www.contoso.com/a/c`，而非 `www.contoso.com`。
- Web 类别 - 管理员可以允许或拒绝用户访问某些类别的网站，例如赌博网站、社交媒体网站等。


## <a name="tls-inspection"></a>TLS 检查

Azure 防火墙高级版终止出站和东-西 TLS 连接。 允许端到端加密的 [Azure 应用程序网关](../web-application-firewall/ag/ag-overview.md)支持入站 TLS 检查。 Azure 防火墙执行必需的增值安全功能，并重新加密发送到原始目的地的流量。

> [!TIP]
> TLS 1.0 和 1.1 将被弃用并且不再受支持。 TLS 1.0 和 1.1 版本的 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容的目的，这些协议仍可正常工作，但我们不建议使用。 请尽快迁移到 TLS 1.2。

若要详细了解 Azure 防火墙高级版中间 CA 证书要求，请参阅 [Azure 防火墙高级版证书](premium-certificates.md)。

## <a name="idps"></a>IDPS

网络入侵检测和防护系统 (IDPS) 使你可以监视网络是否出现恶意活动，记录有关此活动的信息，予以报告，选择性地尝试阻止。 

Azure 防火墙高级版提供基于签名的 IDPS，允许通过查找特定模式（例如网络流量中的字节序列或恶意软件使用的已知恶意指令序列）来快速检测攻击。 IDPS 签名同时适用于应用程序和网络级别流量（第 4-7 层），它们是完全托管的，并会持续更新。 IDPS 可应用于入站流量、分支到分支（东-西）流量和出站流量。

Azure 防火墙签名/规则集包括：
- 侧重于对实际恶意软件、命令和控制、攻击工具包以及传统防护方法遗漏的疯狂恶意活动进行指纹识别。
- 超过 55,000 条规则，类别超过 50 种。
    - 类别包括恶意软件命令和控制、DoS 攻击、僵尸网络、信息性事件、攻击、漏洞、SCADA 网络协议、攻击工具包活动等。
- 每天发布 20 到 40 个新规则。
- 使用最先进的恶意软件沙箱和全局传感器网络反馈循环，降低误报级别。

通过 IDPS，你可以检测所有端口和协议中对未加密流量的攻击。 但是，当需要检查 HTTPS 通信时，Azure 防火墙可以使用其 TLS 检查功能来解密通信并更好地检测恶意活动。  

通过 IDPS 绕过列表，你可以不将流量筛选到绕过列表中指定的任何 IP 地址、范围和子网。

将 IDPS 模式设置为“警报”时，也可使用签名规则，但是需要阻止一个或多个特定的签名，包括其关联的流量。 在这种情况下，可通过将 TLS 检查模式设置为“拒绝”来添加新的签名规则。


## <a name="url-filtering"></a>URL 筛选

URL 筛选将 Azure 防火墙的 FQDN 筛选功能扩展到考虑整个 URL。 例如，`www.contoso.com/a/c`，而非 `www.contoso.com`。  

URL 筛选可以应用于 HTTP 和 HTTPS 流量。 检查 HTTPS 通信时，Azure 防火墙高级版可以使用其 TLS 检查功能来解密流量，并提取目标 URL 以验证是否允许访问。 TLS 检查要求在应用程序规则级别选择加入。 启用后，可以使用 URL 筛选 HTTPS。 

## <a name="web-categories"></a>Web 类别

通过 Web 类别，管理员可以允许或拒绝用户访问某些类别的网站，例如赌博网站、社交媒体网站等。 Web 类别还将包含在 Azure 防火墙标准版中，但在 Azure 防火墙高级版中，它将进行更多微调。 标准版 SKU 中的 Web 类别功能根据 FQDN 来匹配类别，而高级版 SKU 则根据 HTTP 和 HTTPS 流量的整个 URL 来匹配类别。 

例如，如果 Azure 防火墙截获了 `www.google.com/news` 的 HTTPS 请求，则需要进行以下分类： 

- 防火墙标准版 – 只检查 FQDN 部分，因此 `www.google.com` 将被分类为搜索引擎。 

- 防火墙高级版 – 检查完整的 URL，因此 `www.google.com/news` 将被分类为新闻。

这些类别是根据“责任”、“高带宽”、“商业用途”、“生产力损失”、“常规上网”和“未分类”下的严重性来组织的。      有关 Web 类别的详细说明，请参阅 [Azure 防火墙 Web 类别](web-categories.md)。

### <a name="web-category-logging"></a>Web 类别日志记录
可以在应用程序日志中查看已按 Web 类别筛选的流量。 仅当在防火墙策略应用程序规则中显式配置了“Web 类别”字段时才会显示此字段。 例如，如果没有显式拒绝搜索引擎的规则，并且用户请求访问 www.bing.com，则只会显示默认的拒绝消息，而不会显示 Web 类别消息。 这是因为未显式配置 Web 类别。

### <a name="category-exceptions"></a>类别例外

可以为 Web 类别规则创建例外。 在规则收集组中创建具有更高优先级的单独的允许或拒绝规则集合。 例如，你可以配置一个允许 `www.linkedin.com` 的优先级为 100 的规则集合和一个拒绝社交网络的优先级为 200 的规则集合。 这将为预定义的社交网络 Web 类别创建例外。

 ## <a name="supported-regions"></a>支持的区域

以下区域支持 Azure 防火墙高级版：

- 澳大利亚中部（公共/澳大利亚）
- 澳大利亚中部 2（公共/澳大利亚）
- 澳大利亚东部（公共/澳大利亚）
- 澳大利亚东南部（公共/澳大利亚）
- 巴西南部（公共/巴西）
- 巴西东南部（公共/巴西）
- 加拿大中部（公共/加拿大）
- 加拿大东部（公共/加拿大）
- 印度中部（公共/印度）
- 美国中部（公共/美国）
- 美国中部 EUAP（公共/加那利（美国））
- 东亚（公共/亚太）
- 美国东部（公共/美国）
- 美国东部 2（公共/美国）
- 法国中部（公共/法国）
- 法国南部（公共/法国）
- 德国中西部（公共/德国）
- 日本东部（公共/日本）
- 日本西部（公共/日本）
- 韩国中部（公共/韩国）
- 韩国南部（公共/韩国）
- 美国中北部（公共/美国）
- 北欧（公共/欧洲）
- 挪威东部（公共/挪威）
- 南非北部（公共/南非）
- 美国中南部（公共/美国）
- 印度南部（公共/印度）
- 东南亚（公共/亚太）
- 瑞士北部（公共/瑞士）
- 阿拉伯联合酋长国中部（公共/阿拉伯联合酋长国）
- 阿拉伯联合酋长国北部（公共/阿拉伯联合酋长国）
- 英国南部（公共/英国）
- 英国西部（公共/英国）
- 美国中西部（公共/美国）
- 西欧（公共/欧洲）
- 印度西部（公共/印度）
- 美国西部（公共/美国）
- 美国西部 2（公共/美国）
- 美国西部 3（公共/美国）


## <a name="known-issues"></a>已知问题

Azure 防火墙高级版存在以下已知问题：


|问题  |说明  |缓解措施  |
|---------|---------|---------|
|ESNI 支持 HTTPS 中的 FQDN 解析|HTTPS 握手不支持加密的 SNI。|目前只有 Firefox 通过自定义配置支持 ESNI。 建议的解决方法是禁用此功能。|
|客户端证书 (TLS)|客户端证书用于在客户端与服务器之间建立相互的身份信任。 TLS 协商期间使用客户端证书。 Azure 防火墙会重新协商与服务器的连接，并且无权访问客户端证书的私钥。|无|
|QUIC/HTTP3|QUIC 是 HTTP 的新的主版本。 它是基于 UDP 的通过 80 (PLAN) 和 443 (SSL) 端口的协议。 将不支持 FQDN/URL/TLS 检查。|配置作为网络规则传递 UDP 80/443。|
不受信任的客户签名证书|一旦从基于 Intranet 的 Web 服务器接收客户签名的证书，防火墙将不信任它们。|我们正在研究修复措施。
|使用适于 HTTP 的 IDPS 的警报中源 IP 地址错误（未经 TLS 检查）。|当正在使用纯文本 HTTP 流量，IDPS 发出新的警报，并且目标是公共 IP 地址时，显示的源 IP 地址错误（显示内部 IP 地址，而不是原始 IP 地址）。|我们正在研究修复措施。|
|证书传播|将 CA 证书应用于防火墙后，证书可能需要 5 到 10 分钟生效。|我们正在研究修复措施。|
|TLS 1.3 支持|TLS 1.3 部分受支持。 从客户端到防火墙的 TLS 隧道基于 TLS 1.2，从防火墙到外部 Web 服务器的 TLS 隧道基于 TLS 1.3。|正在调查更新。|
|KeyVault 专用终结点|KeyVault 支持专用终结点访问以限制其网络曝光。 如果按照 [KeyVault 文档](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)中的说明配置了例外，则受信任的 Azure 服务可以绕过此限制。 Azure 防火墙当前未列为受信任服务，因此无法访问 Key Vault。|我们正在研究修复措施。|

## <a name="next-steps"></a>后续步骤

- [了解 Azure 防火墙高级版证书](premium-certificates.md)
- [部署和配置 Azure 防火墙高级版](premium-deploy.md)
- [迁移到 Azure 防火墙高级版](premium-migrate.md)
