---
title: Azure 前门： DDoS 保护
description: 本页提供有关 Azure 前门标准/高级版如何帮助防范 DDoS 攻击的信息
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098773"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>Azure 前门标准/高级 (预览版的 DDoS 保护) 

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

Azure 前门提供多种功能和特征，有助于防止分布式拒绝服务 (DDoS) 攻击。 这些功能可防止攻击者访问应用程序，并影响应用程序的可用性和性能。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="integration-with-azure-ddos-protection-basic"></a>与 Azure DDoS 保护基本集成

前门受 Azure DDoS 保护基本保护。 默认情况下，该功能已集成到前门平台中，无需额外付费。 前端的全球部署网络的完整规模和容量可通过 always on 流量监视和实时缓解功能，抵御常见网络层攻击。 基本 DDoS 防护还可以抵御最常见的、频繁出现的第7层 DNS 查询泛滥以及面向公共终结点的第3层和第4容量耗尽攻击。 此服务还提供了一个行之有效的跟踪记录，可保护 Microsoft 的企业和消费者服务免受大规模攻击。 有关详细信息，请参阅 [Azure DDoS 保护](../../security/fundamentals/ddos-best-practices.md)。

## <a name="protocol-blocking"></a>协议阻止

前门仅接受 HTTP 和 HTTPS 协议上的流量，并且仅处理具有已知标头的有效请求 `Host` 。 此行为有助于缓解一些常见的 DDoS 攻击类型，包括容量耗尽的攻击，这些攻击跨一系列的协议和端口、DNS 放大攻击和 TCP 中毒攻击传播。

## <a name="capacity-absorption"></a>全部容量

前门是一种高度可扩展的全球分布式服务。 我们有许多客户，其中包括 Microsoft 自己的大规模云产品，每秒接收数百个请求。 前门位于 Azure 网络、吸收和地理位置隔离大容量攻击的边缘。 这可以防止恶意流量进一步超出 Azure 网络的边缘。

## <a name="caching"></a>Caching

[前门的缓存功能](concept-caching.md) 可用于保护后端免受攻击生成的大型流量量。 缓存的资源将从前门边缘节点返回，因此不会转发到后端。 即使短暂的缓存过期时间 (秒或分钟数) 动态响应可大大减少后端服务上的负载。 有关缓存概念和模式的详细信息，请参阅 [缓存注意事项](/azure/architecture/best-practices/caching) 和 [缓存端模式](/azure/architecture/patterns/cache-aside)。

## <a name="web-application-firewall-waf"></a>Web 应用程序防火墙 (WAF)

[前门的 Web 应用程序防火墙 (WAF) ](../../web-application-firewall/afds/afds-overview.md) 可用于缓解多种不同类型的攻击：

* 使用托管规则集可针对许多常见攻击提供保护。
* 来自定义的地理区域外或已定义区域内的流量可被阻止或重定向到静态网页。 有关详细信息，请参阅 [异地筛选](../../web-application-firewall/afds/waf-front-door-geo-filtering.md)。
* 可识别为恶意的 IP 地址和范围可能会被阻止。
* 可以应用速率限制，以防止 IP 地址过于频繁地调用服务。
* 你可以创建 [自定义 WAF 规则](../../web-application-firewall/afds/waf-front-door-custom-rules.md) 以自动阻止和速率限制具有已知签名的 HTTP 或 HTTPS 攻击。

## <a name="for-further-protection"></a>为了进一步保护

如果需要进一步保护，则可以在部署后端的 VNet 中启用 [Azure DDoS 保护标准](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) 。 DDoS 保护标准客户获得更多好处，包括：

* 成本保护
* SLA 保证
* 从 DDoS 快速响应团队访问专家，在攻击期间立即获得帮助。

## <a name="next-steps"></a>后续步骤

了解如何 [创建前门标准/高级版](create-front-door-portal.md)。
