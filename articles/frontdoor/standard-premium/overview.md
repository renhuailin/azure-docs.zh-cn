---
title: Azure Front Door 标准版/高级版 | Microsoft Docs
description: 本文提供 Azure Front Door 标准版/高级版的概述。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: acae0362d664567bb15b964f396f0c300135ed1c
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114726747"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>什么是 Azure Front Door 标准版/高级版（预览版）？

> [!IMPORTANT]
> 本文档适用于 Azure Front Door 标准版/高级版（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看 [Azure Front Door 文档](../front-door-overview.md)。

Azure Front Door 标准版/高级版是快速、可靠且安全的新式云 CDN，使用 Microsoft 全球边缘网络并与智能威胁防护相集成。 它将 Azure Front Door、Azure 内容分发网络 (CDN) 标准和 Azure Web 应用程序防火墙 (WAF) 的功能合并到了一个安全的云 CDN 平台。

借助 Azure Front Door 标准版/高级版，可将全球的消费型应用程序和企业应用程序转化成安全且高性能的个性化新式应用程序，其内容可在靠近用户的网络边缘处呈现给全球受众。 该服务还使应用程序能够无需预热即可横向扩展，同时可从全局 HTTP 负载均衡中受益，并支持即时故障转移。

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Azure Front Door 标准版/高级版体系结构" lightbox="../media/overview/front-door-overview-expanded.png":::

Azure Front Door 标准版/高级版在第 7 层（HTTP/HTTPS 层）工作，将任意广播协议与拆分 TCP 和 Microsoft 全球网络配合使用来改善全球连接。 根据使用规则集的自定义路由方法，可以确保 Azure Front Door 将客户端请求路由到最快且可用性最高的源位置。 应用程序源是托管在 Azure 内部或外部的任何面向 Internet 的服务。 Azure Front Door 标准版/高级版提供多种流量路由方法和源运行状况监视选项来满足不同应用程序需求和自动故障转移方案。 与流量管理器类似，Front Door 可以灵活应对故障，包括整个 Azure 区域的故障。

Azure Front Door 还通过集成的 Web 应用程序防火墙防护、机器人防护和内置的第 3 层/第 4 层分布式拒绝服务 (DDoS) 防护来保护边缘上的应用。 此外，它还通过专用链接服务保护专用后端。 Azure Front Door 可在全球范围内提供 Microsoft 在实践中表现最佳的安全性。  

>[!NOTE]
> Azure 为方案提供了一套完全托管的负载均衡解决方案。
>
> * 若要进行基于 DNS 的全局路由，并且在传输层安全性 (TLS) 协议终止（“SSL 卸载”）、每 HTTP/HTTPS 请求或应用程序层处理方面没有要求，请查看[流量管理器](../../traffic-manager/traffic-manager-overview.md)。
> * 若要在应用程序层对某个区域内的多个服务器实现负载均衡，请查看[应用程序网关](../../application-gateway/overview.md)
> * 若要执行网络层负载均衡，请查看[负载均衡器](../../load-balancer/load-balancer-overview.md)。
>
> 端到端场景可从结合所需的解决方案中受益。
> 有关 Azure 负载平衡选项的比较，请参阅 [Overview of load-balancing options in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview)（Azure 中的负载平衡选项概述）。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="why-use-azure-front-door-standardpremium-preview"></a>为何要使用 Azure Front Door 标准版/高级版（预览版）？

Azure Front Door 标准版/高级版提供单个统一平台，该平台采用内置的统包式安全集成以及简单且可预测的定价模型，能够满足动态和静态加速的需求。 使用 Front Door 还能定义、管理和监视应用的全球路由。

Azure Front Door 标准版/高级版（预览版）附带的重要功能：

- 使用 **[基于拆分 TCP](../front-door-routing-architecture.md#splittcp)** 的任意广播协议提供加速的应用程序性能。

- 智能 **[运行状况探测](concept-health-probes.md)** 可用于监视并在 **[源](concept-origin.md)** 之间实现负载均衡。

- 定义支持灵活域验证的 **[自定义域](how-to-add-custom-domain.md)** 。

- 使用集成的 **[Web 应用程序防火墙 (WAF)](../../web-application-firewall/afds/afds-overview.md)** 保证应用程序的安全。

- SSL 卸载和集成的 **[证书管理](how-to-configure-https-custom-domain.md)** 。

- 使用 **[专用链接](concept-private-link.md)** 保护源。  

- 可通过 **[规则集](concept-rule-set.md)** 自定义的流量路由和优化。

- 在一体式仪表板中查看 Front Door 和安全模式的 **[内置报告](how-to-reports.md)** 。

- 与 Azure 监视服务集成的 **[实时监视](how-to-monitor-metrics.md)** 和警报。

- 针对每个 Front Door 请求和失败的运行状况探测提供 **[日志记录](how-to-logs.md)** 。

- 原生支持端到端 IPv6 连接和 HTTP/2 协议。

## <a name="pricing"></a>定价

Azure Front Door 标准版/高级版有两个 SKU：“标准”和“高级”。 请参阅[层级比较](tier-comparison.md)。 有关定价信息，请参阅 [Front Door 定价](https://azure.microsoft.com/pricing/details/frontdoor/)。 

## <a name="whats-new"></a>新增功能

订阅 RSS 源，并在 [Azure 更新](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door)页上查看最新的 Azure Front Door 功能更新。

## <a name="next-steps"></a>后续步骤

* 了解如何[创建 Front Door](create-front-door-portal.md)。
