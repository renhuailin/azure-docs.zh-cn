---
title: Azure Front Door：DDoS 防护
description: 此页面提供有关 Azure Front Door 标准版/高级版如何帮助防范 DDoS 攻击的信息
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098773"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>有关 Azure Front Door 标准版/高级版（预览版）的DDoS 防护

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

Azure Front Door 具有多种功能和特征，有助于防止分布式拒绝服务 (DDoS) 攻击。 这些功能可防止攻击者访问应用程序和影响应用程序的可用性与性能。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="integration-with-azure-ddos-protection-basic"></a>与 Azure DDoS 防护基础版集成

Front Door 受 Azure DDoS 防护基础版保护。 该功能默认集成在 Front Door 平台中，无需额外费用。 Front Door 具有全球部署的网络，其完整的规模和容量通过始终开启的流量监视和实时缓解措施来提供对常见网络层攻击的防御。 基本 DDoS 防护还可以抵御最常见的、频繁出现的第 7 层 DNS 查询泛滥以及针对公共终结点的第 3 层和第 4 层容量耗尽攻击。 此服务还提供了有效的跟踪记录，用于保护 Microsoft 的企业和消费者服务免受大规模攻击。 有关详细信息，请参阅 [Azure DDoS 防护](../../security/fundamentals/ddos-best-practices.md)。

## <a name="protocol-blocking"></a>协议阻止

Front Door 只接受 HTTP 和 HTTPS 协议上的流量，并且将只处理具有已知 `Host` 标头的有效请求。 此行为有助于缓解一些常见的 DDoS 攻击类型，包括在各种协议和端口间传播的容量耗尽攻击、DNS 放大攻击和 TCP 中毒攻击。

## <a name="capacity-absorption"></a>容量吸收

Front Door 是高度缩放的全球分布型服务。 我们有许多客户，其中包括 Microsoft 自己的大规模云产品，每秒接收成千上万个请求。 Front Door 位于 Azure 网络的边缘，吸收并从地理位置上隔离大容量攻击。 这样就可以防止恶意流量进一步超过 Azure 网络的边缘。

## <a name="caching"></a>Caching

[Front Door 的缓存功能](concept-caching.md)可用于保护后端避开攻击生成的大规模流量。 缓存的资源将从 Front Door 边缘节点返回，因此不会被转发到后端。 即使动态响应的缓存过期时间很短（几秒或几分钟），也可以大幅减少后端服务上的负载。 有关缓存概念和模式的详细信息，请参阅[缓存注意事项](/azure/architecture/best-practices/caching)和[缓存端模式](/azure/architecture/patterns/cache-aside)。

## <a name="web-application-firewall-waf"></a>Web 应用程序防火墙 (WAF)

[Front Door 的 Web 应用程序防火墙 (WAF)](../../web-application-firewall/afds/afds-overview.md) 可用于缓解许多不同类型的攻击：

* 使用托管规则集可针对许多常见攻击提供保护。
* 可以阻止来自某个定义的地理区域之外的或在某个定义的区域之内的流量，或者将其重定向到某个静态网页。 有关详细信息，请参阅[地区筛选](../../web-application-firewall/afds/waf-front-door-geo-filtering.md)。
* 可以阻止标识为恶意的 IP 地址和范围。
* 可以应用速率限制来防止 IP 地址过于频繁地调用服务。
* 可以创建[自定义 WAF 规则](../../web-application-firewall/afds/waf-front-door-custom-rules.md)来自动阻止具有已知签名的 HTTP 或 HTTPS 攻击并对这些攻击进行速率限制。

## <a name="for-further-protection"></a>如需进一步保护

如果需要进一步保护，可在部署了后端的 VNet 上启用 [Azure DDoS 防护标准](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard)。 DDoS 防护标准版客户将获得更多权益，包括：

* 成本保护
* SLA 保证
* 可访问 DDoS 快速响应团队的专家，在攻击期间立即获得帮助。

## <a name="next-steps"></a>后续步骤

了解如何[创建 Front Door Standard/Premium](create-front-door-portal.md)。
