---
title: Azure 前门-DDoS 防护
description: 本页提供有关 Azure 前门如何帮助防范 DDoS 攻击的信息
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350777"
---
# <a name="ddos-protection-on-front-door"></a>前门防护

Azure 前门提供多种功能和特征，有助于防止分布式拒绝服务 (DDoS) 攻击。 这些功能可防止攻击者访问应用程序，并影响应用程序的可用性和性能。

## <a name="integration-with-azure-ddos-protection-basic"></a>与 Azure DDoS 保护基本集成

前门受 Azure DDoS 保护基本保护。 默认情况下，它集成到了前门平台，无需额外付费。 前端的全球部署网络的完整规模和容量可通过 always on 流量监视和实时缓解功能，抵御常见网络层攻击。 基本 DDoS 防护还可以抵御最常见的、频繁出现的第7层 DNS 查询泛滥以及面向公共终结点的第3层和第4容量耗尽攻击。 此服务还提供了一个行之有效的跟踪记录，可保护 Microsoft 的企业和消费者服务免受大规模攻击。 有关详细信息，请参阅 [Azure DDoS 保护](../security/fundamentals/ddos-best-practices.md)。

## <a name="protocol-blocking"></a>协议阻止

前门仅接受 HTTP 和 HTTPS 协议上的流量，并且仅处理具有已知标头的有效请求 `Host` 。 此行为有助于缓解一些常见的 DDoS 攻击类型，包括传播到各种协议和端口、DNS 放大攻击和 TCP 中毒攻击的容量耗尽攻击。

## <a name="capacity-absorption"></a>全部容量

前门是一种高度可扩展的全球分布式服务。 我们有许多客户，其中包括 Microsoft 自己的大规模云产品，每秒接收数百个请求。 前门位于 Azure 网络的边缘，吸收，并在地理上隔离大容量攻击。 这可以防止恶意流量进一步超出 Azure 网络的边缘。

## <a name="caching"></a>缓存

[前门的缓存功能](./front-door-caching.md) 可用于保护后端免受攻击生成的大型流量量。 缓存的资源将从前门边缘节点返回，因此不会转发到后端。 即使短暂的缓存过期时间 (秒或分钟数) 动态响应可大大减少后端服务上的负载。 有关缓存概念和模式的详细信息，请参阅 [缓存注意事项](/azure/architecture/best-practices/caching) 和 [缓存端模式](/azure/architecture/patterns/cache-aside)。

## <a name="web-application-firewall-waf"></a>Web 应用程序防火墙 (WAF)

[前门的 Web 应用程序防火墙 (WAF) ](../web-application-firewall/afds/afds-overview.md) 可用于缓解多种不同类型的攻击：

* 使用托管规则集可针对多种常见攻击提供保护。
* 来自定义的地理区域外或已定义区域内的流量可被阻止或重定向到静态网页。 有关详细信息，请参阅 [异地筛选](../web-application-firewall/afds/waf-front-door-geo-filtering.md)。
* 可识别为恶意的 IP 地址和范围可能会被阻止。
* 可以应用速率限制，以防止 IP 地址过于频繁地调用服务。
* 你可以创建 [自定义 WAF 规则](../web-application-firewall/afds/waf-front-door-custom-rules.md) 以自动阻止和速率限制具有已知签名的 HTTP 或 HTTPS 攻击。

## <a name="for-further-protection"></a>为了进一步保护

如果需要进一步保护，则可以在部署后端的 VNet 中启用 [Azure DDoS 保护标准](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) 。 DDoS 保护标准客户可获得更多好处，包括从 DDoS 快速响应团队获得的对专家的成本保护、SLA 保证和访问权限，以便在攻击期间立即获得帮助。

## <a name="next-steps"></a>后续步骤

- 了解如何 [在前门上配置 WAF 配置文件](front-door-waf.md)。 
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。